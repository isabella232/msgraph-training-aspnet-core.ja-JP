---
ms.openlocfilehash: 6e6c476b4ff0901f50d8e35a17f584d73b48b533
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822473"
---
<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph API を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。 この手順では、 [Microsoft Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) ライブラリを構成します。

> [!IMPORTANT]
> アプリケーション ID とシークレットをソースに格納しないようにするには、 [.Net シークレットマネージャー](/aspnet/core/security/app-secrets) を使用してこれらの値を格納します。 シークレットマネージャーは開発のみを目的としており、運用アプリでは、信頼されたシークレットマネージャーを使用して機密情報を格納する必要があります。

1. **/appsettings.jsを** 開き、その内容を次のように置き換えます。

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. **Graphtutorial .csproj** が配置されているディレクトリで CLI を開き、次のコマンドを実行します。これには、 `YOUR_APP_ID` Azure PORTAL からアプリケーション ID を使用し、アプリケーションシークレットを使用し `YOUR_APP_SECRET` ます。

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a>サインインの実装

最初に、Microsoft Identity platform サービスをアプリケーションに追加します。

1. **GraphConstants.cs** という名前の新しいファイルを作成し、次のコードを追加し **ます。**

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. **./Startup.cs** ファイルを開き、次の `using` ステートメントをファイルの先頭に追加します。

    ```csharp
    using Microsoft.AspNetCore.Authentication.OpenIdConnect;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc.Authorization;
    using Microsoft.Identity.Web;
    using Microsoft.Identity.Web.UI;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.Graph;
    using System.Net;
    using System.Net.Http.Headers;
    ```

1. 既存の `ConfigureServices` 関数を、以下の関数で置き換えます。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services
            // Use OpenId authentication
            .AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
            // Specify this is a web app and needs auth code flow
            .AddMicrosoftIdentityWebApp(Configuration)
            // Add ability to call web API (Graph)
            // and get access tokens
            .EnableTokenAcquisitionToCallDownstreamApi(options => {
                Configuration.Bind("AzureAd", options);
            }, GraphConstants.Scopes)
            // Use in-memory token cache
            // See https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization
            .AddInMemoryTokenCaches();

        // Require authentication
        services.AddControllersWithViews(options =>
        {
            var policy = new AuthorizationPolicyBuilder()
                .RequireAuthenticatedUser()
                .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
        })
        // Add the Microsoft Identity UI pages for signin/out
        .AddMicrosoftIdentityUI();
    }
    ```

1. 関数で `Configure` 、行の上に次の行を追加し `app.UseAuthorization();` ます。

    ```csharp
    app.UseAuthentication();
    ```

1. **/Controllers/HomeController.cs** を開き、その内容を次のように置き換えます。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using System.Diagnostics;
    using System.Threading.Tasks;

    namespace GraphTutorial.Controllers
    {
        public class HomeController : Controller
        {
            ITokenAcquisition _tokenAcquisition;
            private readonly ILogger<HomeController> _logger;

            // Get the ITokenAcquisition interface via
            // dependency injection
            public HomeController(
                ITokenAcquisition tokenAcquisition,
                ILogger<HomeController> logger)
            {
                _tokenAcquisition = tokenAcquisition;
                _logger = logger;
            }

            public async Task<IActionResult> Index()
            {
                // TEMPORARY
                // Get the token and display it
                try
                {
                    string token = await _tokenAcquisition
                        .GetAccessTokenForUserAsync(GraphConstants.Scopes);
                    return View().WithInfo("Token acquired", token);
                }
                catch (MicrosoftIdentityWebChallengeUserException)
                {
                    return Challenge();
                }
            }

            public IActionResult Privacy()
            {
                return View();
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            public IActionResult Error()
            {
                return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            [AllowAnonymous]
            public IActionResult ErrorWithMessage(string message, string debug)
            {
                return View("Index").WithError(message, debug);
            }
        }
    }
    ```

1. 変更を保存してプロジェクトを開始します。 Microsoft アカウントを使用してログインします。

1. 同意プロンプトを調べます。 アクセス許可の一覧は、/Graph/GraphConstants.cs で構成されているアクセス許可スコープの一覧に対応します **。**

    - **アクセス権が付与されているデータへのアクセスを保持する:** ( `offline_access` ) 更新トークンを取得するために、このアクセス許可は msal によって要求されます。
    - **サインインしてプロファイルを読み取る:** ( `User.Read` ) このアクセス許可により、アプリはログインしているユーザーのプロファイルとプロファイル写真を取得できます。
    - **メールボックスの設定の読み取り:** ( `MailboxSettings.Read` ) このアクセス許可により、アプリは、タイムゾーンや時刻の形式など、ユーザーのメールボックス設定を読み取ることができます。
    - **予定表へのフルアクセス:** ( `Calendars.ReadWrite` ) この権限によって、アプリはユーザーの予定表にあるイベントの読み取り、新しいイベントの追加、および既存のイベントの変更を行うことができます。

    ![Microsoft identity platform 同意プロンプトのスクリーンショット](./images/add-aad-auth-03.png)

    同意に関する詳細については、「 [AZURE AD アプリケーションの同意エクスペリエンス](/azure/active-directory/develop/application-consent-experience)について」を参照してください。

1. 要求されたアクセス許可への同意。 ブラウザーがアプリにリダイレクトし、トークンが表示されます。

### <a name="get-user-details"></a>ユーザーの詳細情報を取得する

ユーザーがログインすると、Microsoft Graph からそのユーザーの情報を入手できます。

1. /Graph/GraphClaimsPrincipalExtensions.cs を開き、内容全体を次のように置き換えます **。**

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. **Startup.cs** を開き、既存の行を `.AddMicrosoftIdentityWebApp(Configuration)` 次のコードに置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    このコードの内容を検討してください。

    - イベントのイベントハンドラーを追加し `OnTokenValidated` ます。
        - インターフェイスを使用して `ITokenAcquisition` アクセストークンを取得します。
        - Microsoft Graph を呼び出して、ユーザーのプロファイルと写真を取得します。
        - ユーザーの id にグラフ情報を追加します。

1. 呼び出しの後、呼び出しの前に、次の関数呼び出しを追加し `EnableTokenAcquisitionToCallDownstreamApi` `AddInMemoryTokenCaches` ます。

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    これにより、認証された **Graphserviceclient** 備えた、コントローラーが依存関係の挿入を介して使用できるようになります。

1. **/Controllers/HomeController.cs** を開き、関数を `Index` 次のように置き換えます。

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. `ITokenAcquisition` **HomeController** クラス内のすべての参照を削除します。

1. 変更を保存し、アプリを開始して、サインインプロセスを実行します。 ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. 右上隅にあるユーザーアバターをクリックして、[ **サインアウト** ] リンクにアクセスします。 **[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>トークンの格納と更新

この時点で、アプリケーションには、API 呼び出しのヘッダーで送信されるアクセストークンがあり `Authorization` ます。 これは、アプリが Microsoft Graph にユーザーの代わりにアクセスできるようにするトークンです。

ただし、このトークンは一時的なものです。 トークンが発行された後、有効期限が切れる時間になります。 ここで、更新トークンが役に立ちます。 更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。

アプリは、Microsoft Identity Web ライブラリを使用しているため、トークンストレージを実装したり、ロジックを更新したりする必要はありません。

アプリでは、アプリの再起動時にトークンを保持する必要がないアプリに十分なメモリ内トークンキャッシュを使用します。 代わりに、運用アプリは、Microsoft Identity. Web ライブラリの [分散キャッシュオプション](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) を使用する場合があります。

この `GetAccessTokenForUserAsync` メソッドは、トークンの有効期限を処理し、更新します。 最初に、キャッシュされたトークンをチェックし、有効期限が切れていない場合はそれを返します。 有効期限が切れている場合は、キャッシュされた更新トークンを使用して新しいものを取得します。

コントローラーが依存関係の挿入によって取得する **Graphserviceclient** ユーザーは、ユーザーが使用する認証プロバイダーで事前に構成され `GetAccessTokenForUserAsync` ます。

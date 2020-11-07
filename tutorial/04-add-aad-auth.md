---
ms.openlocfilehash: 6e6c476b4ff0901f50d8e35a17f584d73b48b533
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822473"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e68cf-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="e68cf-102">これは、Microsoft Graph API を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="e68cf-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="e68cf-103">この手順では、 [Microsoft Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) ライブラリを構成します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-103">In this step you will configure the [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) library.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e68cf-104">アプリケーション ID とシークレットをソースに格納しないようにするには、 [.Net シークレットマネージャー](/aspnet/core/security/app-secrets) を使用してこれらの値を格納します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-104">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="e68cf-105">シークレットマネージャーは開発のみを目的としており、運用アプリでは、信頼されたシークレットマネージャーを使用して機密情報を格納する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e68cf-105">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

1. <span data-ttu-id="e68cf-106">**/appsettings.jsを** 開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-106">Open **./appsettings.json** and replace its contents with the following.</span></span>

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. <span data-ttu-id="e68cf-107">**Graphtutorial .csproj** が配置されているディレクトリで CLI を開き、次のコマンドを実行します。これには、 `YOUR_APP_ID` Azure PORTAL からアプリケーション ID を使用し、アプリケーションシークレットを使用し `YOUR_APP_SECRET` ます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-107">Open your CLI in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="e68cf-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="e68cf-108">Implement sign-in</span></span>

<span data-ttu-id="e68cf-109">最初に、Microsoft Identity platform サービスをアプリケーションに追加します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-109">Start by adding the Microsoft Identity platform services to the application.</span></span>

1. <span data-ttu-id="e68cf-110">**GraphConstants.cs** という名前の新しいファイルを作成し、次のコードを追加し **ます。**</span><span class="sxs-lookup"><span data-stu-id="e68cf-110">Create a new file named **GraphConstants.cs** in the **./Graph** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. <span data-ttu-id="e68cf-111">**./Startup.cs** ファイルを開き、次の `using` ステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-111">Open the **./Startup.cs** file and add the following `using` statements to the top of the file.</span></span>

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

1. <span data-ttu-id="e68cf-112">既存の `ConfigureServices` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-112">Replace the existing `ConfigureServices` function with the following.</span></span>

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

1. <span data-ttu-id="e68cf-113">関数で `Configure` 、行の上に次の行を追加し `app.UseAuthorization();` ます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-113">In the `Configure` function, add the following line above the `app.UseAuthorization();` line.</span></span>

    ```csharp
    app.UseAuthentication();
    ```

1. <span data-ttu-id="e68cf-114">**/Controllers/HomeController.cs** を開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-114">Open **./Controllers/HomeController.cs** and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="e68cf-115">変更を保存してプロジェクトを開始します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-115">Save your changes and start the project.</span></span> <span data-ttu-id="e68cf-116">Microsoft アカウントを使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="e68cf-116">Login with your Microsoft account.</span></span>

1. <span data-ttu-id="e68cf-117">同意プロンプトを調べます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-117">Examine the consent prompt.</span></span> <span data-ttu-id="e68cf-118">アクセス許可の一覧は、/Graph/GraphConstants.cs で構成されているアクセス許可スコープの一覧に対応します **。**</span><span class="sxs-lookup"><span data-stu-id="e68cf-118">The list of permissions correspond to list of permissions scopes configured in **./Graph/GraphConstants.cs**.</span></span>

    - <span data-ttu-id="e68cf-119">**アクセス権が付与されているデータへのアクセスを保持する:** ( `offline_access` ) 更新トークンを取得するために、このアクセス許可は msal によって要求されます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-119">**Maintain access to data you have given it access to:** (`offline_access`) This permission is requested by MSAL in order to retrieve refresh tokens.</span></span>
    - <span data-ttu-id="e68cf-120">**サインインしてプロファイルを読み取る:** ( `User.Read` ) このアクセス許可により、アプリはログインしているユーザーのプロファイルとプロファイル写真を取得できます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-120">**Sign you in and read your profile:** (`User.Read`) This permission allows the app to get the logged-in user's profile and profile photo.</span></span>
    - <span data-ttu-id="e68cf-121">**メールボックスの設定の読み取り:** ( `MailboxSettings.Read` ) このアクセス許可により、アプリは、タイムゾーンや時刻の形式など、ユーザーのメールボックス設定を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-121">**Read your mailbox settings:** (`MailboxSettings.Read`) This permission allows the app to read the user's mailbox settings, including time zone and time format.</span></span>
    - <span data-ttu-id="e68cf-122">**予定表へのフルアクセス:** ( `Calendars.ReadWrite` ) この権限によって、アプリはユーザーの予定表にあるイベントの読み取り、新しいイベントの追加、および既存のイベントの変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-122">**Have full access to your calendars:** (`Calendars.ReadWrite`) This permission allows the app to read events on the user's calendar, add new events, and modify existing ones.</span></span>

    ![Microsoft identity platform 同意プロンプトのスクリーンショット](./images/add-aad-auth-03.png)

    <span data-ttu-id="e68cf-124">同意に関する詳細については、「 [AZURE AD アプリケーションの同意エクスペリエンス](/azure/active-directory/develop/application-consent-experience)について」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e68cf-124">For more information regarding consent, see [Understanding Azure AD application consent experiences](/azure/active-directory/develop/application-consent-experience).</span></span>

1. <span data-ttu-id="e68cf-125">要求されたアクセス許可への同意。</span><span class="sxs-lookup"><span data-stu-id="e68cf-125">Consent to the requested permissions.</span></span> <span data-ttu-id="e68cf-126">ブラウザーがアプリにリダイレクトし、トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="e68cf-127">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="e68cf-127">Get user details</span></span>

<span data-ttu-id="e68cf-128">ユーザーがログインすると、Microsoft Graph からそのユーザーの情報を入手できます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-128">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="e68cf-129">/Graph/GraphClaimsPrincipalExtensions.cs を開き、内容全体を次のように置き換えます **。**</span><span class="sxs-lookup"><span data-stu-id="e68cf-129">Open **./Graph/GraphClaimsPrincipalExtensions.cs** and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. <span data-ttu-id="e68cf-130">**Startup.cs** を開き、既存の行を `.AddMicrosoftIdentityWebApp(Configuration)` 次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-130">Open **./Startup.cs** and replace the existing `.AddMicrosoftIdentityWebApp(Configuration)` line with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    <span data-ttu-id="e68cf-131">このコードの内容を検討してください。</span><span class="sxs-lookup"><span data-stu-id="e68cf-131">Consider what this code does.</span></span>

    - <span data-ttu-id="e68cf-132">イベントのイベントハンドラーを追加し `OnTokenValidated` ます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-132">It adds an event handler for the `OnTokenValidated` event.</span></span>
        - <span data-ttu-id="e68cf-133">インターフェイスを使用して `ITokenAcquisition` アクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-133">It uses the `ITokenAcquisition` interface to get an access token.</span></span>
        - <span data-ttu-id="e68cf-134">Microsoft Graph を呼び出して、ユーザーのプロファイルと写真を取得します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-134">It calls Microsoft Graph to get the user's profile and photo.</span></span>
        - <span data-ttu-id="e68cf-135">ユーザーの id にグラフ情報を追加します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-135">It adds the Graph information to the user's identity.</span></span>

1. <span data-ttu-id="e68cf-136">呼び出しの後、呼び出しの前に、次の関数呼び出しを追加し `EnableTokenAcquisitionToCallDownstreamApi` `AddInMemoryTokenCaches` ます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-136">Add the following function call after the `EnableTokenAcquisitionToCallDownstreamApi` call and before the `AddInMemoryTokenCaches` call.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    <span data-ttu-id="e68cf-137">これにより、認証された **Graphserviceclient** 備えた、コントローラーが依存関係の挿入を介して使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e68cf-137">This will make an authenticated **GraphServiceClient** available to controllers via dependency injection.</span></span>

1. <span data-ttu-id="e68cf-138">**/Controllers/HomeController.cs** を開き、関数を `Index` 次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-138">Open **./Controllers/HomeController.cs** and replace the `Index` function with the following.</span></span>

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. <span data-ttu-id="e68cf-139">`ITokenAcquisition` **HomeController** クラス内のすべての参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-139">Remove all references to `ITokenAcquisition` in the **HomeController** class.</span></span>

1. <span data-ttu-id="e68cf-140">変更を保存し、アプリを開始して、サインインプロセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-140">Save your changes, start the app, and go through the sign-in process.</span></span> <span data-ttu-id="e68cf-141">ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e68cf-141">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. <span data-ttu-id="e68cf-143">右上隅にあるユーザーアバターをクリックして、[ **サインアウト** ] リンクにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="e68cf-143">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="e68cf-144">**[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="e68cf-144">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="e68cf-146">トークンの格納と更新</span><span class="sxs-lookup"><span data-stu-id="e68cf-146">Storing and refreshing tokens</span></span>

<span data-ttu-id="e68cf-147">この時点で、アプリケーションには、API 呼び出しのヘッダーで送信されるアクセストークンがあり `Authorization` ます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-147">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="e68cf-148">これは、アプリが Microsoft Graph にユーザーの代わりにアクセスできるようにするトークンです。</span><span class="sxs-lookup"><span data-stu-id="e68cf-148">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="e68cf-149">ただし、このトークンは一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="e68cf-149">However, this token is short-lived.</span></span> <span data-ttu-id="e68cf-150">トークンが発行された後、有効期限が切れる時間になります。</span><span class="sxs-lookup"><span data-stu-id="e68cf-150">The token expires an hour after it is issued.</span></span> <span data-ttu-id="e68cf-151">ここで、更新トークンが役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-151">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="e68cf-152">更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-152">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="e68cf-153">アプリは、Microsoft Identity Web ライブラリを使用しているため、トークンストレージを実装したり、ロジックを更新したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e68cf-153">Because the app is using the Microsoft.Identity.Web library, you do not have to implement any token storage or refresh logic.</span></span>

<span data-ttu-id="e68cf-154">アプリでは、アプリの再起動時にトークンを保持する必要がないアプリに十分なメモリ内トークンキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-154">The app uses the in-memory token cache, which is sufficient for apps that do not need to persist tokens when the app restarts.</span></span> <span data-ttu-id="e68cf-155">代わりに、運用アプリは、Microsoft Identity. Web ライブラリの [分散キャッシュオプション](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="e68cf-155">Production apps may instead use the [distributed cache options](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in the Microsoft.Identity.Web library.</span></span>

<span data-ttu-id="e68cf-156">この `GetAccessTokenForUserAsync` メソッドは、トークンの有効期限を処理し、更新します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-156">The `GetAccessTokenForUserAsync` method handles token expiration and refresh for you.</span></span> <span data-ttu-id="e68cf-157">最初に、キャッシュされたトークンをチェックし、有効期限が切れていない場合はそれを返します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-157">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="e68cf-158">有効期限が切れている場合は、キャッシュされた更新トークンを使用して新しいものを取得します。</span><span class="sxs-lookup"><span data-stu-id="e68cf-158">If it is expired, it uses the cached refresh token to obtain a new one.</span></span>

<span data-ttu-id="e68cf-159">コントローラーが依存関係の挿入によって取得する **Graphserviceclient** ユーザーは、ユーザーが使用する認証プロバイダーで事前に構成され `GetAccessTokenForUserAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="e68cf-159">The **GraphServiceClient** that controllers get via dependency injection will be pre-configured with an authentication provider that uses `GetAccessTokenForUserAsync` for you.</span></span>

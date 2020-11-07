---
ms.openlocfilehash: 308938efbedc4618c7b0ca3ea6b2eebc0582da10
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822486"
---
<!-- markdownlint-disable MD002 MD041 -->

最初に、ASP.NET コア web アプリを作成します。

1. プロジェクトを作成するディレクトリで、コマンドラインインターフェイス (CLI) を開きます。 次のコマンドを実行します。

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. プロジェクトが作成されたら、現在のディレクトリを **Graphtutorial** ディレクトリに変更し、CLI で次のコマンドを実行して、動作を確認します。

    ```Shell
    dotnet run
    ```

1. ブラウザーを開き、を参照し `https://localhost:5001` ます。 すべてが動作している場合は、既定の ASP.NET コアページが表示されます。

> [!IMPORTANT]
> **Localhost** の証明書が信頼されていないことを示す警告が表示された場合は、.NET コア CLI を使用して開発証明書をインストールして信頼できます。 特定のオペレーティングシステムの手順については、「 [ASP.NET Core で HTTPS を強制](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) する」を参照してください。

## <a name="add-nuget-packages"></a>NuGet パッケージを追加する

に進む前に、後で使用する追加の NuGet パッケージをインストールします。

- アクセストークンを要求および管理するための[Microsoft Identity](https://www.nuget.org/packages/Microsoft.Identity.Web/) 。
- 依存関係の挿入を介して Microsoft Graph SDK を追加するための、 [Microsoft Identity グラフ](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/)。
- サインインとサインアウトの UI の場合は、 [「Microsoft Identity](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) 」を参照してください。
- [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/): Microsoft Graph を呼び出すためのものです。
- [TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) は、プラットフォーム間での時間帯識別子の処理を行います。

1. CLI で次のコマンドを実行して、依存関係をインストールします。

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Identity.Web.UI --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.18.0
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、アプリケーションの基本的な UI 構造を作成します。

### <a name="implement-alert-extension-methods"></a>通知の拡張メソッドを実装する

このセクションでは、 `IActionResult` コントローラビューによって返される型の拡張メソッドを作成します。 この拡張機能を使用すると、一時的なエラーまたは成功メッセージをビューに渡すことができます。

> [!TIP]
> このチュートリアルでは、任意のテキストエディターを使用して、ソースファイルを編集できます。 ただし、デバッグや Intellisense などの追加機能は [Visual Studio Code](https://code.visualstudio.com/) によって提供されます。

1. **Alerts** という名前の **graphtutorial** ディレクトリに新しいディレクトリを作成します。

1. **WithAlertResult.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. **AlertExtensions.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a>ユーザーデータ拡張メソッドを実装する

このセクションでは、 `ClaimsPrincipal` Microsoft Identity platform によって生成されたオブジェクトの拡張メソッドを作成します。 これにより、既存のユーザー id を Microsoft Graph のデータで拡張することができます。

> [!NOTE]
> このコードは、後のセクションで説明するように、ここではプレースホルダーにすぎません。

1. **Graph** という名前の **graphtutorial** ディレクトリに新しいディレクトリを作成します。

1. **GraphClaimsPrincipalExtensions.cs** という名前の新しいファイルを作成し、次のコードを追加します。

    ```csharp
    using System.Security.Claims;

    namespace GraphTutorial
    {
        public static class GraphClaimTypes {
            public const string DisplayName ="graph_name";
            public const string Email = "graph_email";
            public const string Photo = "graph_photo";
            public const string TimeZone = "graph_timezone";
            public const string DateTimeFormat = "graph_datetimeformat";
        }

        // Helper methods to access Graph user data stored in
        // the claims principal
        public static class GraphClaimsPrincipalExtensions
        {
            public static string GetUserGraphDisplayName(this ClaimsPrincipal claimsPrincipal)
            {
                return "Adele Vance";
            }

            public static string GetUserGraphEmail(this ClaimsPrincipal claimsPrincipal)
            {
                return "adelev@contoso.com";
            }

            public static string GetUserGraphPhoto(this ClaimsPrincipal claimsPrincipal)
            {
                return "/img/no-profile-photo.png";
            }
        }
    }
    ```

### <a name="create-views"></a>ビューを作成する

このセクションでは、アプリケーションの Razor ビューを実装します。

1. 次のコードを追加して、 **_LoginPartial** という名前の新しいファイルを **./** の名前または共有ディレクトリに追加します。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. 次のコードを追加して、 **_AlertPartial** という名前の新しいファイルを **./** の名前または共有ディレクトリに追加します。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. アプリのグローバル レイアウトを更新するため、 **./Views/Shared/_Layout.cshtml** ファイルを開き、内容全体を次のコードで置き換えます。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. **/Wwwroot/css/site.css** を開き、ファイルの下部に次のコードを追加します。

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. **./Views/Home/index.cshtml** ファイルを開き、その内容を次のように置き換えます。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. **Img** という名前の **./wwwroot** ディレクトリに新しいディレクトリを作成します。 このディレクトリに、選択した名前の **no-profile-photo.png** のイメージファイルを追加します。 この画像は、ユーザーが Microsoft Graph に写真を持たない場合にユーザーの写真として使用されます。

    > [!TIP]
    > これらのスクリーンショットに使用されているイメージは、 [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png)からダウンロードできます。

1. すべての変更を保存し、サーバーを再起動し `dotnet run` ます ()。 この時点で、アプリの外観は大きく異なります。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)

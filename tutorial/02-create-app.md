---
ms.openlocfilehash: 308938efbedc4618c7b0ca3ea6b2eebc0582da10
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822486"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c0daf-101">最初に、ASP.NET コア web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-101">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="c0daf-102">プロジェクトを作成するディレクトリで、コマンドラインインターフェイス (CLI) を開きます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="c0daf-103">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-103">Run the following command.</span></span>

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. <span data-ttu-id="c0daf-104">プロジェクトが作成されたら、現在のディレクトリを **Graphtutorial** ディレクトリに変更し、CLI で次のコマンドを実行して、動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="c0daf-105">ブラウザーを開き、を参照し `https://localhost:5001` ます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-105">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="c0daf-106">すべてが動作している場合は、既定の ASP.NET コアページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-106">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c0daf-107">**Localhost** の証明書が信頼されていないことを示す警告が表示された場合は、.NET コア CLI を使用して開発証明書をインストールして信頼できます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-107">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="c0daf-108">特定のオペレーティングシステムの手順については、「 [ASP.NET Core で HTTPS を強制](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0daf-108">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="c0daf-109">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="c0daf-109">Add NuGet packages</span></span>

<span data-ttu-id="c0daf-110">に進む前に、後で使用する追加の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0daf-110">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="c0daf-111">アクセストークンを要求および管理するための[Microsoft Identity](https://www.nuget.org/packages/Microsoft.Identity.Web/) 。</span><span class="sxs-lookup"><span data-stu-id="c0daf-111">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="c0daf-112">依存関係の挿入を介して Microsoft Graph SDK を追加するための、 [Microsoft Identity グラフ](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/)。</span><span class="sxs-lookup"><span data-stu-id="c0daf-112">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding the Microsoft Graph SDK via dependency injection.</span></span>
- <span data-ttu-id="c0daf-113">サインインとサインアウトの UI の場合は、 [「Microsoft Identity](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0daf-113">[Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) for sign-in and sign-out UI.</span></span>
- <span data-ttu-id="c0daf-114">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/): Microsoft Graph を呼び出すためのものです。</span><span class="sxs-lookup"><span data-stu-id="c0daf-114">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="c0daf-115">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) は、プラットフォーム間での時間帯識別子の処理を行います。</span><span class="sxs-lookup"><span data-stu-id="c0daf-115">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for handling time zoned identifiers cross-platform.</span></span>

1. <span data-ttu-id="c0daf-116">CLI で次のコマンドを実行して、依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0daf-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Identity.Web.UI --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.18.0
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="c0daf-117">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="c0daf-117">Design the app</span></span>

<span data-ttu-id="c0daf-118">このセクションでは、アプリケーションの基本的な UI 構造を作成します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-118">In this section you will create the basic UI structure of the application.</span></span>

### <a name="implement-alert-extension-methods"></a><span data-ttu-id="c0daf-119">通知の拡張メソッドを実装する</span><span class="sxs-lookup"><span data-stu-id="c0daf-119">Implement alert extension methods</span></span>

<span data-ttu-id="c0daf-120">このセクションでは、 `IActionResult` コントローラビューによって返される型の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-120">In this section you will create extension methods for the `IActionResult` type returned by controller views.</span></span> <span data-ttu-id="c0daf-121">この拡張機能を使用すると、一時的なエラーまたは成功メッセージをビューに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-121">This extension will enable passing temporary error or success messages to the view.</span></span>

> [!TIP]
> <span data-ttu-id="c0daf-122">このチュートリアルでは、任意のテキストエディターを使用して、ソースファイルを編集できます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-122">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="c0daf-123">ただし、デバッグや Intellisense などの追加機能は [Visual Studio Code](https://code.visualstudio.com/) によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-123">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="c0daf-124">**Alerts** という名前の **graphtutorial** ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-124">Create a new directory in the **GraphTutorial** directory named **Alerts**.</span></span>

1. <span data-ttu-id="c0daf-125">**WithAlertResult.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**</span><span class="sxs-lookup"><span data-stu-id="c0daf-125">Create a new file named **WithAlertResult.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. <span data-ttu-id="c0daf-126">**AlertExtensions.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**</span><span class="sxs-lookup"><span data-stu-id="c0daf-126">Create a new file named **AlertExtensions.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a><span data-ttu-id="c0daf-127">ユーザーデータ拡張メソッドを実装する</span><span class="sxs-lookup"><span data-stu-id="c0daf-127">Implement user data extension methods</span></span>

<span data-ttu-id="c0daf-128">このセクションでは、 `ClaimsPrincipal` Microsoft Identity platform によって生成されたオブジェクトの拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-128">In this section you will create extension methods for the `ClaimsPrincipal` object generated by the Microsoft Identity platform.</span></span> <span data-ttu-id="c0daf-129">これにより、既存のユーザー id を Microsoft Graph のデータで拡張することができます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-129">This will allow you to extend the existing user identity with data from Microsoft Graph.</span></span>

> [!NOTE]
> <span data-ttu-id="c0daf-130">このコードは、後のセクションで説明するように、ここではプレースホルダーにすぎません。</span><span class="sxs-lookup"><span data-stu-id="c0daf-130">This code is just a placeholder for now, you will complete it in a later section.</span></span>

1. <span data-ttu-id="c0daf-131">**Graph** という名前の **graphtutorial** ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-131">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>

1. <span data-ttu-id="c0daf-132">**GraphClaimsPrincipalExtensions.cs** という名前の新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-132">Create a new file named **GraphClaimsPrincipalExtensions.cs** and add the following code.</span></span>

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

### <a name="create-views"></a><span data-ttu-id="c0daf-133">ビューを作成する</span><span class="sxs-lookup"><span data-stu-id="c0daf-133">Create views</span></span>

<span data-ttu-id="c0daf-134">このセクションでは、アプリケーションの Razor ビューを実装します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-134">In this section you will implement the Razor views for the application.</span></span>

1. <span data-ttu-id="c0daf-135">次のコードを追加して、 **_LoginPartial** という名前の新しいファイルを **./** の名前または共有ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-135">Add a new file named **_LoginPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. <span data-ttu-id="c0daf-136">次のコードを追加して、 **_AlertPartial** という名前の新しいファイルを **./** の名前または共有ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-136">Add a new file named **_AlertPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. <span data-ttu-id="c0daf-137">アプリのグローバル レイアウトを更新するため、 **./Views/Shared/_Layout.cshtml** ファイルを開き、内容全体を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-137">Open the **./Views/Shared/_Layout.cshtml** file, and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. <span data-ttu-id="c0daf-138">**/Wwwroot/css/site.css** を開き、ファイルの下部に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-138">Open **./wwwroot/css/site.css** and add the following code at the bottom of the file.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="c0daf-139">**./Views/Home/index.cshtml** ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-139">Open the **./Views/Home/index.cshtml** file and replace its contents with the following.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. <span data-ttu-id="c0daf-140">**Img** という名前の **./wwwroot** ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-140">Create a new directory in the **./wwwroot** directory named **img**.</span></span> <span data-ttu-id="c0daf-141">このディレクトリに、選択した名前の **no-profile-photo.png** のイメージファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="c0daf-141">Add an image file of your choosing named **no-profile-photo.png** in this directory.</span></span> <span data-ttu-id="c0daf-142">この画像は、ユーザーが Microsoft Graph に写真を持たない場合にユーザーの写真として使用されます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-142">This image will be used as the user's photo when the user has no photo in Microsoft Graph.</span></span>

    > [!TIP]
    > <span data-ttu-id="c0daf-143">これらのスクリーンショットに使用されているイメージは、 [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png)からダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="c0daf-143">You can download the image used in these screenshots from [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).</span></span>

1. <span data-ttu-id="c0daf-144">すべての変更を保存し、サーバーを再起動し `dotnet run` ます ()。</span><span class="sxs-lookup"><span data-stu-id="c0daf-144">Save all of your changes and restart the server (`dotnet run`).</span></span> <span data-ttu-id="c0daf-145">この時点で、アプリの外観は大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="c0daf-145">Now, the app should look very different.</span></span>

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)

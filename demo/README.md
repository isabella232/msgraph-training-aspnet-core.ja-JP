---
ms.openlocfilehash: 30398bafbf47aaa374a200dd1834c9f2003e967f
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822441"
---
# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="96b7a-101">完了したプロジェクトを実行する方法</span><span class="sxs-lookup"><span data-stu-id="96b7a-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="96b7a-102">前提条件</span><span class="sxs-lookup"><span data-stu-id="96b7a-102">Prerequisites</span></span>

<span data-ttu-id="96b7a-103">このフォルダーで完了したプロジェクトを実行するには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="96b7a-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="96b7a-104">開発用コンピューターにインストールされている [.Net コア SDK](https://dotnet.microsoft.com/download) 。</span><span class="sxs-lookup"><span data-stu-id="96b7a-104">The [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="96b7a-105">( **注:** このチュートリアルは、.NET Core SDK バージョン3.1.201 を使用して作成されています。</span><span class="sxs-lookup"><span data-stu-id="96b7a-105">( **Note:** This tutorial was written with .NET Core SDK version 3.1.201.</span></span> <span data-ttu-id="96b7a-106">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。)</span><span class="sxs-lookup"><span data-stu-id="96b7a-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="96b7a-107">Outlook.com 上のメールボックスを持つ個人の Microsoft アカウント、または Microsoft 職場または学校のアカウントのいずれか。</span><span class="sxs-lookup"><span data-stu-id="96b7a-107">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="96b7a-108">Microsoft アカウントを持っていない場合は、無料のアカウントを取得するためのオプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="96b7a-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="96b7a-109">[新しい個人用 Microsoft アカウントにサインアップ](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)することができます。</span><span class="sxs-lookup"><span data-stu-id="96b7a-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="96b7a-110">[Office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="96b7a-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="96b7a-111">Web アプリケーションを Azure Active Directory 管理センターに登録する</span><span class="sxs-lookup"><span data-stu-id="96b7a-111">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="96b7a-112">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-112">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="96b7a-113">**個人用アカウント** (別名: Microsoft アカウント)、または **職場/学校アカウント** を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="96b7a-113">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="96b7a-114">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-114">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="96b7a-115">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="96b7a-115">A screenshot of the App registrations</span></span> ](../tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="96b7a-116">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-116">Select **New registration**.</span></span> <span data-ttu-id="96b7a-117">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-117">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="96b7a-118">`ASP.NET Core Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-118">Set **Name** to `ASP.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="96b7a-119">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-119">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="96b7a-120">**[リダイレクト URI]** で、最初のドロップダウン リストを `Web` に設定し、それから `https://localhost:5001/` に値を設定します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-120">Under **Redirect URI** , set the first drop-down to `Web` and set the value to `https://localhost:5001/`.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](../tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="96b7a-122">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-122">Select **Register**.</span></span> <span data-ttu-id="96b7a-123">**ASP.NET コアグラフのチュートリアル** ページで、 **アプリケーション (クライアント) ID** の値をコピーして保存します。そのためには、次の手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="96b7a-123">On the **ASP.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](../tutorial/images/aad-application-id.png)

1. <span data-ttu-id="96b7a-125">**[管理]** の下の **[認証]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-125">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="96b7a-126">[ **リダイレクト uri** ] で、値と共に URI を追加 `https://localhost:5001/signin-oidc` します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-126">Under **Redirect URIs** add a URI with the value `https://localhost:5001/signin-oidc`.</span></span>

1. <span data-ttu-id="96b7a-127">**ログアウト URL** をに設定 `https://localhost:5001/signout-oidc` します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-127">Set the **Logout URL** to `https://localhost:5001/signout-oidc`.</span></span>

1. <span data-ttu-id="96b7a-128">**[暗黙的な許可]** セクションを検索し、 **[ID トークン]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="96b7a-128">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="96b7a-129">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-129">Select **Save**.</span></span>

    ![Azure portal の Web プラットフォームの設定のスクリーンショット](../tutorial/images/aad-web-platform.png)

1. <span data-ttu-id="96b7a-131">**[管理]** で **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-131">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="96b7a-132">
            \*\*[新しいクライアント シークレット]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-132">Select the **New client secret** button.</span></span> <span data-ttu-id="96b7a-133">**[説明]** に値を入力して、 **[有効期限]** のオプションのいずれかを選び、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-133">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![[クライアントシークレットの追加] ダイアログのスクリーンショット](../tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="96b7a-135">このページを離れる前に、クライアント シークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="96b7a-135">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="96b7a-136">次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="96b7a-136">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="96b7a-137">このクライアント シークレットは今後表示されないため、この段階で必ずコピーするようにしてください。</span><span class="sxs-lookup"><span data-stu-id="96b7a-137">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![新規追加されたクライアント シークレットのスクリーンショット](../tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="96b7a-139">サンプルを構成する</span><span class="sxs-lookup"><span data-stu-id="96b7a-139">Configure the sample</span></span>

1. <span data-ttu-id="96b7a-140">**Graphtutorial .csproj** が配置されているディレクトリでコマンドラインインターフェイス (CLI) を開き、次のコマンドを実行します `YOUR_APP_ID` 。アプリケーション ID は Azure portal から、 `YOUR_APP_SECRET` アプリケーションシークレットで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="96b7a-140">Open your command line interface (CLI) in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="run-the-sample"></a><span data-ttu-id="96b7a-141">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="96b7a-141">Run the sample</span></span>

<span data-ttu-id="96b7a-142">CLI で、次のコマンドを実行してアプリケーションを起動します。</span><span class="sxs-lookup"><span data-stu-id="96b7a-142">In your CLI, run the following command to start the application.</span></span>

```Shell
dotnet run
```

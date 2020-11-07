---
ms.openlocfilehash: debd685996df22a83110a14ca585cfafa4e08a0d
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822432"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6a066-101">この演習では、Azure Active Directory 管理センターを使用して、新しい Azure AD web アプリケーション登録を作成します。</span><span class="sxs-lookup"><span data-stu-id="6a066-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="6a066-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="6a066-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="6a066-103">**個人用アカウント** (別名: Microsoft アカウント)、または **職場/学校アカウント** を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="6a066-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="6a066-104">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="6a066-105">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="6a066-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="6a066-106">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-106">Select **New registration**.</span></span> <span data-ttu-id="6a066-107">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="6a066-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="6a066-108">`ASP.NET Core Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="6a066-108">Set **Name** to `ASP.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="6a066-109">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a066-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="6a066-110">**[リダイレクト URI]** で、最初のドロップダウン リストを `Web` に設定し、それから `https://localhost:5001/` に値を設定します。</span><span class="sxs-lookup"><span data-stu-id="6a066-110">Under **Redirect URI** , set the first drop-down to `Web` and set the value to `https://localhost:5001/`.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="6a066-112">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-112">Select **Register**.</span></span> <span data-ttu-id="6a066-113">**ASP.NET コアグラフのチュートリアル** ページで、 **アプリケーション (クライアント) ID** の値をコピーして保存します。そのためには、次の手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a066-113">On the **ASP.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. <span data-ttu-id="6a066-115">**[管理]** の下の **[認証]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-115">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="6a066-116">[ **リダイレクト uri** ] で、値と共に URI を追加 `https://localhost:5001/signin-oidc` します。</span><span class="sxs-lookup"><span data-stu-id="6a066-116">Under **Redirect URIs** add a URI with the value `https://localhost:5001/signin-oidc`.</span></span>

1. <span data-ttu-id="6a066-117">**ログアウト URL** をに設定 `https://localhost:5001/signout-oidc` します。</span><span class="sxs-lookup"><span data-stu-id="6a066-117">Set the **Logout URL** to `https://localhost:5001/signout-oidc`.</span></span>

1. <span data-ttu-id="6a066-118">**[暗黙的な許可]** セクションを検索し、 **[ID トークン]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6a066-118">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="6a066-119">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-119">Select **Save**.</span></span>

    ![Azure portal の Web プラットフォームの設定のスクリーンショット](./images/aad-web-platform.png)

1. <span data-ttu-id="6a066-121">**[管理]** で **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-121">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="6a066-122">
            \*\*[新しいクライアント シークレット]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-122">Select the **New client secret** button.</span></span> <span data-ttu-id="6a066-123">**[説明]** に値を入力して、 **[有効期限]** のオプションのいずれかを選び、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a066-123">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![[クライアントシークレットの追加] ダイアログのスクリーンショット](./images/aad-new-client-secret.png)

1. <span data-ttu-id="6a066-125">このページを離れる前に、クライアント シークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="6a066-125">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="6a066-126">次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="6a066-126">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6a066-127">このクライアント シークレットは今後表示されないため、この段階で必ずコピーするようにしてください。</span><span class="sxs-lookup"><span data-stu-id="6a066-127">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![新規追加されたクライアント シークレットのスクリーンショット](./images/aad-copy-client-secret.png)

---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822395"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e65f1-101">このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="e65f1-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-model"></a><span data-ttu-id="e65f1-102">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="e65f1-102">Create model</span></span>

1. <span data-ttu-id="e65f1-103">**NewEvent.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**</span><span class="sxs-lookup"><span data-stu-id="e65f1-103">Create a new file named **NewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a><span data-ttu-id="e65f1-104">ビューを作成する</span><span class="sxs-lookup"><span data-stu-id="e65f1-104">Create view</span></span>

1. <span data-ttu-id="e65f1-105">次のコードを追加して、" **new** " という名前の新しいファイルを作成します **。**</span><span class="sxs-lookup"><span data-stu-id="e65f1-105">Create a new file named **New.cshtml** in he **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a><span data-ttu-id="e65f1-106">コントローラーアクションを追加する</span><span class="sxs-lookup"><span data-stu-id="e65f1-106">Add controller actions</span></span>

1. <span data-ttu-id="e65f1-107">**/Controllers/CalendarController.cs** を開き、次のアクションをクラスに追加して `CalendarController` 、新しいイベントフォームを表示します。</span><span class="sxs-lookup"><span data-stu-id="e65f1-107">Open **./Controllers/CalendarController.cs** and add the following action to the `CalendarController` class to render the new event form.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. <span data-ttu-id="e65f1-108">`CalendarController`ユーザーが [ **保存** ] をクリックして Microsoft Graph を使用してユーザーの予定表にイベントを追加したときに、フォームから新しいイベントを受信するための次のアクションをクラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="e65f1-108">Add the following action to the `CalendarController` class to receive the new event from the form when the user clicks **Save** and use Microsoft Graph to add the event to the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. <span data-ttu-id="e65f1-109">アプリを起動し、サインインして、 **[カレンダー]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e65f1-109">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="e65f1-110">[ **新しいイベント** ] ボタンをクリックして、フォームに入力し、[ **保存** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e65f1-110">Click the **New event** button, fill in the form, and click **Save**.</span></span>

    ![新しいイベントフォームのスクリーンショット](./images/create-event-01.png)

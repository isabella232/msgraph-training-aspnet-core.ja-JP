---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822395"
---
<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。

## <a name="create-model"></a>モデルの作成

1. **NewEvent.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a>ビューを作成する

1. 次のコードを追加して、" **new** " という名前の新しいファイルを作成します **。**

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a>コントローラーアクションを追加する

1. **/Controllers/CalendarController.cs** を開き、次のアクションをクラスに追加して `CalendarController` 、新しいイベントフォームを表示します。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. `CalendarController`ユーザーが [ **保存** ] をクリックして Microsoft Graph を使用してユーザーの予定表にイベントを追加したときに、フォームから新しいイベントを受信するための次のアクションをクラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. アプリを起動し、サインインして、 **[カレンダー]** リンクをクリックします。 [ **新しいイベント** ] ボタンをクリックして、フォームに入力し、[ **保存** ] をクリックします。

    ![新しいイベントフォームのスクリーンショット](./images/create-event-01.png)

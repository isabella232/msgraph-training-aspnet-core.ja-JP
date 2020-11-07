---
ms.openlocfilehash: 17394dd6283464eabcbea1f60c48640412b55431
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822504"
---
<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、アプリケーションに Microsoft Graph を組み込みます。 このアプリケーションでは、microsoft [Graph クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet) を使用して microsoft graph を呼び出すことにします。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

最初に、予定表ビュー用の新しいコントローラーを作成します。

1. **CalendarController.cs** という名前の新しいファイルを **./Controllers** ディレクトリに追加し、次のコードを追加します。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using TimeZoneConverter;

    namespace GraphTutorial.Controllers
    {
        public class CalendarController : Controller
        {
            private readonly GraphServiceClient _graphClient;
            private readonly ILogger<HomeController> _logger;

            public CalendarController(
                GraphServiceClient graphClient,
                ILogger<HomeController> logger)
            {
                _graphClient = graphClient;
                _logger = logger;
            }
        }
    }
    ```

1. 次の関数をクラスに追加して、 `CalendarController` ユーザーの予定表ビューを取得します。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    のコードについて検討 `GetUserWeekCalendar` します。

    - ユーザーのタイムゾーンを使用して、週の UTC の開始日と終了日/時刻の値を取得します。
    - ユーザーの [予定表ビュー](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) を照会して、開始と終了の日付/時刻の間にあるすべてのイベントを取得します。 [イベントを一覧](/graph/api/user-list-events?view=graph-rest-1.0)表示する代わりに予定表ビューを使用すると、定期的なイベントが拡張され、指定した期間内に発生するすべてのアイテムが返されます。
    - ヘッダーを使用して、 `Prefer: outlook.timezone` ユーザーのタイムゾーンに返された結果を取得します。
    - を使用して、 `Select` 返されるフィールドをアプリで使用されるものだけに制限します。
    - を使用して `OrderBy` 、結果を時系列順に並べ替えます。
    - `PageIterator`[イベントコレクションの to ページ](/graph/sdks/paging)を使用します。 これにより、ユーザーの予定表に、要求されたページサイズよりも多くのイベントがある場合に処理されます。

1. 次の関数をクラスに追加して、 `CalendarController` 返されるデータの一時ビューを実装します。

    ```csharp
    // Minimum permission scope needed for this view
    [AuthorizeForScopes(Scopes = new[] { "Calendars.Read" })]
    public async Task<IActionResult> Index()
    {
        try
        {
            var userTimeZone = TZConvert.GetTimeZoneInfo(
                User.GetUserGraphTimeZone());
            var startOfWeek = CalendarController.GetUtcStartOfWeekInTimeZone(
                DateTime.Today, userTimeZone);

            var events = await GetUserWeekCalendar(startOfWeek);

            // Return a JSON dump of events
            return new ContentResult {
                Content = _graphClient.HttpProvider.Serializer.SerializeObject(events),
                ContentType = "application/json"
            };
        }
        catch (ServiceException ex)
        {
            if (ex.InnerException is MicrosoftIdentityWebChallengeUserException)
            {
                throw ex;
            }

            return new ContentResult {
                Content = $"Error getting calendar view: {ex.Message}",
                ContentType = "text/plain"
            };
        }
    }
    ```

1. アプリを起動し、サインインして、ナビゲーション バーの **[カレンダー]** リンクをクリックします。 すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

結果を表示するとき、よりユーザー フレンドリなビューを追加できます。

### <a name="create-view-models"></a>ビューモデルを作成する

1. **CalendarViewEvent.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. **DailyViewModel.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. **CalendarViewModel.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a>ビューを作成する

1. **./Views** ディレクトリに **Calendar** という名前の新しいディレクトリを作成します。

1. 次のコードを追加して、 **_DailyEventsPartial** という名前の新しいファイルを **./のビューまたは予定表** のディレクトリに作成します。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. 次のコードを追加して、 **./のビューまたは予定表** のディレクトリに、次のよう **な名前の** 新しいファイルを作成します。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a>予定表コントローラーを更新する

1. **/Controllers/CalendarController.cs** を開き、既存の `Index` 関数を次のように置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. アプリを起動し、サインインして、 **[カレンダー]** リンクをクリックします。 アプリにイベントの表が表示されます。

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)

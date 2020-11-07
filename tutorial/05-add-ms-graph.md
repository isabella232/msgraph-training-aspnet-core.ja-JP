---
ms.openlocfilehash: 17394dd6283464eabcbea1f60c48640412b55431
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822504"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8947f-101">このセクションでは、アプリケーションに Microsoft Graph を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="8947f-101">In this section you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="8947f-102">このアプリケーションでは、microsoft [Graph クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet) を使用して microsoft graph を呼び出すことにします。</span><span class="sxs-lookup"><span data-stu-id="8947f-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="8947f-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="8947f-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="8947f-104">最初に、予定表ビュー用の新しいコントローラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8947f-104">Start by creating a new controller for calendar views.</span></span>

1. <span data-ttu-id="8947f-105">**CalendarController.cs** という名前の新しいファイルを **./Controllers** ディレクトリに追加し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8947f-105">Add a new file named **CalendarController.cs** in the **./Controllers** directory and add the following code.</span></span>

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

1. <span data-ttu-id="8947f-106">次の関数をクラスに追加して、 `CalendarController` ユーザーの予定表ビューを取得します。</span><span class="sxs-lookup"><span data-stu-id="8947f-106">Add the following functions to the `CalendarController` class to get the user's calendar view.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    <span data-ttu-id="8947f-107">のコードについて検討 `GetUserWeekCalendar` します。</span><span class="sxs-lookup"><span data-stu-id="8947f-107">Consider what the code in `GetUserWeekCalendar` does.</span></span>

    - <span data-ttu-id="8947f-108">ユーザーのタイムゾーンを使用して、週の UTC の開始日と終了日/時刻の値を取得します。</span><span class="sxs-lookup"><span data-stu-id="8947f-108">It uses the user's time zone to get UTC start and end date/time values for the week.</span></span>
    - <span data-ttu-id="8947f-109">ユーザーの [予定表ビュー](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) を照会して、開始と終了の日付/時刻の間にあるすべてのイベントを取得します。</span><span class="sxs-lookup"><span data-stu-id="8947f-109">It queries the user's [calendar view](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) to get all events that fall between the start and end date/times.</span></span> <span data-ttu-id="8947f-110">[イベントを一覧](/graph/api/user-list-events?view=graph-rest-1.0)表示する代わりに予定表ビューを使用すると、定期的なイベントが拡張され、指定した期間内に発生するすべてのアイテムが返されます。</span><span class="sxs-lookup"><span data-stu-id="8947f-110">Using a calendar view instead of [listing events](/graph/api/user-list-events?view=graph-rest-1.0) expands recurring events, returning any occurrences that happen in the specified time window.</span></span>
    - <span data-ttu-id="8947f-111">ヘッダーを使用して、 `Prefer: outlook.timezone` ユーザーのタイムゾーンに返された結果を取得します。</span><span class="sxs-lookup"><span data-stu-id="8947f-111">It uses the `Prefer: outlook.timezone` header to get results back in the user's timezone.</span></span>
    - <span data-ttu-id="8947f-112">を使用して、 `Select` 返されるフィールドをアプリで使用されるものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="8947f-112">It uses `Select` to limit the fields that come back to just those used by the app.</span></span>
    - <span data-ttu-id="8947f-113">を使用して `OrderBy` 、結果を時系列順に並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="8947f-113">It uses `OrderBy` to sort the results chronologically.</span></span>
    - <span data-ttu-id="8947f-114">`PageIterator`[イベントコレクションの to ページ](/graph/sdks/paging)を使用します。</span><span class="sxs-lookup"><span data-stu-id="8947f-114">It uses a `PageIterator` to [page through the events collection](/graph/sdks/paging).</span></span> <span data-ttu-id="8947f-115">これにより、ユーザーの予定表に、要求されたページサイズよりも多くのイベントがある場合に処理されます。</span><span class="sxs-lookup"><span data-stu-id="8947f-115">This handles the case where the user has more events on their calendar than the requested page size.</span></span>

1. <span data-ttu-id="8947f-116">次の関数をクラスに追加して、 `CalendarController` 返されるデータの一時ビューを実装します。</span><span class="sxs-lookup"><span data-stu-id="8947f-116">Add the following function to the `CalendarController` class to implement a temporary view of the returned data.</span></span>

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

1. <span data-ttu-id="8947f-117">アプリを起動し、サインインして、ナビゲーション バーの **[カレンダー]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="8947f-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="8947f-118">すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8947f-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="8947f-119">結果の表示</span><span class="sxs-lookup"><span data-stu-id="8947f-119">Display the results</span></span>

<span data-ttu-id="8947f-120">結果を表示するとき、よりユーザー フレンドリなビューを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8947f-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

### <a name="create-view-models"></a><span data-ttu-id="8947f-121">ビューモデルを作成する</span><span class="sxs-lookup"><span data-stu-id="8947f-121">Create view models</span></span>

1. <span data-ttu-id="8947f-122">**CalendarViewEvent.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**</span><span class="sxs-lookup"><span data-stu-id="8947f-122">Create a new file named **CalendarViewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. <span data-ttu-id="8947f-123">**DailyViewModel.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**</span><span class="sxs-lookup"><span data-stu-id="8947f-123">Create a new file named **DailyViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. <span data-ttu-id="8947f-124">**CalendarViewModel.cs** という名前の新しいファイルを作成し、次のコードを追加します **。**</span><span class="sxs-lookup"><span data-stu-id="8947f-124">Create a new file named **CalendarViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a><span data-ttu-id="8947f-125">ビューを作成する</span><span class="sxs-lookup"><span data-stu-id="8947f-125">Create views</span></span>

1. <span data-ttu-id="8947f-126">**./Views** ディレクトリに **Calendar** という名前の新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="8947f-126">Create a new directory named **Calendar** in the **./Views** directory.</span></span>

1. <span data-ttu-id="8947f-127">次のコードを追加して、 **_DailyEventsPartial** という名前の新しいファイルを **./のビューまたは予定表** のディレクトリに作成します。</span><span class="sxs-lookup"><span data-stu-id="8947f-127">Create a new file named **_DailyEventsPartial.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. <span data-ttu-id="8947f-128">次のコードを追加して、 **./のビューまたは予定表** のディレクトリに、次のよう **な名前の** 新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="8947f-128">Create a new file named **Index.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a><span data-ttu-id="8947f-129">予定表コントローラーを更新する</span><span class="sxs-lookup"><span data-stu-id="8947f-129">Update calendar controller</span></span>

1. <span data-ttu-id="8947f-130">**/Controllers/CalendarController.cs** を開き、既存の `Index` 関数を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8947f-130">Open **./Controllers/CalendarController.cs** and replace the existing `Index` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. <span data-ttu-id="8947f-131">アプリを起動し、サインインして、 **[カレンダー]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="8947f-131">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="8947f-132">アプリにイベントの表が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8947f-132">The app should now render a table of events.</span></span>

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)

<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="718d8-101">En esta demostración, incorporará Microsoft Graph a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="718d8-101">In this demo you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="718d8-102">Para esta aplicación, usará la [biblioteca de cliente de Microsoft Graph para .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="718d8-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="718d8-103">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="718d8-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="718d8-104">Empiece extendiendo la `GraphHelper` clase que creó en el último módulo.</span><span class="sxs-lookup"><span data-stu-id="718d8-104">Start by extending the `GraphHelper` class you created in the last module.</span></span> <span data-ttu-id="718d8-105">En primer lugar, agregue `using` las siguientes instrucciones a la parte `Helpers/GraphHelper.cs` superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="718d8-105">First, add the following `using` statements to the top of the `Helpers/GraphHelper.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using Microsoft.Identity.Client;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Claims;
using System.Web;
```

<span data-ttu-id="718d8-106">A continuación, agregue el siguiente código `GraphHelper` a la clase.</span><span class="sxs-lookup"><span data-stu-id="718d8-106">Then add the following code to the `GraphHelper` class.</span></span>

```cs
// Load configuration settings from PrivateSettings.config
private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

public static async Task<IEnumerable<Event>> GetEventsAsync()
{
    var graphClient = GetAuthenticatedClient();

    var events = await graphClient.Me.Events.Request()
        .Select("subject,organizer,start,end")
        .OrderBy("createdDateTime DESC")
        .GetAsync();

    return events.CurrentPage;
}

private static GraphServiceClient GetAuthenticatedClient()
{
    return new GraphServiceClient(
        new DelegateAuthenticationProvider(
            async (requestMessage) =>
            {
                // Get the signed in user's id and create a token cache
                string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
                SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId,
                    new HttpContextWrapper(HttpContext.Current));

                var idClient = new ConfidentialClientApplication(
                    appId, redirectUri, new ClientCredential(appSecret),
                    tokenStore.GetMsalCacheInstance(), null);

                var accounts = await idClient.GetAccountsAsync();

                // By calling this here, the token can be refreshed
                // if it's expired right before the Graph call is made
                var result = await idClient.AcquireTokenSilentAsync(
                    graphScopes.Split(' '), accounts.FirstOrDefault());

                requestMessage.Headers.Authorization =
                    new AuthenticationHeaderValue("Bearer", result.AccessToken);
            }));
}
```

<span data-ttu-id="718d8-107">Tenga en cuenta lo que está haciendo este código.</span><span class="sxs-lookup"><span data-stu-id="718d8-107">Consider what this code is doing.</span></span>

- <span data-ttu-id="718d8-108">La `GetAuthenticatedClient` función Inicializa un `GraphServiceClient` con un proveedor de autenticación que llama `AcquireTokenSilentAsync`a.</span><span class="sxs-lookup"><span data-stu-id="718d8-108">The `GetAuthenticatedClient` function initializes a `GraphServiceClient` with an authentication provider that calls `AcquireTokenSilentAsync`.</span></span>
- <span data-ttu-id="718d8-109">En la `GetEventsAsync` función:</span><span class="sxs-lookup"><span data-stu-id="718d8-109">In the `GetEventsAsync` function:</span></span>
  - <span data-ttu-id="718d8-110">La dirección URL a la que se `/v1.0/me/events`llamará es.</span><span class="sxs-lookup"><span data-stu-id="718d8-110">The URL that will be called is `/v1.0/me/events`.</span></span>
  - <span data-ttu-id="718d8-111">La `Select` función limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="718d8-111">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
  - <span data-ttu-id="718d8-112">La `OrderBy` función ordena los resultados por la fecha y hora en que se crearon, con el elemento más reciente en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="718d8-112">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="718d8-113">Ahora cree un controlador para las vistas de calendario.</span><span class="sxs-lookup"><span data-stu-id="718d8-113">Now create a controller for the calendar views.</span></span> <span data-ttu-id="718d8-114">Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y elija **Agregar controlador de >...** Elija **controlador de MVC 5: vacío** y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="718d8-114">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="718d8-115">Asigne un nombre `CalendarController` al controlador y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="718d8-115">Name the controller `CalendarController` and choose **Add**.</span></span> <span data-ttu-id="718d8-116">Reemplace todo el contenido del nuevo archivo por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="718d8-116">Replace the entire contents of the new file with the following code.</span></span>

```cs
using graph_tutorial.Helpers;
using System.Threading.Tasks;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public class CalendarController : BaseController
    {
        // GET: Calendar
        [Authorize]
        public async Task<ActionResult> Index()
        {
            var events = await GraphHelper.GetEventsAsync();
            return Json(events, JsonRequestBehavior.AllowGet);
        }
    }
}
```

<span data-ttu-id="718d8-117">Ahora puede probar esto.</span><span class="sxs-lookup"><span data-stu-id="718d8-117">Now you can test this.</span></span> <span data-ttu-id="718d8-118">Inicie la aplicación, inicie sesión y haga clic en el vínculo de **calendario** en la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="718d8-118">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="718d8-119">Si todo funciona, debería ver un volcado JSON de eventos en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="718d8-119">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="718d8-120">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="718d8-120">Display the results</span></span>

<span data-ttu-id="718d8-121">Ahora puede Agregar una vista para mostrar los resultados de forma más fácil de uso.</span><span class="sxs-lookup"><span data-stu-id="718d8-121">Now you can add a view to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="718d8-122">En el explorador de soluciones, haga clic con el botón secundario en la carpeta **vistas/calendario** y elija **Agregar vista >...**. Asigne un nombre `Index` a la vista y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="718d8-122">In Solution Explorer, right-click the **Views/Calendar** folder and choose **Add > View...**. Name the view `Index` and choose **Add**.</span></span> <span data-ttu-id="718d8-123">Reemplace todo el contenido del nuevo archivo por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="718d8-123">Replace the entire contents of the new file with the following code.</span></span>

```html
@model IEnumerable<Microsoft.Graph.Event>

@{
    ViewBag.Current = "Calendar";
}

<h1>Calendar</h1>
<table class="table">
    <thead>
        <tr>
            <th scope="col">Organizer</th>
            <th scope="col">Subject</th>
            <th scope="col">Start</th>
            <th scope="col">End</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.Organizer.EmailAddress.Name</td>
                <td>@item.Subject</td>
                <td>@Convert.ToDateTime(item.Start.DateTime).ToString("M/d/yy h:mm tt")</td>
                <td>@Convert.ToDateTime(item.End.DateTime).ToString("M/d/yy h:mm tt")</td>
            </tr>
        }
    </tbody>
</table>
```

<span data-ttu-id="718d8-124">Se recorrerá en bucle una colección de eventos y se agregará una fila de tabla para cada uno.</span><span class="sxs-lookup"><span data-stu-id="718d8-124">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="718d8-125">Quite la `return Json(events, JsonRequestBehavior.AllowGet);` línea de la `Index` función en `Controllers/CalendarController.cs`y reemplácela por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="718d8-125">Remove the `return Json(events, JsonRequestBehavior.AllowGet);` line from the `Index` function in `Controllers/CalendarController.cs`, and replace it with the following code.</span></span>

```cs
return View(events);
```

<span data-ttu-id="718d8-126">Inicie la aplicación, inicie sesión y haga clic en el vínculo **calendario** .</span><span class="sxs-lookup"><span data-stu-id="718d8-126">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="718d8-127">La aplicación ahora debería representar una tabla de eventos.</span><span class="sxs-lookup"><span data-stu-id="718d8-127">The app should now render a table of events.</span></span>

![Captura de pantalla de la tabla de eventos](./images/add-msgraph-01.png)
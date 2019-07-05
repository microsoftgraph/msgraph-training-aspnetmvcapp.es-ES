<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="29c59-101">En esta demostración, incorporará Microsoft Graph a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="29c59-101">In this demo you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="29c59-102">Para esta aplicación, usará la [biblioteca de cliente de Microsoft Graph para .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="29c59-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="29c59-103">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="29c59-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="29c59-104">Empiece extendiendo la `GraphHelper` clase que creó en el último módulo.</span><span class="sxs-lookup"><span data-stu-id="29c59-104">Start by extending the `GraphHelper` class you created in the last module.</span></span>

1. <span data-ttu-id="29c59-105">Agregue las siguientes `using` instrucciones en la parte superior del `Helpers/GraphHelper.cs` archivo.</span><span class="sxs-lookup"><span data-stu-id="29c59-105">Add the following `using` statements to the top of the `Helpers/GraphHelper.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using Microsoft.Identity.Client;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Claims;
    using System.Web;
    ```

1. <span data-ttu-id="29c59-106">Agregue el siguiente código a la `GraphHelper` clase.</span><span class="sxs-lookup"><span data-stu-id="29c59-106">Add the following code to the `GraphHelper` class.</span></span>

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
                    var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                        .WithRedirectUri(redirectUri)
                        .WithClientSecret(appSecret)
                        .Build();

                    var tokenStore = new SessionTokenStore(idClient.UserTokenCache,
                            HttpContext.Current, ClaimsPrincipal.Current);

                    var accounts = await idClient.GetAccountsAsync();

                    // By calling this here, the token can be refreshed
                    // if it's expired right before the Graph call is made
                    var scopes = graphScopes.Split(' ');
                    var result = await idClient.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                        .ExecuteAsync();

                    requestMessage.Headers.Authorization =
                        new AuthenticationHeaderValue("Bearer", result.AccessToken);
                }));
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="29c59-107">Tenga en cuenta lo que está haciendo este código.</span><span class="sxs-lookup"><span data-stu-id="29c59-107">Consider what this code is doing.</span></span>
    >
    > - <span data-ttu-id="29c59-108">La `GetAuthenticatedClient` función Inicializa un `GraphServiceClient` con un proveedor de autenticación que llama `AcquireTokenSilent`a.</span><span class="sxs-lookup"><span data-stu-id="29c59-108">The `GetAuthenticatedClient` function initializes a `GraphServiceClient` with an authentication provider that calls `AcquireTokenSilent`.</span></span>
    > - <span data-ttu-id="29c59-109">En la `GetEventsAsync` función:</span><span class="sxs-lookup"><span data-stu-id="29c59-109">In the `GetEventsAsync` function:</span></span>
    >   - <span data-ttu-id="29c59-110">La dirección URL a la que se `/v1.0/me/events`llamará es.</span><span class="sxs-lookup"><span data-stu-id="29c59-110">The URL that will be called is `/v1.0/me/events`.</span></span>
    >   - <span data-ttu-id="29c59-111">La `Select` función limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="29c59-111">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="29c59-112">La `OrderBy` función ordena los resultados por la fecha y hora en que se crearon, con el elemento más reciente en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="29c59-112">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="29c59-113">Cree un controlador para las vistas de calendario.</span><span class="sxs-lookup"><span data-stu-id="29c59-113">Create a controller for the calendar views.</span></span> <span data-ttu-id="29c59-114">Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y seleccione **Agregar > controlador..**.. Elija **controlador de MVC 5: vacío** y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="29c59-114">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="29c59-115">Asigne un nombre `CalendarController` al controlador y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="29c59-115">Name the controller `CalendarController` and select **Add**.</span></span> <span data-ttu-id="29c59-116">Reemplace todo el contenido del nuevo archivo por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="29c59-116">Replace the entire contents of the new file with the following code.</span></span>

    ```cs
    using graph_tutorial.Helpers;
    using System;
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

                // Change start and end dates from UTC to local time
                foreach (var ev in events)
                {
                    ev.Start.DateTime = DateTime.Parse(ev.Start.DateTime).ToLocalTime().ToString();
                    ev.Start.TimeZone = TimeZoneInfo.Local.Id;
                    ev.End.DateTime = DateTime.Parse(ev.End.DateTime).ToLocalTime().ToString();
                    ev.End.TimeZone = TimeZoneInfo.Local.Id;
                }

                return Json(events, JsonRequestBehavior.AllowGet);
            }
        }
    }
    ```

1. <span data-ttu-id="29c59-117">Inicie la aplicación, inicie sesión y haga clic en el vínculo de **calendario** en la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="29c59-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="29c59-118">Si todo funciona, debería ver un volcado JSON de eventos en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="29c59-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="29c59-119">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="29c59-119">Display the results</span></span>

<span data-ttu-id="29c59-120">Ahora puede Agregar una vista para mostrar los resultados de forma más fácil de uso.</span><span class="sxs-lookup"><span data-stu-id="29c59-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="29c59-121">En el explorador de soluciones, haga clic con el botón secundario en la carpeta **vistas/calendario** y seleccione **Agregar > vista...**. Asigne un nombre `Index` a la vista y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="29c59-121">In Solution Explorer, right-click the **Views/Calendar** folder and select **Add > View...**. Name the view `Index` and select **Add**.</span></span> <span data-ttu-id="29c59-122">Reemplace todo el contenido del nuevo archivo por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="29c59-122">Replace the entire contents of the new file with the following code.</span></span>

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

    <span data-ttu-id="29c59-123">Se recorrerá en bucle una colección de eventos y se agregará una fila de tabla para cada uno.</span><span class="sxs-lookup"><span data-stu-id="29c59-123">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="29c59-124">Quite la `return Json(events, JsonRequestBehavior.AllowGet);` línea de la `Index` función en `Controllers/CalendarController.cs`y reemplácela por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="29c59-124">Remove the `return Json(events, JsonRequestBehavior.AllowGet);` line from the `Index` function in `Controllers/CalendarController.cs`, and replace it with the following code.</span></span>

    ```cs
    return View(events);
    ```

1. <span data-ttu-id="29c59-125">Inicie la aplicación, inicie sesión y haga clic en el vínculo **calendario** .</span><span class="sxs-lookup"><span data-stu-id="29c59-125">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="29c59-126">La aplicación ahora debería representar una tabla de eventos.</span><span class="sxs-lookup"><span data-stu-id="29c59-126">The app should now render a table of events.</span></span>

    ![Captura de pantalla de la tabla de eventos](./images/add-msgraph-01.png)

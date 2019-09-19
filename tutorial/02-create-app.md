<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="2d03e-101">Empiece por crear un proyecto de MVC de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="2d03e-101">Start by creating an ASP.NET MVC project.</span></span>

1. <span data-ttu-id="2d03e-102">Abra Visual Studio y seleccione **crear un nuevo proyecto**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-102">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="2d03e-103">En el cuadro de diálogo **crear nuevo proyecto** , elija la opción **aplicación Web de ASP.net (.NET Framework)** que usa C# y, a continuación, seleccione **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-103">In the **Create new project** dialog, choose the **ASP.NET Web Application (.NET Framework)** option that uses C#, then select **Next**.</span></span>

    ![Cuadro de diálogo Crear nuevo proyecto de Visual Studio 2019](./images/vs-create-new-project.png)

1. <span data-ttu-id="2d03e-105">Escriba `graph-tutorial` en el campo **nombre del proyecto** y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-105">Enter `graph-tutorial` in the **Project name** field and select **Create**.</span></span>

    ![Cuadro de diálogo Configurar nuevo proyecto de Visual Studio 2019](./images/vs-configure-new-project.png)

    > [!NOTE]
    > <span data-ttu-id="2d03e-107">Asegúrese de que escribe exactamente el mismo nombre para el proyecto de Visual Studio que se especifica en estas instrucciones de la práctica.</span><span class="sxs-lookup"><span data-stu-id="2d03e-107">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="2d03e-108">El nombre del proyecto de Visual Studio se convierte en parte del espacio de nombres en el código.</span><span class="sxs-lookup"><span data-stu-id="2d03e-108">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="2d03e-109">El código incluido en estas instrucciones depende del espacio de nombres que coincida con el nombre de proyecto de Visual Studio especificado en estas instrucciones.</span><span class="sxs-lookup"><span data-stu-id="2d03e-109">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="2d03e-110">Si usa un nombre de proyecto diferente, el código no se compilará a menos que ajuste todos los espacios de nombres para que se correspondan con el nombre del proyecto de Visual Studio que ha especificado al crear el proyecto.</span><span class="sxs-lookup"><span data-stu-id="2d03e-110">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

1. <span data-ttu-id="2d03e-111">Elija **MVC** y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-111">Choose **MVC** and select **Create**.</span></span>

    ![Visual Studio 2019 cuadro de diálogo Crear nueva aplicación Web de ASP.NET](./images/vs-create-new-asp-app.png)

1. <span data-ttu-id="2d03e-113">Presione **F5** o seleccione **depurar > iniciar depuración**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-113">Press **F5** or select **Debug > Start Debugging**.</span></span> <span data-ttu-id="2d03e-114">Si todo funciona, el explorador predeterminado debe abrir y mostrar una página ASP.NET predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2d03e-114">If everything is working, your default browser should open and display a default ASP.NET page.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="2d03e-115">Agregar paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="2d03e-115">Add NuGet packages</span></span>

<span data-ttu-id="2d03e-116">Antes de continuar, actualice el `bootstrap` paquete Nuget e instale algunos paquetes Nuget adicionales que usará más adelante.</span><span class="sxs-lookup"><span data-stu-id="2d03e-116">Before moving on, update the `bootstrap` NuGet package, and install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="2d03e-117">[Microsoft. Owin. host. SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) para habilitar las interfaces [Owin](http://owin.org/) en la aplicación ASP.net.</span><span class="sxs-lookup"><span data-stu-id="2d03e-117">[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) to enable the [OWIN](http://owin.org/) interfaces in the ASP.NET application.</span></span>
- <span data-ttu-id="2d03e-118">[Microsoft. Owin. Security. OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) para realizar la autenticación de OpenID Connect con Azure.</span><span class="sxs-lookup"><span data-stu-id="2d03e-118">[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) for doing OpenID Connect authentication with Azure.</span></span>
- <span data-ttu-id="2d03e-119">[Microsoft. Owin. Security. cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) para habilitar la autenticación basada en cookies.</span><span class="sxs-lookup"><span data-stu-id="2d03e-119">[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) to enable cookie-based authentication.</span></span>
- <span data-ttu-id="2d03e-120">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) para solicitar y administrar tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="2d03e-120">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="2d03e-121">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2d03e-121">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="2d03e-122">Seleccione **herramientas > el administrador de paquetes de NuGet > consola del administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-122">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>
1. <span data-ttu-id="2d03e-123">En la consola del administrador de paquetes, escriba los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="2d03e-123">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Update-Package bootstrap
    Install-Package Microsoft.Owin.Host.SystemWeb
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Identity.Client -Version 4.3.1
    Install-Package Microsoft.Graph -Version 1.17.0
    ```

## <a name="design-the-app"></a><span data-ttu-id="2d03e-124">Diseñar la aplicación</span><span class="sxs-lookup"><span data-stu-id="2d03e-124">Design the app</span></span>

<span data-ttu-id="2d03e-125">En esta sección, creará la estructura básica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2d03e-125">In this section you will create the basic structure of the application.</span></span>

1. <span data-ttu-id="2d03e-126">Crear una clase básica de inicio OWIN.</span><span class="sxs-lookup"><span data-stu-id="2d03e-126">Create a basic OWIN startup class.</span></span> <span data-ttu-id="2d03e-127">Haga clic con el `graph-tutorial` botón secundario en la carpeta en el explorador de soluciones y seleccione **Agregar > nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-127">Right-click the `graph-tutorial` folder in Solution Explorer and select **Add > New Item**.</span></span> <span data-ttu-id="2d03e-128">Elija la plantilla de **clase de inicio de OWIN** , `Startup.cs`asigne un nombre al archivo y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-128">Choose the **OWIN Startup Class** template, name the file `Startup.cs`, and select **Add**.</span></span>

1. <span data-ttu-id="2d03e-129">Haga clic con el botón secundario en la carpeta **modelos** en el explorador de soluciones y seleccione **Agregar > clase...**. Asigne un nombre `Alert` a la clase y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-129">Right-click the **Models** folder in Solution Explorer and select **Add > Class...**. Name the class `Alert` and select **Add**.</span></span> <span data-ttu-id="2d03e-130">Reemplace todo el contenido de `Alert.cs` con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="2d03e-130">Replace the entire contents of `Alert.cs` with the following code.</span></span>

    ```cs
    namespace graph_tutorial.Models
    {
        // Used to flash error messages in the app's views.
        public class Alert
        {
            public const string AlertKey = "TempDataAlerts";
            public string Message { get; set; }
            public string Debug { get; set; }
        }
    }
    ```

1. <span data-ttu-id="2d03e-131">Abra el `./Views/Shared/_Layout.cshtml` archivo y reemplace todo el contenido por el código siguiente para actualizar el diseño global de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2d03e-131">Open the `./Views/Shared/_Layout.cshtml` file, and replace its entire contents with the following code to update the global layout of the app.</span></span>

    ```html
    @{
        var alerts = TempData.ContainsKey(graph_tutorial.Models.Alert.AlertKey) ?
            (List<graph_tutorial.Models.Alert>)TempData[graph_tutorial.Models.Alert.AlertKey] :
            new List<graph_tutorial.Models.Alert>();
    }

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>ASP.NET Graph Tutorial</title>
        @Styles.Render("~/Content/css")
        @Scripts.Render("~/bundles/modernizr")

        <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
              integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt"
              crossorigin="anonymous">
    </head>

    <body>
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <div class="container">
                @Html.ActionLink("ASP.NET Graph Tutorial", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
                        aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="collapse navbar-collapse" id="navbarCollapse">
                    <ul class="navbar-nav mr-auto">
                        <li class="nav-item">
                            @Html.ActionLink("Home", "Index", "Home", new { area = "" },
                                new { @class = ViewBag.Current == "Home" ? "nav-link active" : "nav-link" })
                        </li>
                        @if (Request.IsAuthenticated)
                        {
                            <li class="nav-item" data-turbolinks="false">
                                @Html.ActionLink("Calendar", "Index", "Calendar", new { area = "" },
                                    new { @class = ViewBag.Current == "Calendar" ? "nav-link active" : "nav-link" })
                            </li>
                        }
                    </ul>
                    <ul class="navbar-nav justify-content-end">
                        <li class="nav-item">
                            <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                                <i class="fas fa-external-link-alt mr-1"></i>Docs
                            </a>
                        </li>
                        @if (Request.IsAuthenticated)
                        {
                            <li class="nav-item dropdown">
                                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                                    @if (!string.IsNullOrEmpty(ViewBag.User.Avatar))
                                    {
                                        <img src="@ViewBag.User.Avatar" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                                    }
                                    else
                                    {
                                        <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                                    }
                                </a>
                                <div class="dropdown-menu dropdown-menu-right">
                                    <h5 class="dropdown-item-text mb-0">@ViewBag.User.DisplayName</h5>
                                    <p class="dropdown-item-text text-muted mb-0">@ViewBag.User.Email</p>
                                    <div class="dropdown-divider"></div>
                                    @Html.ActionLink("Sign Out", "SignOut", "Account", new { area = "" }, new { @class = "dropdown-item" })
                                </div>
                            </li>
                        }
                        else
                        {
                            <li class="nav-item">
                                @Html.ActionLink("Sign In", "SignIn", "Account", new { area = "" }, new { @class = "nav-link" })
                            </li>
                        }
                    </ul>
                </div>
            </div>
        </nav>
        <main role="main" class="container">
            @foreach (var alert in alerts)
            {
                <div class="alert alert-danger" role="alert">
                    <p class="mb-3">@alert.Message</p>
                    @if (!string.IsNullOrEmpty(alert.Debug))
                    {
                        <pre class="alert-pre border bg-light p-2"><code>@alert.Debug</code></pre>
                    }
                </div>
            }

            @RenderBody()
        </main>
        @Scripts.Render("~/bundles/jquery")
        @Scripts.Render("~/bundles/bootstrap")
        @RenderSection("scripts", required: false)
    </body>
    </html>
    ```

    > [!NOTE]
    > <span data-ttu-id="2d03e-132">Este código agrega un [bootstrap](https://getbootstrap.com/) para los estilos sencillos y la [fuente maravilla](https://fontawesome.com/) para algunos iconos simples.</span><span class="sxs-lookup"><span data-stu-id="2d03e-132">This code adds [Bootstrap](https://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="2d03e-133">También define un diseño global con una barra de navegación y usa la `Alert` clase para mostrar las alertas.</span><span class="sxs-lookup"><span data-stu-id="2d03e-133">It also defines a global layout with a nav bar, and uses the `Alert` class to display any alerts.</span></span>

1. <span data-ttu-id="2d03e-134">Abra `Content/Site.css` y reemplace todo su contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="2d03e-134">Open `Content/Site.css` and replace its entire contents with the following code.</span></span>

    ```css
    body {
      padding-top: 4.5rem;
    }

    .alert-pre {
      word-wrap: break-word;
      word-break: break-all;
      white-space: pre-wrap;
    }
    ```

1. <span data-ttu-id="2d03e-135">Abra el `Views/Home/index.cshtml` archivo y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="2d03e-135">Open the `Views/Home/index.cshtml` file and replace its contents with the following.</span></span>

    ```html
    @{
        ViewBag.Current = "Home";
    }

    <div class="jumbotron">
        <h1>ASP.NET Graph Tutorial</h1>
        <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from ASP.NET</p>
        @if (Request.IsAuthenticated)
        {
            <h4>Welcome @ViewBag.User.DisplayName!</h4>
            <p>Use the navigation bar at the top of the page to get started.</p>
        }
        else
        {
            @Html.ActionLink("Click here to sign in", "SignIn", "Account", new { area = "" }, new { @class = "btn btn-primary btn-large" })
        }
    </div>
    ```

1. <span data-ttu-id="2d03e-136">Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y seleccione **Agregar > controlador..**.. Elija **controlador de MVC 5: vacío** y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-136">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="2d03e-137">Asigne un nombre `BaseController` al controlador y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="2d03e-137">Name the controller `BaseController` and select **Add**.</span></span> <span data-ttu-id="2d03e-138">Reemplace el contenido de `BaseController.cs` por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="2d03e-138">Replace the contents of `BaseController.cs` with the following code.</span></span>

    ```cs
    using graph_tutorial.Models;
    using System.Collections.Generic;
    using System.Web.Mvc;

    namespace graph_tutorial.Controllers
    {
        public abstract class BaseController : Controller
        {
            protected void Flash(string message, string debug=null)
            {
                var alerts = TempData.ContainsKey(Alert.AlertKey) ?
                    (List<Alert>)TempData[Alert.AlertKey] :
                    new List<Alert>();

                alerts.Add(new Alert
                {
                    Message = message,
                    Debug = debug
                });

                TempData[Alert.AlertKey] = alerts;
            }
        }
    }
    ```

1. <span data-ttu-id="2d03e-139">Abra `Controllers/HomeController.cs` y cambie la `public class HomeController : Controller` línea a:</span><span class="sxs-lookup"><span data-stu-id="2d03e-139">Open `Controllers/HomeController.cs` and change the `public class HomeController : Controller` line to:</span></span>

    ```cs
    public class HomeController : BaseController
    ```

1. <span data-ttu-id="2d03e-140">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="2d03e-140">Save all of your changes and restart the server.</span></span> <span data-ttu-id="2d03e-141">Ahora, la aplicación debe tener un aspecto muy diferente.</span><span class="sxs-lookup"><span data-stu-id="2d03e-141">Now, the app should look very different.</span></span>

    ![Una captura de pantalla de la Página principal rediseñada](./images/create-app-01.png)

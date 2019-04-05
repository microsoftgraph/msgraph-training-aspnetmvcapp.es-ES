<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5ff5d-101">Abra Visual Studio y seleccione **archivo _GT_ nuevo _GT_ proyecto**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-101">Open Visual Studio, and select **File > New > Project**.</span></span> <span data-ttu-id="5ff5d-102">En el cuadro de diálogo **nuevo proyecto** , haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5ff5d-102">In the **New Project** dialog, do the following:</span></span>

1. <span data-ttu-id="5ff5d-103">Seleccione **plantillas _GT_ Visual C# _GT_ web**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-103">Select **Templates > Visual C# > Web**.</span></span>
1. <span data-ttu-id="5ff5d-104">Seleccione **aplicación Web de ASP.net (.NET Framework)**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-104">Select **ASP.NET Web Application (.NET Framework)**.</span></span>
1. <span data-ttu-id="5ff5d-105">Escriba **gráfico: tutorial** para el nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-105">Enter **graph-tutorial** for the Name of the project.</span></span>

![Cuadro de diálogo Crear nuevo proyecto de Visual Studio 2017](./images/vs-new-project-01.png)

> [!NOTE]
> <span data-ttu-id="5ff5d-107">Asegúrese de que escribe exactamente el mismo nombre para el proyecto de Visual Studio que se especifica en estas instrucciones de la práctica.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-107">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="5ff5d-108">El nombre del proyecto de Visual Studio se convierte en parte del espacio de nombres en el código.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-108">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="5ff5d-109">El código incluido en estas instrucciones depende del espacio de nombres que coincida con el nombre de proyecto de Visual Studio especificado en estas instrucciones.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-109">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="5ff5d-110">Si usa un nombre de proyecto diferente, el código no se compilará a menos que ajuste todos los espacios de nombres para que se correspondan con el nombre del proyecto de Visual Studio que ha especificado al crear el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-110">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

<span data-ttu-id="5ff5d-111">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-111">Select **OK**.</span></span> <span data-ttu-id="5ff5d-112">En el cuadro de diálogo **nuevo proyecto de aplicación Web de ASP.net** , seleccione **MVC** (en **ASP.net 4.7.2 plantillas**) y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-112">In the **New ASP.NET Web Application Project** dialog, select **MVC** (under **ASP.NET 4.7.2 Templates**) and select **OK**.</span></span>

<span data-ttu-id="5ff5d-113">Presione **F5** o seleccione **depurar _GT_ iniciar**depuración.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-113">Press **F5** or select **Debug > Start Debugging**.</span></span> <span data-ttu-id="5ff5d-114">Si todo funciona, el explorador predeterminado debe abrir y mostrar una página ASP.NET predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-114">If everything is working, your default browser should open and display a default ASP.NET page.</span></span>

<span data-ttu-id="5ff5d-115">Antes de continuar, actualice el `bootstrap` paquete Nuget e instale algunos paquetes Nuget adicionales que usará más adelante.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-115">Before moving on, update the `bootstrap` NuGet package, and install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="5ff5d-116">[Microsoft. Owin. host. SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) para habilitar las interfaces [Owin](http://owin.org/) en la aplicación ASP.net.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-116">[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) to enable the [OWIN](http://owin.org/) interfaces in the ASP.NET application.</span></span>
- <span data-ttu-id="5ff5d-117">[Microsoft. Owin. Security. OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) para realizar la autenticación de OpenID Connect con Azure.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-117">[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) for doing OpenID Connect authentication with Azure.</span></span>
- <span data-ttu-id="5ff5d-118">[Microsoft. Owin. Security. cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) para habilitar la autenticación basada en cookies.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-118">[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) to enable cookie-based authentication.</span></span>
- <span data-ttu-id="5ff5d-119">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) para solicitar y administrar tokens de acceso.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-119">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="5ff5d-120">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-120">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="5ff5d-121">Seleccione **herramientas > el administrador de paquetes de NuGet _GT_ la consola del administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-121">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="5ff5d-122">En la consola del administrador de paquetes, escriba los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-122">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Update-Package bootstrap
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Identity.Client -Version 2.7.0
Install-Package Microsoft.Graph -Version 1.11.0
```

<span data-ttu-id="5ff5d-123">Crear una clase básica de inicio OWIN.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-123">Create a basic OWIN startup class.</span></span> <span data-ttu-id="5ff5d-124">Haga clic con el `graph-tutorial` botón secundario en la carpeta en el explorador de soluciones y elija **Agregar > nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-124">Right-click the `graph-tutorial` folder in Solution Explorer and choose **Add > New Item**.</span></span> <span data-ttu-id="5ff5d-125">Elija la plantilla de **clase de inicio de OWIN** , `Startup.cs`asigne un nombre al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-125">Choose the **OWIN Startup Class** template, name the file `Startup.cs`, and choose **Add**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="5ff5d-126">Diseñar la aplicación</span><span class="sxs-lookup"><span data-stu-id="5ff5d-126">Design the app</span></span>

<span data-ttu-id="5ff5d-127">Empiece por crear un modelo sencillo para un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-127">Start by creating a simple model for an error message.</span></span> <span data-ttu-id="5ff5d-128">Usaremos este modelo para los mensajes de error de Flash en las vistas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-128">You'll use this model to flash error messages in the app's views.</span></span>

<span data-ttu-id="5ff5d-129">Haga clic con el botón secundario en la carpeta **modelos** en el explorador de soluciones y elija **Agregar clase >...**. Asigne un nombre `Alert` a la clase y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-129">Right-click the **Models** folder in Solution Explorer and choose **Add > Class...**. Name the class `Alert` and choose **Add**.</span></span> <span data-ttu-id="5ff5d-130">Agregue el siguiente código en `Alert.cs`.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-130">Add the following code in `Alert.cs`.</span></span>

```cs
namespace graph_tutorial.Models
{
    public class Alert
    {
        public const string AlertKey = "TempDataAlerts";
        public string Message { get; set; }
        public string Debug { get; set; }
    }
}
```

<span data-ttu-id="5ff5d-131">Ahora, actualice el diseño global de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-131">Now update the global layout of the app.</span></span> <span data-ttu-id="5ff5d-132">Abra el `./Views/Shared/_Layout.cshtml` archivo y reemplace todo el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-132">Open the `./Views/Shared/_Layout.cshtml` file, and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="5ff5d-133">Este código agrega un [bootstrap](https://getbootstrap.com/) para los estilos sencillos y la [fuente maravilla](https://fontawesome.com/) para algunos iconos simples.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-133">This code adds [Bootstrap](https://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="5ff5d-134">También define un diseño global con una barra de navegación y usa la `Alert` clase para mostrar las alertas.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-134">It also defines a global layout with a nav bar, and uses the `Alert` class to display any alerts.</span></span>

<span data-ttu-id="5ff5d-135">Ahora, `Content/Site.css` abra y reemplace todo su contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-135">Now open `Content/Site.css` and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="5ff5d-136">Ahora, actualice la página predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-136">Now update the default page.</span></span> <span data-ttu-id="5ff5d-137">Abra el `Views/Home/index.cshtml` archivo y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-137">Open the `Views/Home/index.cshtml` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="5ff5d-138">Ahora, agregue una función auxiliar para crear una `Alert` y pásela a la vista.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-138">Now add a helper function to create an `Alert` and pass it to the view.</span></span> <span data-ttu-id="5ff5d-139">Para poder acceder fácilmente a cualquier controlador que haya creado, defina una clase de controlador base.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-139">In order to make it easily available to any controller we create, define a base controller class.</span></span>

<span data-ttu-id="5ff5d-140">Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y elija **Agregar controlador de >...** Elija **controlador de MVC 5: vacío** y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-140">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="5ff5d-141">Asigne un nombre `BaseController` al controlador y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-141">Name the controller `BaseController` and choose **Add**.</span></span> <span data-ttu-id="5ff5d-142">Reemplace el contenido de `BaseController.cs` por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-142">Replace the contents of `BaseController.cs` with the following code.</span></span>

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

<span data-ttu-id="5ff5d-143">Cualquier controlador puede heredar de esta clase base de controlador para obtener acceso `Flash` a la función.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-143">Any controller can inherit from this base controller class to gain access to the `Flash` function.</span></span> <span data-ttu-id="5ff5d-144">Actualice la `HomeController` clase de `BaseController`la que se hereda.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-144">Update the `HomeController` class to inherit from `BaseController`.</span></span> <span data-ttu-id="5ff5d-145">Abra `Controllers/HomeController.cs` y cambie la `public class HomeController : Controller` línea a:</span><span class="sxs-lookup"><span data-stu-id="5ff5d-145">Open `Controllers/HomeController.cs` and change the `public class HomeController : Controller` line to:</span></span>

```cs
public class HomeController : BaseController
```

<span data-ttu-id="5ff5d-146">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-146">Save all of your changes and restart the server.</span></span> <span data-ttu-id="5ff5d-147">Ahora, la aplicación debe tener un aspecto muy diferente.</span><span class="sxs-lookup"><span data-stu-id="5ff5d-147">Now, the app should look very different.</span></span>

![Una captura de pantalla de la Página principal rediseñada](./images/create-app-01.png)
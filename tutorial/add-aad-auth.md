<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6558a-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="6558a-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="6558a-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="6558a-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="6558a-103">En este paso, integrará el software intermedio OWIN y la biblioteca de la [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6558a-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

<span data-ttu-id="6558a-104">Haga clic con el botón secundario en el proyecto de **tutorial gráfico** en el explorador de soluciones y elija **Agregar > nuevo elemento..**.. Elija el **archivo de configuración Web**, asigne `PrivateSettings.config` un nombre al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="6558a-104">Right-click the **graph-tutorial** project in Solution Explorer and choose **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and choose **Add**.</span></span> <span data-ttu-id="6558a-105">Reemplace todo el contenido por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="6558a-105">Replace its entire contents with the following code.</span></span>

```xml
<appSettings>
    <add key="ida:AppID" value="YOUR APP ID" />
    <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
    <add key="ida:RedirectUri" value="http://localhost:PORT/" />
    <add key="ida:AppScopes" value="User.Read Calendars.Read" />
</appSettings>
```

<span data-ttu-id="6558a-106">Reemplace `YOUR_APP_ID_HERE` por el identificador de la aplicación del portal de registro de la `YOUR_APP_PASSWORD_HERE` aplicación y reemplace por la contraseña que ha generado.</span><span class="sxs-lookup"><span data-stu-id="6558a-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the password you generated.</span></span> <span data-ttu-id="6558a-107">Asegúrese también de modificar el `PORT` valor del `ida:RedirectUri` para que coincide con la dirección URL de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6558a-107">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6558a-108">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `PrivateSettings.config` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.</span><span class="sxs-lookup"><span data-stu-id="6558a-108">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="6558a-109">Actualización `Web.config` para cargar este nuevo archivo.</span><span class="sxs-lookup"><span data-stu-id="6558a-109">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="6558a-110">Reemplace `<appSettings>` (line 7) por lo siguiente</span><span class="sxs-lookup"><span data-stu-id="6558a-110">Replace the `<appSettings>` (line 7) with the following</span></span>

```xml
<appSettings file="PrivateSettings.config">
```

## <a name="implement-sign-in"></a><span data-ttu-id="6558a-111">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="6558a-111">Implement sign-in</span></span>

<span data-ttu-id="6558a-112">Para empezar, inicialice el middleware OWIN para usar la autenticación de Azure AD para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6558a-112">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span> <span data-ttu-id="6558a-113">Haga clic con el botón derecho en la carpeta **App_Start** en el explorador de soluciones y elija **Agregar clase >...**. Asigne un nombre `Startup.Auth.cs` al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="6558a-113">Right-click the **App_Start** folder in Solution Explorer and choose **Add > Class...**. Name the file `Startup.Auth.cs` and choose **Add**.</span></span> <span data-ttu-id="6558a-114">Reemplace todo el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6558a-114">Replace the entire contents with the following code.</span></span>

```cs
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;
using Microsoft.IdentityModel.Tokens;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.Notifications;
using Microsoft.Owin.Security.OpenIdConnect;
using Owin;
using System.Configuration;
using System.Threading.Tasks;
using System.Web;

namespace graph_tutorial
{
    public partial class Startup
    {
        // Load configuration settings from PrivateSettings.config
        private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
        private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
        private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
        private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

        public void ConfigureAuth(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());

            app.UseOpenIdConnectAuthentication(
              new OpenIdConnectAuthenticationOptions
              {
                  ClientId = appId,
                  Authority = "https://login.microsoftonline.com/common/v2.0",
                  Scope = $"openid email profile offline_access {graphScopes}",
                  RedirectUri = redirectUri,
                  PostLogoutRedirectUri = redirectUri,
                  TokenValidationParameters = new TokenValidationParameters
                  {
                      // For demo purposes only, see below
                      ValidateIssuer = false

                      // In a real multi-tenant app, you would add logic to determine whether the
                      // issuer was from an authorized tenant
                      //ValidateIssuer = true,
                      //IssuerValidator = (issuer, token, tvp) =>
                      //{
                      //  if (MyCustomTenantValidation(issuer))
                      //  {
                      //    return issuer;
                      //  }
                      //  else
                      //  {
                      //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
                      //  }
                      //}
                  },
                  Notifications = new OpenIdConnectAuthenticationNotifications
                  {
                      AuthenticationFailed = OnAuthenticationFailedAsync,
                      AuthorizationCodeReceived = OnAuthorizationCodeReceivedAsync
                  }
              }
            );
        }

        private static Task OnAuthenticationFailedAsync(AuthenticationFailedNotification<OpenIdConnectMessage,
          OpenIdConnectAuthenticationOptions> notification)
        {
            notification.HandleResponse();
            string redirect = $"/Home/Error?message={notification.Exception.Message}";
            if (notification.ProtocolMessage != null && !string.IsNullOrEmpty(notification.ProtocolMessage.ErrorDescription))
            {
                redirect += $"&debug={notification.ProtocolMessage.ErrorDescription}";
            }
            notification.Response.Redirect(redirect);
            return Task.FromResult(0);
        }

        private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
        {
            var idClient = new ConfidentialClientApplication(
                appId, redirectUri, new ClientCredential(appSecret), null, null);

            string message;
            string debug;

            try
            {
                string[] scopes = graphScopes.Split(' ');

                var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
                    notification.Code, scopes);

                message = "Access token retrieved.";
                debug = result.AccessToken;
            }
            catch (MsalException ex)
            {
                message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
                debug = ex.Message;
            }

            notification.HandleResponse();
            notification.Response.Redirect($"/Home/Error?message={message}&debug={debug}");
        }
    }
}
```

<span data-ttu-id="6558a-115">Este código configura el middleware OWIN con los valores de y define `PrivateSettings.config` dos métodos de devolución de llamada `OnAuthenticationFailedAsync` y `OnAuthorizationCodeReceivedAsync`.</span><span class="sxs-lookup"><span data-stu-id="6558a-115">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="6558a-116">Estos métodos de devolución de llamada se invocarán cuando el proceso de inicio de sesión vuelva de Azure.</span><span class="sxs-lookup"><span data-stu-id="6558a-116">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

<span data-ttu-id="6558a-117">Ahora, actualice `Startup.cs` el archivo para llamar `ConfigureAuth` al método.</span><span class="sxs-lookup"><span data-stu-id="6558a-117">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="6558a-118">Reemplace todo el contenido de `Startup.cs` con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6558a-118">Replace the entire contents of `Startup.cs` with the following code.</span></span>

```cs
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(graph_tutorial.Startup))]

namespace graph_tutorial
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

<span data-ttu-id="6558a-119">Agregue una `Error` acción a la `HomeController` clase para transformar los `message` parámetros `debug` de consulta y en `Alert` un objeto.</span><span class="sxs-lookup"><span data-stu-id="6558a-119">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="6558a-120">Abra `Controllers/HomeController.cs` y agregue la siguiente función.</span><span class="sxs-lookup"><span data-stu-id="6558a-120">Open `Controllers/HomeController.cs` and add the following function.</span></span>

```cs
public ActionResult Error(string message, string debug)
{
    Flash(message, debug);
    return RedirectToAction("Index");
}
```

<span data-ttu-id="6558a-121">Agregue un controlador para controlar el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-121">Add a controller to handle sign-in.</span></span> <span data-ttu-id="6558a-122">Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y elija **Agregar controlador de >...** Elija **controlador de MVC 5: vacío** y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="6558a-122">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="6558a-123">Asigne un nombre `AccountController` al controlador y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="6558a-123">Name the controller `AccountController` and choose **Add**.</span></span> <span data-ttu-id="6558a-124">Reemplace todo el contenido del archivo por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="6558a-124">Replace the entire contents of the file with the following code.</span></span>

```cs
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using System.Security.Claims;
using System.Web;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public class AccountController : Controller
    {
        public void SignIn()
        {
            if (!Request.IsAuthenticated)
            {
                // Signal OWIN to send an authorization request to Azure
                Request.GetOwinContext().Authentication.Challenge(
                    new AuthenticationProperties { RedirectUri = "/" },
                    OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }

        public ActionResult SignOut()
        {
            if (Request.IsAuthenticated)
            {
                Request.GetOwinContext().Authentication.SignOut(
                    CookieAuthenticationDefaults.AuthenticationType);
            }

            return RedirectToAction("Index", "Home");
        }
    }
}
```

<span data-ttu-id="6558a-125">Define una `SignIn` acción y `SignOut` .</span><span class="sxs-lookup"><span data-stu-id="6558a-125">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="6558a-126">La `SignIn` acción comprueba si la solicitud ya está autenticada.</span><span class="sxs-lookup"><span data-stu-id="6558a-126">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="6558a-127">Si no es así, invoca al middleware OWIN para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="6558a-127">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="6558a-128">La `SignOut` acción invoca al middleware OWIN para cerrar la sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-128">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

<span data-ttu-id="6558a-129">Guarde los cambios e inicie el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6558a-129">Save your changes and start the project.</span></span> <span data-ttu-id="6558a-130">Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a.</span><span class="sxs-lookup"><span data-stu-id="6558a-130">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="6558a-131">Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados.</span><span class="sxs-lookup"><span data-stu-id="6558a-131">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="6558a-132">El explorador redirige a la aplicación, que muestra el token.</span><span class="sxs-lookup"><span data-stu-id="6558a-132">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="6558a-133">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="6558a-133">Get user details</span></span>

<span data-ttu-id="6558a-134">Para empezar, cree un nuevo archivo que contenga todas las llamadas de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="6558a-134">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="6558a-135">Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y elija **Agregar > nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="6558a-135">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="6558a-136">Asigne un nombre `Helpers`a la carpeta.</span><span class="sxs-lookup"><span data-stu-id="6558a-136">Name the folder `Helpers`.</span></span> <span data-ttu-id="6558a-137">Haga clic con el botón secundario en esta nueva carpeta y elija **Agregar clase >...**. Asigne un nombre `GraphHelper.cs` al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="6558a-137">Right click this new folder and choose **Add > Class...**. Name the file `GraphHelper.cs` and choose **Add**.</span></span> <span data-ttu-id="6558a-138">Reemplace el contenido de este archivo con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6558a-138">Replace the contents of this file with the following code.</span></span>

```cs
using Microsoft.Graph;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace graph_tutorial.Helpers
{
    public static class GraphHelper
    {
        public static async Task<User> GetUserDetailsAsync(string accessToken)
        {
            var graphClient = new GraphServiceClient(
                new DelegateAuthenticationProvider(
                    async (requestMessage) =>
                    {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", accessToken);
                    }));

            return await graphClient.Me.Request().GetAsync();
        }
    }
}
```

<span data-ttu-id="6558a-139">Esto implementa la `GetUserDetails` función, que usa el SDK de Microsoft Graph para llamar al `/me` punto de conexión y devolver el resultado.</span><span class="sxs-lookup"><span data-stu-id="6558a-139">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="6558a-140">Actualice el `OnAuthorizationCodeReceivedAsync` método en `App_Start/Startup.Auth.cs` para llamar a esta función.</span><span class="sxs-lookup"><span data-stu-id="6558a-140">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="6558a-141">En primer lugar, agregue `using` la siguiente instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="6558a-141">First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.Helpers;
```

<span data-ttu-id="6558a-142">Reemplace el bloque `try` existente `OnAuthorizationCodeReceivedAsync` por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="6558a-142">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

```cs
try
{
    string[] scopes = graphScopes.Split(' ');

    var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
        notification.Code, scopes);

    var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

    string email = string.IsNullOrEmpty(userDetails.Mail) ?
        userDetails.UserPrincipalName : userDetails.Mail;

    message = "User info retrieved.";
    debug = $"User: {userDetails.DisplayName}, Email: {email}";
}
```

<span data-ttu-id="6558a-143">Ahora, si guarda los cambios e inicia la aplicación, después de iniciar sesión, verá el nombre y la dirección de correo electrónico del usuario en lugar del token de acceso.</span><span class="sxs-lookup"><span data-stu-id="6558a-143">Now if you save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="6558a-144">Almacenamiento de tokens</span><span class="sxs-lookup"><span data-stu-id="6558a-144">Storing the tokens</span></span>

<span data-ttu-id="6558a-145">Ahora que puede obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6558a-145">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="6558a-146">Como se trata de una aplicación de ejemplo, usaremos la sesión para almacenar los tokens.</span><span class="sxs-lookup"><span data-stu-id="6558a-146">Since this is a sample app, we'll use the session to store the tokens.</span></span> <span data-ttu-id="6558a-147">Una aplicación real usaría una solución de almacenamiento seguro más confiable, como una base de datos.</span><span class="sxs-lookup"><span data-stu-id="6558a-147">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="6558a-148">Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y elija **Agregar > nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="6558a-148">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="6558a-149">Asigne un nombre `TokenStorage`a la carpeta.</span><span class="sxs-lookup"><span data-stu-id="6558a-149">Name the folder `TokenStorage`.</span></span> <span data-ttu-id="6558a-150">Haga clic con el botón secundario en esta nueva carpeta y elija **Agregar clase >...**. Asigne un nombre `SessionTokenStore.cs` al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="6558a-150">Right click this new folder and choose **Add > Class...**. Name the file `SessionTokenStore.cs` and choose **Add**.</span></span> <span data-ttu-id="6558a-151">Reemplace el contenido de este archivo con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6558a-151">Replace the contents of this file with the following code.</span></span>

```cs
using Microsoft.Identity.Client;
using Newtonsoft.Json;
using System.Threading;
using System.Web;

namespace graph_tutorial.TokenStorage
{
    // Simple class to serialize into the session
    public class CachedUser
    {
        public string DisplayName { get; set; }
        public string Email { get; set; }
        public string Avatar { get; set; }
    }

    // Adapted from https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-v2
    public class SessionTokenStore
    {
        private static ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);
        private readonly string userId = string.Empty;
        private readonly string cacheId = string.Empty;
        private readonly string cachedUserId = string.Empty;
        private HttpContextBase httpContext = null;

        TokenCache tokenCache = new TokenCache();

        public SessionTokenStore(string userId, HttpContextBase httpContext)
        {
            this.userId = userId;
            cacheId = $"{userId}_TokenCache";
            cachedUserId = $"{userId}_UserCache";
            this.httpContext = httpContext;
            Load();
        }

        public TokenCache GetMsalCacheInstance()
        {
            tokenCache.SetBeforeAccess(BeforeAccessNotification);
            tokenCache.SetAfterAccess(AfterAccessNotification);
            Load();
            return tokenCache;
        }

        public bool HasData()
        {
            return (httpContext.Session[cacheId] != null && ((byte[])httpContext.Session[cacheId]).Length > 0);
        }

        public void Clear()
        {
            httpContext.Session.Remove(cacheId);
        }

        private void Load()
        {
            sessionLock.EnterReadLock();
            tokenCache.Deserialize((byte[])httpContext.Session[cacheId]);
            sessionLock.ExitReadLock();
        }

        private void Persist()
        {
            sessionLock.EnterReadLock();

            // Optimistically set HasStateChanged to false.
            // We need to do it early to avoid losing changes made by a concurrent thread.
            tokenCache.HasStateChanged = false;

            httpContext.Session[cacheId] = tokenCache.Serialize();
            sessionLock.ExitReadLock();
        }

        // Triggered right before MSAL needs to access the cache.
        private void BeforeAccessNotification(TokenCacheNotificationArgs args)
        {
            // Reload the cache from the persistent store in case it changed since the last access.
            Load();
        }

        // Triggered right after MSAL accessed the cache.
        private void AfterAccessNotification(TokenCacheNotificationArgs args)
        {
            // if the access operation resulted in a cache update
            if (tokenCache.HasStateChanged)
            {
                Persist();
            }
        }

        public void SaveUserDetails(CachedUser user)
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cachedUserId] = JsonConvert.SerializeObject(user);
            sessionLock.ExitReadLock();
        }

        public CachedUser GetUserDetails()
        {
            sessionLock.EnterReadLock();
            var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[cachedUserId]);
            sessionLock.ExitReadLock();
            return cachedUser;
        }
    }
}
```

<span data-ttu-id="6558a-152">Este código crea una `SessionTokenStore` clase que funciona con la clase de la `TokenCache` biblioteca de MSAL.</span><span class="sxs-lookup"><span data-stu-id="6558a-152">This code creates a `SessionTokenStore` class that works with the MSAL library's `TokenCache` class.</span></span> <span data-ttu-id="6558a-153">En este caso, la mayor parte del código implica la `TokenCache` serialización y deserialización de para la sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-153">Most of the code here involves serializing and deserializing the `TokenCache` to the session.</span></span> <span data-ttu-id="6558a-154">También proporciona una clase y métodos para serializar y deserializar los detalles del usuario en la sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-154">It also provides a class and methods to serialize and deserialize the user's details to the session.</span></span>

<span data-ttu-id="6558a-155">Ahora, agregue la siguiente `using` instrucción a la parte superior del `App_Start/Startup.Auth.cs` archivo.</span><span class="sxs-lookup"><span data-stu-id="6558a-155">Now, add the following `using` statement to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.IdentityModel.Claims;
```

<span data-ttu-id="6558a-156">Ahora, actualice `OnAuthorizationCodeReceivedAsync` la función para crear una instancia de `SessionTokenStore` la clase y proporcionarla al constructor del `ConfidentialClientApplication` objeto.</span><span class="sxs-lookup"><span data-stu-id="6558a-156">Now update the `OnAuthorizationCodeReceivedAsync` function to create an instance of the `SessionTokenStore` class and provide that to the constructor for the `ConfidentialClientApplication` object.</span></span> <span data-ttu-id="6558a-157">Esto hará que MSAL use la implementación de la memoria caché para almacenar los tokens.</span><span class="sxs-lookup"><span data-stu-id="6558a-157">That will cause MSAL to use your cache implementation for storing tokens.</span></span> <span data-ttu-id="6558a-158">Reemplace la función `OnAuthorizationCodeReceivedAsync` existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6558a-158">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

```cs
private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
{
    // Get the signed in user's id and create a token cache
    string signedInUserId = notification.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
    SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId,
        notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase);

    var idClient = new ConfidentialClientApplication(
        appId, redirectUri, new ClientCredential(appSecret), tokenStore.GetMsalCacheInstance(), null);

    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
            notification.Code, scopes);

        var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

        var cachedUser = new CachedUser()
        {
            DisplayName = userDetails.DisplayName,
            Email = string.IsNullOrEmpty(userDetails.Mail) ?
            userDetails.UserPrincipalName : userDetails.Mail,
            Avatar = string.Empty
        };

        tokenStore.SaveUserDetails(cachedUser);
    }
    catch (MsalException ex)
    {
        string message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
    catch(Microsoft.Graph.ServiceException ex)
    {
        string message = "GetUserDetailsAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
}
```

<span data-ttu-id="6558a-159">Para resumir los cambios:</span><span class="sxs-lookup"><span data-stu-id="6558a-159">To summarize the changes:</span></span>

- <span data-ttu-id="6558a-160">El código pasa ahora un `TokenCache` objeto al constructor para `ConfidentialClientApplication`.</span><span class="sxs-lookup"><span data-stu-id="6558a-160">The code now passes a `TokenCache` object to the constructor for `ConfidentialClientApplication`.</span></span> <span data-ttu-id="6558a-161">La biblioteca de MSAL administrará la lógica de almacenamiento de tokens y la actualizará cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="6558a-161">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
- <span data-ttu-id="6558a-162">El código pasa ahora los detalles del usuario obtenidos de Microsoft Graph al `SessionTokenStore` objeto que se va a almacenar en la sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-162">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
- <span data-ttu-id="6558a-163">Si se realiza correctamente, el código deja de redirigir, simplemente devuelve.</span><span class="sxs-lookup"><span data-stu-id="6558a-163">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="6558a-164">Esto permite al middleware OWIN completar el proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6558a-164">This allows the OWIN middleware to complete the authentication process.</span></span>

<span data-ttu-id="6558a-165">Como la caché de tokens se almacena en la sesión, `SignOut` actualice la `Controllers/AccountController.cs` acción en para borrar el almacén de tokens antes de cerrar la sesión. En primer lugar, agregue `using` la siguiente instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="6558a-165">Since the token cache is stored in the session, update the `SignOut` action in `Controllers/AccountController.cs` to clear the token store before signing out. First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
```

<span data-ttu-id="6558a-166">A continuación, reemplace la `SignOut` función existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6558a-166">Then, replace the existing `SignOut` function with the following.</span></span>

```cs
public ActionResult SignOut()
{
    if (Request.IsAuthenticated)
    {
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

        tokenStore.Clear();

        Request.GetOwinContext().Authentication.SignOut(
            CookieAuthenticationDefaults.AuthenticationType);
    }

    return RedirectToAction("Index", "Home");
}
```

<span data-ttu-id="6558a-167">Los detalles del usuario en caché son algo que necesita cada vista de la aplicación, por lo que `BaseController` debe actualizar la clase para que cargue esta información de la sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-167">The cached user details are something that every view in the application will need, so update the `BaseController` class to load this information from the session.</span></span> <span data-ttu-id="6558a-168">Abra `Controllers/BaseController.cs` y agregue las siguientes `using` instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="6558a-168">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.Security.Claims;
using System.Web;
using Microsoft.Owin.Security.Cookies;
```

<span data-ttu-id="6558a-169">A continuación, agregue la siguiente función.</span><span class="sxs-lookup"><span data-stu-id="6558a-169">Then add the following function.</span></span>

```cs
protected override void OnActionExecuting(ActionExecutingContext filterContext)
{
    if (Request.IsAuthenticated)
    {
        // Get the signed in user's id and create a token cache
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

        if (tokenStore.HasData())
        {
            // Add the user to the view bag
            ViewBag.User = tokenStore.GetUserDetails();
        }
        else
        {
            // The session has lost data. This happens often
            // when debugging. Log out so the user can log back in
            Request.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            filterContext.Result = RedirectToAction("Index", "Home");
        }
    }

    base.OnActionExecuting(filterContext);
}
```

<span data-ttu-id="6558a-170">Inicie el servidor y pase por el proceso de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-170">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="6558a-171">Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="6558a-171">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

<span data-ttu-id="6558a-173">Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** .</span><span class="sxs-lookup"><span data-stu-id="6558a-173">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="6558a-174">Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="6558a-174">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="6558a-176">Actualizar tokens</span><span class="sxs-lookup"><span data-stu-id="6558a-176">Refreshing tokens</span></span>

<span data-ttu-id="6558a-177">En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="6558a-177">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="6558a-178">Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.</span><span class="sxs-lookup"><span data-stu-id="6558a-178">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="6558a-179">Sin embargo, este token es de corta duración.</span><span class="sxs-lookup"><span data-stu-id="6558a-179">However, this token is short-lived.</span></span> <span data-ttu-id="6558a-180">El token expira una hora después de su emisión.</span><span class="sxs-lookup"><span data-stu-id="6558a-180">The token expires an hour after it is issued.</span></span> <span data-ttu-id="6558a-181">Aquí es donde el token de actualización se vuelve útil.</span><span class="sxs-lookup"><span data-stu-id="6558a-181">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="6558a-182">El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo.</span><span class="sxs-lookup"><span data-stu-id="6558a-182">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="6558a-183">Debido a que la aplicación usa la biblioteca de MSAL `TokenCache` y un objeto, no es necesario implementar ninguna lógica de actualización de tokens.</span><span class="sxs-lookup"><span data-stu-id="6558a-183">Because the app is using the MSAL library and a `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="6558a-184">El `ConfidentialClientApplication.AcquireTokenSilentAsync` método realiza toda la lógica por usted.</span><span class="sxs-lookup"><span data-stu-id="6558a-184">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="6558a-185">Primero comprueba el token almacenado en caché y, si no lo ha expirado, lo devuelve.</span><span class="sxs-lookup"><span data-stu-id="6558a-185">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="6558a-186">Si ha expirado, usa el token de actualización almacenado en caché para obtener uno nuevo.</span><span class="sxs-lookup"><span data-stu-id="6558a-186">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="6558a-187">Este método se utilizará en el siguiente módulo.</span><span class="sxs-lookup"><span data-stu-id="6558a-187">You'll use this method in the following module.</span></span>
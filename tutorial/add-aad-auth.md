<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="54e67-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="54e67-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="54e67-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="54e67-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="54e67-103">En este paso, integrará el software intermedio OWIN y la biblioteca de la [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="54e67-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

<span data-ttu-id="54e67-104">Haga clic con el botón secundario en el proyecto de **tutorial gráfico** en el explorador de soluciones y elija **Agregar > nuevo elemento..**.. Elija el **archivo de configuración Web**, asigne `PrivateSettings.config` un nombre al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="54e67-104">Right-click the **graph-tutorial** project in Solution Explorer and choose **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and choose **Add**.</span></span> <span data-ttu-id="54e67-105">Reemplace todo el contenido por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="54e67-105">Replace its entire contents with the following code.</span></span>

```xml
<appSettings>
    <add key="ida:AppID" value="YOUR APP ID" />
    <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
    <add key="ida:RedirectUri" value="http://localhost:PORT/" />
    <add key="ida:AppScopes" value="User.Read Calendars.Read" />
</appSettings>
```

<span data-ttu-id="54e67-106">Reemplace `YOUR_APP_ID_HERE` por el identificador de aplicación del portal de registro de aplicaciones y `YOUR_APP_PASSWORD_HERE` reemplace por el secreto de cliente que ha generado.</span><span class="sxs-lookup"><span data-stu-id="54e67-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="54e67-107">Si el secreto de cliente contiene una y`&`comercial (), asegúrese de reemplazarlas `&amp;` por `PrivateSettings.config`en.</span><span class="sxs-lookup"><span data-stu-id="54e67-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="54e67-108">Asegúrese también de modificar el `PORT` valor del `ida:RedirectUri` para que coincide con la dirección URL de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="54e67-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="54e67-109">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `PrivateSettings.config` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.</span><span class="sxs-lookup"><span data-stu-id="54e67-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="54e67-110">Actualización `Web.config` para cargar este nuevo archivo.</span><span class="sxs-lookup"><span data-stu-id="54e67-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="54e67-111">Reemplace `<appSettings>` (line 7) por lo siguiente</span><span class="sxs-lookup"><span data-stu-id="54e67-111">Replace the `<appSettings>` (line 7) with the following</span></span>

```xml
<appSettings file="PrivateSettings.config">
```

## <a name="implement-sign-in"></a><span data-ttu-id="54e67-112">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="54e67-112">Implement sign-in</span></span>

<span data-ttu-id="54e67-113">Para empezar, inicialice el middleware OWIN para usar la autenticación de Azure AD para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="54e67-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span> <span data-ttu-id="54e67-114">Haga clic con el botón derecho en la carpeta **App_Start** en el explorador de soluciones y elija **Agregar clase >...**. Asigne un nombre `Startup.Auth.cs` al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="54e67-114">Right-click the **App_Start** folder in Solution Explorer and choose **Add > Class...**. Name the file `Startup.Auth.cs` and choose **Add**.</span></span> <span data-ttu-id="54e67-115">Reemplace todo el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="54e67-115">Replace the entire contents with the following code.</span></span>

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

<span data-ttu-id="54e67-116">Este código configura el middleware OWIN con los valores de y define `PrivateSettings.config` dos métodos de devolución de llamada `OnAuthenticationFailedAsync` y `OnAuthorizationCodeReceivedAsync`.</span><span class="sxs-lookup"><span data-stu-id="54e67-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="54e67-117">Estos métodos de devolución de llamada se invocarán cuando el proceso de inicio de sesión vuelva de Azure.</span><span class="sxs-lookup"><span data-stu-id="54e67-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

<span data-ttu-id="54e67-118">Ahora, actualice `Startup.cs` el archivo para llamar `ConfigureAuth` al método.</span><span class="sxs-lookup"><span data-stu-id="54e67-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="54e67-119">Reemplace todo el contenido de `Startup.cs` con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="54e67-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

<span data-ttu-id="54e67-120">Agregue una `Error` acción a la `HomeController` clase para transformar los `message` parámetros `debug` de consulta y en `Alert` un objeto.</span><span class="sxs-lookup"><span data-stu-id="54e67-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="54e67-121">Abra `Controllers/HomeController.cs` y agregue la siguiente función.</span><span class="sxs-lookup"><span data-stu-id="54e67-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

```cs
public ActionResult Error(string message, string debug)
{
    Flash(message, debug);
    return RedirectToAction("Index");
}
```

<span data-ttu-id="54e67-122">Agregue un controlador para controlar el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="54e67-123">Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y elija **Agregar controlador de >...** Elija **controlador de MVC 5: vacío** y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="54e67-123">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="54e67-124">Asigne un nombre `AccountController` al controlador y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="54e67-124">Name the controller `AccountController` and choose **Add**.</span></span> <span data-ttu-id="54e67-125">Reemplace todo el contenido del archivo por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="54e67-125">Replace the entire contents of the file with the following code.</span></span>

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

<span data-ttu-id="54e67-126">Define una `SignIn` acción y `SignOut` .</span><span class="sxs-lookup"><span data-stu-id="54e67-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="54e67-127">La `SignIn` acción comprueba si la solicitud ya está autenticada.</span><span class="sxs-lookup"><span data-stu-id="54e67-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="54e67-128">Si no es así, invoca al middleware OWIN para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="54e67-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="54e67-129">La `SignOut` acción invoca al middleware OWIN para cerrar la sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

<span data-ttu-id="54e67-130">Guarde los cambios e inicie el proyecto.</span><span class="sxs-lookup"><span data-stu-id="54e67-130">Save your changes and start the project.</span></span> <span data-ttu-id="54e67-131">Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a.</span><span class="sxs-lookup"><span data-stu-id="54e67-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="54e67-132">Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados.</span><span class="sxs-lookup"><span data-stu-id="54e67-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="54e67-133">El explorador redirige a la aplicación, que muestra el token.</span><span class="sxs-lookup"><span data-stu-id="54e67-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="54e67-134">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="54e67-134">Get user details</span></span>

<span data-ttu-id="54e67-135">Para empezar, cree un nuevo archivo que contenga todas las llamadas de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="54e67-135">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="54e67-136">Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y elija **Agregar > nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="54e67-136">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="54e67-137">Asigne un nombre `Helpers`a la carpeta.</span><span class="sxs-lookup"><span data-stu-id="54e67-137">Name the folder `Helpers`.</span></span> <span data-ttu-id="54e67-138">Haga clic con el botón secundario en esta nueva carpeta y elija **Agregar clase >...**. Asigne un nombre `GraphHelper.cs` al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="54e67-138">Right click this new folder and choose **Add > Class...**. Name the file `GraphHelper.cs` and choose **Add**.</span></span> <span data-ttu-id="54e67-139">Reemplace el contenido de este archivo con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="54e67-139">Replace the contents of this file with the following code.</span></span>

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

<span data-ttu-id="54e67-140">Esto implementa la `GetUserDetails` función, que usa el SDK de Microsoft Graph para llamar al `/me` punto de conexión y devolver el resultado.</span><span class="sxs-lookup"><span data-stu-id="54e67-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="54e67-141">Actualice el `OnAuthorizationCodeReceivedAsync` método en `App_Start/Startup.Auth.cs` para llamar a esta función.</span><span class="sxs-lookup"><span data-stu-id="54e67-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="54e67-142">En primer lugar, agregue `using` la siguiente instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="54e67-142">First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.Helpers;
```

<span data-ttu-id="54e67-143">Reemplace el bloque `try` existente `OnAuthorizationCodeReceivedAsync` por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="54e67-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

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

<span data-ttu-id="54e67-144">Ahora, si guarda los cambios e inicia la aplicación, después de iniciar sesión, verá el nombre y la dirección de correo electrónico del usuario en lugar del token de acceso.</span><span class="sxs-lookup"><span data-stu-id="54e67-144">Now if you save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="54e67-145">Almacenamiento de tokens</span><span class="sxs-lookup"><span data-stu-id="54e67-145">Storing the tokens</span></span>

<span data-ttu-id="54e67-146">Ahora que puede obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="54e67-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="54e67-147">Como se trata de una aplicación de ejemplo, usaremos la sesión para almacenar los tokens.</span><span class="sxs-lookup"><span data-stu-id="54e67-147">Since this is a sample app, we'll use the session to store the tokens.</span></span> <span data-ttu-id="54e67-148">Una aplicación real usaría una solución de almacenamiento seguro más confiable, como una base de datos.</span><span class="sxs-lookup"><span data-stu-id="54e67-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="54e67-149">Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y elija **Agregar > nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="54e67-149">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="54e67-150">Asigne un nombre `TokenStorage`a la carpeta.</span><span class="sxs-lookup"><span data-stu-id="54e67-150">Name the folder `TokenStorage`.</span></span> <span data-ttu-id="54e67-151">Haga clic con el botón secundario en esta nueva carpeta y elija **Agregar clase >...**. Asigne un nombre `SessionTokenStore.cs` al archivo y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="54e67-151">Right click this new folder and choose **Add > Class...**. Name the file `SessionTokenStore.cs` and choose **Add**.</span></span> <span data-ttu-id="54e67-152">Reemplace el contenido de este archivo con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="54e67-152">Replace the contents of this file with the following code.</span></span>

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
            if (args.HasStateChanged)
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

<span data-ttu-id="54e67-153">Este código crea una `SessionTokenStore` clase que funciona con la clase de la `TokenCache` biblioteca de MSAL.</span><span class="sxs-lookup"><span data-stu-id="54e67-153">This code creates a `SessionTokenStore` class that works with the MSAL library's `TokenCache` class.</span></span> <span data-ttu-id="54e67-154">En este caso, la mayor parte del código implica la `TokenCache` serialización y deserialización de para la sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-154">Most of the code here involves serializing and deserializing the `TokenCache` to the session.</span></span> <span data-ttu-id="54e67-155">También proporciona una clase y métodos para serializar y deserializar los detalles del usuario en la sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-155">It also provides a class and methods to serialize and deserialize the user's details to the session.</span></span>

<span data-ttu-id="54e67-156">Ahora, agregue la siguiente `using` instrucción a la parte superior del `App_Start/Startup.Auth.cs` archivo.</span><span class="sxs-lookup"><span data-stu-id="54e67-156">Now, add the following `using` statement to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.IdentityModel.Claims;
```

<span data-ttu-id="54e67-157">Ahora, actualice `OnAuthorizationCodeReceivedAsync` la función para crear una instancia de `SessionTokenStore` la clase y proporcionarla al constructor del `ConfidentialClientApplication` objeto.</span><span class="sxs-lookup"><span data-stu-id="54e67-157">Now update the `OnAuthorizationCodeReceivedAsync` function to create an instance of the `SessionTokenStore` class and provide that to the constructor for the `ConfidentialClientApplication` object.</span></span> <span data-ttu-id="54e67-158">Esto hará que MSAL use la implementación de la memoria caché para almacenar los tokens.</span><span class="sxs-lookup"><span data-stu-id="54e67-158">That will cause MSAL to use your cache implementation for storing tokens.</span></span> <span data-ttu-id="54e67-159">Reemplace la función `OnAuthorizationCodeReceivedAsync` existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="54e67-159">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

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

<span data-ttu-id="54e67-160">Para resumir los cambios:</span><span class="sxs-lookup"><span data-stu-id="54e67-160">To summarize the changes:</span></span>

- <span data-ttu-id="54e67-161">El código pasa ahora un `TokenCache` objeto al constructor para `ConfidentialClientApplication`.</span><span class="sxs-lookup"><span data-stu-id="54e67-161">The code now passes a `TokenCache` object to the constructor for `ConfidentialClientApplication`.</span></span> <span data-ttu-id="54e67-162">La biblioteca de MSAL administrará la lógica de almacenamiento de tokens y la actualizará cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="54e67-162">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
- <span data-ttu-id="54e67-163">El código pasa ahora los detalles del usuario obtenidos de Microsoft Graph al `SessionTokenStore` objeto que se va a almacenar en la sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-163">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
- <span data-ttu-id="54e67-164">Si se realiza correctamente, el código deja de redirigir, simplemente devuelve.</span><span class="sxs-lookup"><span data-stu-id="54e67-164">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="54e67-165">Esto permite al middleware OWIN completar el proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="54e67-165">This allows the OWIN middleware to complete the authentication process.</span></span>

<span data-ttu-id="54e67-166">Como la caché de tokens se almacena en la sesión, `SignOut` actualice la `Controllers/AccountController.cs` acción en para borrar el almacén de tokens antes de cerrar la sesión. En primer lugar, agregue `using` la siguiente instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="54e67-166">Since the token cache is stored in the session, update the `SignOut` action in `Controllers/AccountController.cs` to clear the token store before signing out. First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
```

<span data-ttu-id="54e67-167">A continuación, reemplace la `SignOut` función existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="54e67-167">Then, replace the existing `SignOut` function with the following.</span></span>

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

<span data-ttu-id="54e67-168">Los detalles del usuario en caché son algo que necesita cada vista de la aplicación, por lo que `BaseController` debe actualizar la clase para que cargue esta información de la sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-168">The cached user details are something that every view in the application will need, so update the `BaseController` class to load this information from the session.</span></span> <span data-ttu-id="54e67-169">Abra `Controllers/BaseController.cs` y agregue las siguientes `using` instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="54e67-169">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.Security.Claims;
using System.Web;
using Microsoft.Owin.Security.Cookies;
```

<span data-ttu-id="54e67-170">A continuación, agregue la siguiente función.</span><span class="sxs-lookup"><span data-stu-id="54e67-170">Then add the following function.</span></span>

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

<span data-ttu-id="54e67-171">Inicie el servidor y pase por el proceso de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-171">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="54e67-172">Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="54e67-172">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

<span data-ttu-id="54e67-174">Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** .</span><span class="sxs-lookup"><span data-stu-id="54e67-174">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="54e67-175">Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="54e67-175">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="54e67-177">Actualizar tokens</span><span class="sxs-lookup"><span data-stu-id="54e67-177">Refreshing tokens</span></span>

<span data-ttu-id="54e67-178">En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="54e67-178">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="54e67-179">Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.</span><span class="sxs-lookup"><span data-stu-id="54e67-179">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="54e67-180">Sin embargo, este token es de corta duración.</span><span class="sxs-lookup"><span data-stu-id="54e67-180">However, this token is short-lived.</span></span> <span data-ttu-id="54e67-181">El token expira una hora después de su emisión.</span><span class="sxs-lookup"><span data-stu-id="54e67-181">The token expires an hour after it is issued.</span></span> <span data-ttu-id="54e67-182">Aquí es donde el token de actualización se vuelve útil.</span><span class="sxs-lookup"><span data-stu-id="54e67-182">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="54e67-183">El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo.</span><span class="sxs-lookup"><span data-stu-id="54e67-183">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="54e67-184">Debido a que la aplicación usa la biblioteca de MSAL `TokenCache` y un objeto, no es necesario implementar ninguna lógica de actualización de tokens.</span><span class="sxs-lookup"><span data-stu-id="54e67-184">Because the app is using the MSAL library and a `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="54e67-185">El `ConfidentialClientApplication.AcquireTokenSilentAsync` método realiza toda la lógica por usted.</span><span class="sxs-lookup"><span data-stu-id="54e67-185">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="54e67-186">Primero comprueba el token almacenado en caché y, si no lo ha expirado, lo devuelve.</span><span class="sxs-lookup"><span data-stu-id="54e67-186">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="54e67-187">Si ha expirado, usa el token de actualización almacenado en caché para obtener uno nuevo.</span><span class="sxs-lookup"><span data-stu-id="54e67-187">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="54e67-188">Este método se utilizará en el siguiente módulo.</span><span class="sxs-lookup"><span data-stu-id="54e67-188">You'll use this method in the following module.</span></span>
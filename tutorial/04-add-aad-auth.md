<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e111c-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="e111c-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="e111c-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a la API de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e111c-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="e111c-103">En este paso, integrará el software intermedio OWIN y la biblioteca de la [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e111c-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

1. <span data-ttu-id="e111c-104">Haga clic con el botón secundario en el proyecto de **tutorial gráfico** en el explorador de soluciones y seleccione **Agregar > nuevo elemento..**.. Elija el **archivo de configuración Web**, asigne `PrivateSettings.config` un nombre al archivo y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e111c-104">Right-click the **graph-tutorial** project in Solution Explorer and select **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and select **Add**.</span></span> <span data-ttu-id="e111c-105">Reemplace todo el contenido por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="e111c-105">Replace its entire contents with the following code.</span></span>

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    <span data-ttu-id="e111c-106">Reemplace `YOUR_APP_ID_HERE` por el identificador de aplicación del portal de registro de aplicaciones y `YOUR_APP_PASSWORD_HERE` reemplace por el secreto de cliente que ha generado.</span><span class="sxs-lookup"><span data-stu-id="e111c-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="e111c-107">Si el secreto de cliente contiene una y`&`comercial (), asegúrese de reemplazarlas `&amp;` por `PrivateSettings.config`en.</span><span class="sxs-lookup"><span data-stu-id="e111c-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="e111c-108">Asegúrese también de modificar el `PORT` valor del `ida:RedirectUri` para que coincide con la dirección URL de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e111c-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="e111c-109">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `PrivateSettings.config` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.</span><span class="sxs-lookup"><span data-stu-id="e111c-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="e111c-110">Actualización `Web.config` para cargar este nuevo archivo.</span><span class="sxs-lookup"><span data-stu-id="e111c-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="e111c-111">Reemplace `<appSettings>` (line 7) por lo siguiente</span><span class="sxs-lookup"><span data-stu-id="e111c-111">Replace the `<appSettings>` (line 7) with the following</span></span>

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="e111c-112">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="e111c-112">Implement sign-in</span></span>

<span data-ttu-id="e111c-113">Para empezar, inicialice el middleware OWIN para usar la autenticación de Azure AD para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e111c-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span>

1. <span data-ttu-id="e111c-114">Haga clic con el botón derecho en la carpeta **App_Start** en el explorador de soluciones y seleccione **Agregar > clase...**. Asigne un nombre `Startup.Auth.cs` al archivo y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e111c-114">Right-click the **App_Start** folder in Solution Explorer and select **Add > Class...**. Name the file `Startup.Auth.cs` and select **Add**.</span></span> <span data-ttu-id="e111c-115">Reemplace todo el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="e111c-115">Replace the entire contents with the following code.</span></span>

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
                var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                    .WithRedirectUri(redirectUri)
                    .WithClientSecret(appSecret)
                    .Build();

                string message;
                string debug;

                try
                {
                    string[] scopes = graphScopes.Split(' ');

                    var result = await idClient.AcquireTokenByAuthorizationCode(
                        scopes, notification.Code).ExecuteAsync();

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

    > [!NOTE]
    > <span data-ttu-id="e111c-116">Este código configura el middleware OWIN con los valores de y define `PrivateSettings.config` dos métodos de devolución de llamada `OnAuthenticationFailedAsync` y `OnAuthorizationCodeReceivedAsync`.</span><span class="sxs-lookup"><span data-stu-id="e111c-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="e111c-117">Estos métodos de devolución de llamada se invocarán cuando el proceso de inicio de sesión vuelva de Azure.</span><span class="sxs-lookup"><span data-stu-id="e111c-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

1. <span data-ttu-id="e111c-118">Ahora, actualice `Startup.cs` el archivo para llamar `ConfigureAuth` al método.</span><span class="sxs-lookup"><span data-stu-id="e111c-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="e111c-119">Reemplace todo el contenido de `Startup.cs` con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="e111c-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

1. <span data-ttu-id="e111c-120">Agregue una `Error` acción a la `HomeController` clase para transformar los `message` parámetros `debug` de consulta y en `Alert` un objeto.</span><span class="sxs-lookup"><span data-stu-id="e111c-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="e111c-121">Abra `Controllers/HomeController.cs` y agregue la siguiente función.</span><span class="sxs-lookup"><span data-stu-id="e111c-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. <span data-ttu-id="e111c-122">Agregue un controlador para controlar el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="e111c-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="e111c-123">Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y seleccione **Agregar > controlador..**.. Elija **controlador de MVC 5: vacío** y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e111c-123">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="e111c-124">Asigne un nombre `AccountController` al controlador y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e111c-124">Name the controller `AccountController` and select **Add**.</span></span> <span data-ttu-id="e111c-125">Reemplace todo el contenido del archivo por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="e111c-125">Replace the entire contents of the file with the following code.</span></span>

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

    <span data-ttu-id="e111c-126">Define una `SignIn` acción y `SignOut` .</span><span class="sxs-lookup"><span data-stu-id="e111c-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="e111c-127">La `SignIn` acción comprueba si la solicitud ya está autenticada.</span><span class="sxs-lookup"><span data-stu-id="e111c-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="e111c-128">Si no es así, invoca al middleware OWIN para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="e111c-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="e111c-129">La `SignOut` acción invoca al middleware OWIN para cerrar la sesión.</span><span class="sxs-lookup"><span data-stu-id="e111c-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

1. <span data-ttu-id="e111c-130">Guarde los cambios e inicie el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e111c-130">Save your changes and start the project.</span></span> <span data-ttu-id="e111c-131">Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a.</span><span class="sxs-lookup"><span data-stu-id="e111c-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="e111c-132">Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados.</span><span class="sxs-lookup"><span data-stu-id="e111c-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="e111c-133">El explorador redirige a la aplicación, que muestra el token.</span><span class="sxs-lookup"><span data-stu-id="e111c-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="e111c-134">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="e111c-134">Get user details</span></span>

<span data-ttu-id="e111c-135">Una vez que el usuario haya iniciado sesión, podrá obtener su información de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e111c-135">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="e111c-136">Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y seleccione **Agregar > nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="e111c-136">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="e111c-137">Asigne un nombre `Helpers`a la carpeta.</span><span class="sxs-lookup"><span data-stu-id="e111c-137">Name the folder `Helpers`.</span></span>

1. <span data-ttu-id="e111c-138">Haga clic con el botón secundario en esta nueva carpeta y seleccione **agregar > clase...**. Asigne un nombre `GraphHelper.cs` al archivo y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e111c-138">Right click this new folder and select **Add > Class...**. Name the file `GraphHelper.cs` and select **Add**.</span></span> <span data-ttu-id="e111c-139">Reemplace el contenido de este archivo con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="e111c-139">Replace the contents of this file with the following code.</span></span>

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

    <span data-ttu-id="e111c-140">Esto implementa la `GetUserDetails` función, que usa el SDK de Microsoft Graph para llamar al `/me` punto de conexión y devolver el resultado.</span><span class="sxs-lookup"><span data-stu-id="e111c-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="e111c-141">Actualice el `OnAuthorizationCodeReceivedAsync` método en `App_Start/Startup.Auth.cs` para llamar a esta función.</span><span class="sxs-lookup"><span data-stu-id="e111c-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="e111c-142">Agregue la siguiente `using` instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="e111c-142">Add the following `using` statement to the top of the file.</span></span>

    ```cs
    using graph_tutorial.Helpers;
    ```

1. <span data-ttu-id="e111c-143">Reemplace el bloque `try` existente `OnAuthorizationCodeReceivedAsync` por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="e111c-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

    ```cs
    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCode(
            scopes, notification.Code).ExecuteAsync();

        var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

        string email = string.IsNullOrEmpty(userDetails.Mail) ?
            userDetails.UserPrincipalName : userDetails.Mail;

        message = "User info retrieved.";
        debug = $"User: {userDetails.DisplayName}, Email: {email}";
    }
    ```

1. <span data-ttu-id="e111c-144">Guarde los cambios e inicie la aplicación; después de iniciar sesión, verá el nombre y la dirección de correo electrónico del usuario en lugar del token de acceso.</span><span class="sxs-lookup"><span data-stu-id="e111c-144">Save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="e111c-145">Almacenamiento de tokens</span><span class="sxs-lookup"><span data-stu-id="e111c-145">Storing the tokens</span></span>

<span data-ttu-id="e111c-146">Ahora que puede obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e111c-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="e111c-147">Como se trata de una aplicación de ejemplo, usará la sesión para almacenar los tokens.</span><span class="sxs-lookup"><span data-stu-id="e111c-147">Since this is a sample app, you will use the session to store the tokens.</span></span> <span data-ttu-id="e111c-148">Una aplicación real usaría una solución de almacenamiento seguro más confiable, como una base de datos.</span><span class="sxs-lookup"><span data-stu-id="e111c-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span> <span data-ttu-id="e111c-149">En esta sección, hará lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e111c-149">In this section, you will:</span></span>

- <span data-ttu-id="e111c-150">Implemente una clase de almacén de tokens para serializar y almacenar la memoria caché del token de MSAL y los detalles del usuario en la sesión de usuario.</span><span class="sxs-lookup"><span data-stu-id="e111c-150">Implement a token store class to serialize and store the MSAL token cache and the user's details in the user session.</span></span>
- <span data-ttu-id="e111c-151">Actualice el código de autenticación para usar la clase de almacén de tokens.</span><span class="sxs-lookup"><span data-stu-id="e111c-151">Update the authentication code to use the token store class.</span></span>
- <span data-ttu-id="e111c-152">Actualice la clase base del controlador para exponer los detalles del usuario almacenado a todas las vistas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e111c-152">Update the base controller class to expose the stored user details to all views in the application.</span></span>

1. <span data-ttu-id="e111c-153">Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y seleccione **Agregar > nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="e111c-153">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="e111c-154">Asigne un nombre `TokenStorage`a la carpeta.</span><span class="sxs-lookup"><span data-stu-id="e111c-154">Name the folder `TokenStorage`.</span></span>

1. <span data-ttu-id="e111c-155">Haga clic con el botón secundario en esta nueva carpeta y seleccione **agregar > clase...**. Asigne un nombre `SessionTokenStore.cs` al archivo y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e111c-155">Right click this new folder and select **Add > Class...**. Name the file `SessionTokenStore.cs` and select **Add**.</span></span> <span data-ttu-id="e111c-156">Reemplace el contenido de este archivo con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="e111c-156">Replace the contents of this file with the following code.</span></span>

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT license.

    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using System.Security.Claims;
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

        public class SessionTokenStore
        {
            private static readonly ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);

            private HttpContext httpContext = null;
            private string tokenCacheKey = string.Empty;
            private string userCacheKey = string.Empty;

            public SessionTokenStore(ITokenCache tokenCache, HttpContext context, ClaimsPrincipal user)
            {
                httpContext = context;

                if (tokenCache != null)
                {
                    tokenCache.SetBeforeAccess(BeforeAccessNotification);
                    tokenCache.SetAfterAccess(AfterAccessNotification);
                }

                var userId = GetUsersUniqueId(user);
                tokenCacheKey = $"{userId}_TokenCache";
                userCacheKey = $"{userId}_UserCache";
            }

            public bool HasData()
            {
                return (httpContext.Session[tokenCacheKey] != null &&
                    ((byte[])httpContext.Session[tokenCacheKey]).Length > 0);
            }

            public void Clear()
            {
                sessionLock.EnterWriteLock();

                try
                {
                    httpContext.Session.Remove(tokenCacheKey);
                }
                finally
                {
                    sessionLock.ExitWriteLock();
                }
            }

            private void BeforeAccessNotification(TokenCacheNotificationArgs args)
            {
                sessionLock.EnterReadLock();

                try
                {
                    // Load the cache from the session
                    args.TokenCache.DeserializeMsalV3((byte[])httpContext.Session[tokenCacheKey]);
                }
                finally
                {
                    sessionLock.ExitReadLock();
                }
            }

            private void AfterAccessNotification(TokenCacheNotificationArgs args)
            {
                if (args.HasStateChanged)
                {
                    sessionLock.EnterWriteLock();

                    try
                    {
                        // Store the serialized cache in the session
                        httpContext.Session[tokenCacheKey] = args.TokenCache.SerializeMsalV3();
                    }
                    finally
                    {
                        sessionLock.ExitWriteLock();
                    }
                }
            }

            public void SaveUserDetails(CachedUser user)
            {

                sessionLock.EnterWriteLock();
                httpContext.Session[userCacheKey] = JsonConvert.SerializeObject(user);
                sessionLock.ExitWriteLock();
            }

            public CachedUser GetUserDetails()
            {
                sessionLock.EnterReadLock();
                var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[userCacheKey]);
                sessionLock.ExitReadLock();
                return cachedUser;
            }

            private string GetUsersUniqueId(ClaimsPrincipal user)
            {
                // Combine the user's object ID with their tenant ID

                if (user != null)
                {
                    var userObjectId = user.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value ??
                        user.FindFirst("oid").Value;

                    var userTenantId = user.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value ??
                        user.FindFirst("tid").Value;

                    if (!string.IsNullOrEmpty(userObjectId) && !string.IsNullOrEmpty(userTenantId))
                    {
                        return $"{userObjectId}.{userTenantId}";
                    }
                }

                return null;
            }
        }
    }
    ```

1. <span data-ttu-id="e111c-157">Agregue la siguiente `using` instrucción a la parte superior del `App_Start/Startup.Auth.cs` archivo.</span><span class="sxs-lookup"><span data-stu-id="e111c-157">Add the following `using` statement to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. <span data-ttu-id="e111c-158">Reemplace la función `OnAuthorizationCodeReceivedAsync` existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="e111c-158">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

    ```cs
    private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
    {
        var idClient = ConfidentialClientApplicationBuilder.Create(appId)
            .WithRedirectUri(redirectUri)
            .WithClientSecret(appSecret)
            .Build();

        var signedInUser = new ClaimsPrincipal(notification.AuthenticationTicket.Identity);
        var tokenStore = new SessionTokenStore(idClient.UserTokenCache, HttpContext.Current, signedInUser);

        try
        {
            string[] scopes = graphScopes.Split(' ');

            var result = await idClient.AcquireTokenByAuthorizationCode(
                scopes, notification.Code).ExecuteAsync();

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
        catch (Microsoft.Graph.ServiceException ex)
        {
            string message = "GetUserDetailsAsync threw an exception";
            notification.HandleResponse();
            notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="e111c-159">Los cambios en esta nueva versión de `OnAuthorizationCodeReceivedAsync` hacen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e111c-159">The changes in this new version of `OnAuthorizationCodeReceivedAsync` do the following:</span></span>
    >
    > - <span data-ttu-id="e111c-160">El código ahora incluye la caché `ConfidentialClientApplication`de token de usuario predeterminada de la `SessionTokenStore` clase.</span><span class="sxs-lookup"><span data-stu-id="e111c-160">The code now wraps the `ConfidentialClientApplication`'s default user token cache with the `SessionTokenStore` class.</span></span> <span data-ttu-id="e111c-161">La biblioteca de MSAL administrará la lógica de almacenamiento de tokens y la actualizará cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="e111c-161">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
    > - <span data-ttu-id="e111c-162">El código pasa ahora los detalles del usuario obtenidos de Microsoft Graph al `SessionTokenStore` objeto que se va a almacenar en la sesión.</span><span class="sxs-lookup"><span data-stu-id="e111c-162">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
    > - <span data-ttu-id="e111c-163">Si se realiza correctamente, el código deja de redirigir, simplemente devuelve.</span><span class="sxs-lookup"><span data-stu-id="e111c-163">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="e111c-164">Esto permite al middleware OWIN completar el proceso de autenticación.</span><span class="sxs-lookup"><span data-stu-id="e111c-164">This allows the OWIN middleware to complete the authentication process.</span></span>

1. <span data-ttu-id="e111c-165">Actualice la `SignOut` acción para borrar el almacén de tokens antes de cerrar la sesión. Agregue la siguiente `using` instrucción a la parte superior `Controllers/AccountController.cs`de.</span><span class="sxs-lookup"><span data-stu-id="e111c-165">Update the `SignOut` action to clear the token store before signing out. Add the following `using` statement to the top of `Controllers/AccountController.cs`.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. <span data-ttu-id="e111c-166">Reemplace la función `SignOut` existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="e111c-166">Replace the existing `SignOut` function with the following.</span></span>

    ```cs
    public ActionResult SignOut()
    {
        if (Request.IsAuthenticated)
        {
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

            tokenStore.Clear();

            Request.GetOwinContext().Authentication.SignOut(
                CookieAuthenticationDefaults.AuthenticationType);
        }

        return RedirectToAction("Index", "Home");
    }
    ```

1. <span data-ttu-id="e111c-167">Abra `Controllers/BaseController.cs` y agregue las siguientes `using` instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="e111c-167">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. <span data-ttu-id="e111c-168">Agregue la siguiente función.</span><span class="sxs-lookup"><span data-stu-id="e111c-168">Add the following function.</span></span>

    ```cs
    protected override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        if (Request.IsAuthenticated)
        {
            // Get the user's token cache
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

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

1. <span data-ttu-id="e111c-169">Inicie el servidor y pase por el proceso de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="e111c-169">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="e111c-170">Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="e111c-170">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

1. <span data-ttu-id="e111c-172">Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** .</span><span class="sxs-lookup"><span data-stu-id="e111c-172">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="e111c-173">Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="e111c-173">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="e111c-175">Actualizar tokens</span><span class="sxs-lookup"><span data-stu-id="e111c-175">Refreshing tokens</span></span>

<span data-ttu-id="e111c-176">En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="e111c-176">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="e111c-177">Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.</span><span class="sxs-lookup"><span data-stu-id="e111c-177">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="e111c-178">Sin embargo, este token es de corta duración.</span><span class="sxs-lookup"><span data-stu-id="e111c-178">However, this token is short-lived.</span></span> <span data-ttu-id="e111c-179">El token expira una hora después de su emisión.</span><span class="sxs-lookup"><span data-stu-id="e111c-179">The token expires an hour after it is issued.</span></span> <span data-ttu-id="e111c-180">Aquí es donde el token de actualización se vuelve útil.</span><span class="sxs-lookup"><span data-stu-id="e111c-180">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="e111c-181">El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo.</span><span class="sxs-lookup"><span data-stu-id="e111c-181">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="e111c-182">Debido a que la aplicación usa la biblioteca de MSAL y la `TokenCache` serialización del objeto, no es necesario implementar ninguna lógica de actualización de tokens.</span><span class="sxs-lookup"><span data-stu-id="e111c-182">Because the app is using the MSAL library and serializing the `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="e111c-183">El `ConfidentialClientApplication.AcquireTokenSilentAsync` método realiza toda la lógica por usted.</span><span class="sxs-lookup"><span data-stu-id="e111c-183">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="e111c-184">Primero comprueba el token almacenado en caché y, si no lo ha expirado, lo devuelve.</span><span class="sxs-lookup"><span data-stu-id="e111c-184">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="e111c-185">Si ha expirado, usa el token de actualización almacenado en caché para obtener uno nuevo.</span><span class="sxs-lookup"><span data-stu-id="e111c-185">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="e111c-186">Este método se utilizará en el siguiente módulo.</span><span class="sxs-lookup"><span data-stu-id="e111c-186">You'll use this method in the following module.</span></span>

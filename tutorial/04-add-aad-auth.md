<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD. Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a la API de Microsoft Graph. En este paso, integrará el software intermedio OWIN y la biblioteca de la [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) en la aplicación.

1. Haga clic con el botón secundario en el proyecto de **tutorial gráfico** en el explorador de soluciones y seleccione **Agregar > nuevo elemento..**.. Elija el **archivo de configuración Web**, asigne `PrivateSettings.config` un nombre al archivo y seleccione **Agregar**. Reemplace todo el contenido por el siguiente código.

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="http://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    Reemplace `YOUR_APP_ID_HERE` por el identificador de aplicación del portal de registro de aplicaciones y `YOUR_APP_PASSWORD_HERE` reemplace por el secreto de cliente que ha generado. Si el secreto de cliente contiene una y`&`comercial (), asegúrese de reemplazarlas `&amp;` por `PrivateSettings.config`en. Asegúrese también de modificar el `PORT` valor del `ida:RedirectUri` para que coincide con la dirección URL de la aplicación.

    > [!IMPORTANT]
    > Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `PrivateSettings.config` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.

1. Actualización `Web.config` para cargar este nuevo archivo. Reemplace `<appSettings>` (line 7) por lo siguiente

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a>Implementar el inicio de sesión

Para empezar, inicialice el middleware OWIN para usar la autenticación de Azure AD para la aplicación.

1. Haga clic con el botón derecho en la carpeta **App_Start** en el explorador de soluciones y seleccione **Agregar > clase...**. Asigne un nombre `Startup.Auth.cs` al archivo y seleccione **Agregar**. Reemplace todo el contenido por el código siguiente.

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

    Este código configura el middleware OWIN con los valores de y define `PrivateSettings.config` dos métodos de devolución de llamada `OnAuthenticationFailedAsync` y `OnAuthorizationCodeReceivedAsync`. Estos métodos de devolución de llamada se invocarán cuando el proceso de inicio de sesión vuelva de Azure.

1. Ahora, actualice `Startup.cs` el archivo para llamar `ConfigureAuth` al método. Reemplace todo el contenido de `Startup.cs` con el código siguiente.

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

1. Agregue una `Error` acción a la `HomeController` clase para transformar los `message` parámetros `debug` de consulta y en `Alert` un objeto. Abra `Controllers/HomeController.cs` y agregue la siguiente función.

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. Agregue un controlador para controlar el inicio de sesión. Haga clic con el botón secundario en la carpeta **Controladores** en el explorador de soluciones y seleccione **Agregar > controlador..**.. Elija **controlador de MVC 5: vacío** y seleccione **Agregar**. Asigne un nombre `AccountController` al controlador y seleccione **Agregar**. Reemplace todo el contenido del archivo por el siguiente código.

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

    Define una `SignIn` acción y `SignOut` . La `SignIn` acción comprueba si la solicitud ya está autenticada. Si no es así, invoca al middleware OWIN para autenticar al usuario. La `SignOut` acción invoca al middleware OWIN para cerrar la sesión.

1. Guarde los cambios e inicie el proyecto. Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a. Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados. El explorador redirige a la aplicación, que muestra el token.

### <a name="get-user-details"></a>Obtener detalles del usuario

Una vez que el usuario haya iniciado sesión, podrá obtener su información de Microsoft Graph.

1. Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y seleccione **Agregar > nueva carpeta**. Asigne un nombre `Helpers`a la carpeta.

1. Haga clic con el botón secundario en esta nueva carpeta y seleccione **agregar > clase...**. Asigne un nombre `GraphHelper.cs` al archivo y seleccione **Agregar**. Reemplace el contenido de este archivo con el código siguiente.

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

    Esto implementa la `GetUserDetails` función, que usa el SDK de Microsoft Graph para llamar al `/me` punto de conexión y devolver el resultado.

1. Actualice el `OnAuthorizationCodeReceivedAsync` método en `App_Start/Startup.Auth.cs` para llamar a esta función. Agregue la siguiente `using` instrucción a la parte superior del archivo.

    ```cs
    using graph_tutorial.Helpers;
    ```

1. Reemplace el bloque `try` existente `OnAuthorizationCodeReceivedAsync` por el siguiente código.

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

1. Guarde los cambios e inicie la aplicación; después de iniciar sesión, verá el nombre y la dirección de correo electrónico del usuario en lugar del token de acceso.

## <a name="storing-the-tokens"></a>Almacenamiento de tokens

Ahora que puede obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación. Como se trata de una aplicación de ejemplo, usará la sesión para almacenar los tokens. Una aplicación real usaría una solución de almacenamiento seguro más confiable, como una base de datos. En esta sección, hará lo siguiente:

- Implemente una clase de almacén de tokens para serializar y almacenar la memoria caché del token de MSAL y los detalles del usuario en la sesión de usuario.
- Actualice el código de autenticación para usar la clase de almacén de tokens.
- Actualice la clase base del controlador para exponer los detalles del usuario almacenado a todas las vistas de la aplicación.

1. Haga clic con el botón secundario en la carpeta **Graph-tutorial** en el explorador de soluciones y seleccione **Agregar > nueva carpeta**. Asigne un nombre `TokenStorage`a la carpeta.

1. Haga clic con el botón secundario en esta nueva carpeta y seleccione **agregar > clase...**. Asigne un nombre `SessionTokenStore.cs` al archivo y seleccione **Agregar**. Reemplace el contenido de este archivo con el código siguiente.

    ```cs
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

        // Adapted from https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-v2
        public class SessionTokenStore
        {
            private static readonly ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);

            private readonly string userId = string.Empty;
            private readonly string cacheId = string.Empty;
            private readonly string cachedUserId = string.Empty;
            private HttpContext httpContext = null;
            private ITokenCache tokenCache;

            public SessionTokenStore(string userId, HttpContext httpcontext)
            {
                this.userId = userId;
                this.cacheId = $"{userId}_TokenCache";
                this.cachedUserId = $"{userId}_UserCache";
                this.httpContext = httpcontext;
            }

            public void Initialize(ITokenCache tokenCache)
            {
                this.tokenCache = tokenCache;
                this.tokenCache.SetBeforeAccess(BeforeAccessNotification);
                this.tokenCache.SetAfterAccess(AfterAccessNotification);
                Load();
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
                tokenCache.DeserializeMsalV3((byte[])httpContext.Session[cacheId]);
                sessionLock.ExitReadLock();
            }

            private void Persist()
            {
                sessionLock.EnterReadLock();
                httpContext.Session[cacheId] = tokenCache.SerializeMsalV3();
                sessionLock.ExitReadLock();
            }

            private void BeforeAccessNotification(TokenCacheNotificationArgs args)
            {
                Load();
            }

            private void AfterAccessNotification(TokenCacheNotificationArgs args)
            {
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

1. Agregue la siguiente `using` instrucción a la parte superior del `App_Start/Startup.Auth.cs` archivo.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.IdentityModel.Claims;
    ```

1. Reemplace la función `OnAuthorizationCodeReceivedAsync` existente por lo siguiente.

    ```cs
    private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
    {
        var idClient = ConfidentialClientApplicationBuilder.Create(appId)
            .WithRedirectUri(redirectUri)
            .WithClientSecret(appSecret)
            .Build();

        var signedInUserId = notification.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
        var tokenStore = new SessionTokenStore(signedInUserId, HttpContext.Current);
        tokenStore.Initialize(idClient.UserTokenCache);

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
    > Los cambios en esta nueva versión de `OnAuthorizationCodeReceivedAsync` hacen lo siguiente:
    >
    > - El código ahora incluye la caché `ConfidentialClientApplication`de token de usuario predeterminada de la `SessionTokenStore` clase. La biblioteca de MSAL administrará la lógica de almacenamiento de tokens y la actualizará cuando sea necesario.
    > - El código pasa ahora los detalles del usuario obtenidos de Microsoft Graph al `SessionTokenStore` objeto que se va a almacenar en la sesión.
    > - Si se realiza correctamente, el código deja de redirigir, simplemente devuelve. Esto permite al middleware OWIN completar el proceso de autenticación.

1. Actualice la `SignOut` acción para borrar el almacén de tokens antes de cerrar la sesión. Agregue la siguiente `using` instrucción a la parte superior `Controllers/AccountController.cs`de.

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. Reemplace la función `SignOut` existente por lo siguiente.

    ```cs
    public ActionResult SignOut()
    {
        if (Request.IsAuthenticated)
        {
            string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
            SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, System.Web.HttpContext.Current);

            tokenStore.Clear();

            Request.GetOwinContext().Authentication.SignOut(
                CookieAuthenticationDefaults.AuthenticationType);
        }

        return RedirectToAction("Index", "Home");
    }
    ```

1. Abra `Controllers/BaseController.cs` y agregue las siguientes `using` instrucciones en la parte superior del archivo.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. Agregue la siguiente función.

    ```cs
    protected override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        if (Request.IsAuthenticated)
        {
            // Get the signed in user's id and create a token cache
            string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
            SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, System.Web.HttpContext.Current);

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

1. Inicie el servidor y pase por el proceso de inicio de sesión. Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.

    ![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

1. Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** . Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.

    ![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualizar tokens

En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API. Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.

Sin embargo, este token es de corta duración. El token expira una hora después de su emisión. Aquí es donde el token de actualización se vuelve útil. El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo.

Debido a que la aplicación usa la biblioteca de MSAL `TokenCache` y un objeto, no es necesario implementar ninguna lógica de actualización de tokens. El `ConfidentialClientApplication.AcquireTokenSilentAsync` método realiza toda la lógica por usted. Primero comprueba el token almacenado en caché y, si no lo ha expirado, lo devuelve. Si ha expirado, usa el token de actualización almacenado en caché para obtener uno nuevo. Este método se utilizará en el siguiente módulo.

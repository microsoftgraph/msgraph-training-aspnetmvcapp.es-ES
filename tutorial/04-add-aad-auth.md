<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD. Esto es necesario para obtener el token de acceso OAuth necesario para llamar a la API de Microsoft Graph. En este paso, integrará el software intermedio OWIN y la biblioteca de la biblioteca de autenticación de [Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) en la aplicación.

1. Haga clic con el botón secundario en el proyecto **graph-tutorial** en el Explorador de soluciones y seleccione **Agregar > nuevo elemento...**. Elija **Archivo de configuración web,** asigne un nombre al archivo y seleccione `PrivateSettings.config` **Agregar**. Reemplace todo el contenido por el siguiente código.

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    Reemplace con el identificador de aplicación del Portal de registro de aplicaciones y reemplace por el secreto de `YOUR_APP_ID_HERE` `YOUR_APP_PASSWORD_HERE` cliente que generó. Si el secreto de cliente contiene alguna y comercial ( ), asegúrese de `&` reemplazarlas por `&amp;` . `PrivateSettings.config` Asegúrese también de modificar el `PORT` valor para que coincida con la dirección URL de la `ida:RedirectUri` aplicación.

    > [!IMPORTANT]
    > Si usas el control de código fuente como Git, ahora sería un buen momento para excluir el archivo del control de código fuente para evitar la pérdida involuntaria del identificador de la aplicación y la `PrivateSettings.config` contraseña.

1. Actualice `Web.config` para cargar este nuevo archivo. Reemplace la `<appSettings>` línea 7 por lo siguiente

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a>Implementar el inicio de sesión

Empiece por inicializar el software intermedio OWIN para usar la autenticación de Azure AD para la aplicación.

1. Haga clic con el botón **App_Start** en el Explorador de soluciones y seleccione **Agregar > clase...**. Asigne un nombre al `Startup.Auth.cs` archivo y seleccione **Agregar**. Reemplace todo el contenido por el código siguiente.

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
    > Este código configura el software intermedio OWIN con los valores de y define dos métodos de devolución de `PrivateSettings.config` llamada y `OnAuthenticationFailedAsync` `OnAuthorizationCodeReceivedAsync` . Estos métodos de devolución de llamada se invocarán cuando el proceso de inicio de sesión vuelva de Azure.

1. Ahora actualice el `Startup.cs` archivo para llamar al `ConfigureAuth` método. Reemplace todo el contenido por `Startup.cs` el código siguiente.

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

1. Agregue una `Error` acción a la clase para transformar los parámetros de consulta y los parámetros en un `HomeController` `message` `debug` `Alert` objeto. Abra `Controllers/HomeController.cs` y agregue la siguiente función.

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. Agregue un controlador para controlar el inicio de sesión. Haga clic con el botón secundario **en la carpeta Controladores** en el Explorador de soluciones y seleccione Agregar > **controlador...**. Elija **controlador MVC 5 - Vacío** y seleccione **Agregar**. Asigne un nombre al `AccountController` controlador y seleccione **Agregar**. Reemplace todo el contenido del archivo por el siguiente código.

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

    Esto define a `SignIn` y `SignOut` action. La `SignIn` acción comprueba si la solicitud ya está autenticada. Si no es así, invoca el software intermedio OWIN para autenticar al usuario. La `SignOut` acción invoca el software intermedio OWIN para cerrar sesión.

1. Guarde los cambios e inicie el proyecto. Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com` redirigirá. Inicie sesión con su cuenta de Microsoft y consiente los permisos solicitados. El explorador redirige a la aplicación mostrando el token.

### <a name="get-user-details"></a>Obtener detalles del usuario

Una vez que el usuario haya iniciado sesión, puede obtener su información de Microsoft Graph.

1. Haga clic con el botón secundario **en la carpeta graph-tutorial** en el Explorador de soluciones y seleccione **Agregar > nueva carpeta.** Asigne un nombre a la carpeta `Helpers` .

1. Haga clic con el botón secundario en esta nueva carpeta y **seleccione Agregar > clase...**. Asigne un nombre al `GraphHelper.cs` archivo y seleccione **Agregar**. Reemplace el contenido de este archivo por el código siguiente.

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
                        (requestMessage) =>
                        {
                            requestMessage.Headers.Authorization =
                                new AuthenticationHeaderValue("Bearer", accessToken);
                            return Task.FromResult(0);
                        }));

                return await graphClient.Me.Request().GetAsync();
            }
        }
    }
    ```

    Esto implementa la función, que usa el SDK de Microsoft Graph para llamar al punto de `GetUserDetails` conexión y devolver el `/me` resultado.

1. Actualice el `OnAuthorizationCodeReceivedAsync` método para llamar a esta `App_Start/Startup.Auth.cs` función. Agregue la siguiente `using` instrucción en la parte superior del archivo.

    ```cs
    using graph_tutorial.Helpers;
    ```

1. Reemplace el bloque `try` existente por el código `OnAuthorizationCodeReceivedAsync` siguiente.

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

1. Guarde los cambios e inicie la aplicación, después de iniciar sesión debería ver el nombre y la dirección de correo electrónico del usuario en lugar del token de acceso.

## <a name="storing-the-tokens"></a>Almacenar los tokens

Ahora que puedes obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación. Dado que se trata de una aplicación de ejemplo, usarás la sesión para almacenar los tokens. Una aplicación del mundo real usaría una solución de almacenamiento seguro más confiable, como una base de datos. En esta sección, podrá:

- Implemente una clase de almacén de tokens para serializar y almacenar la memoria caché de tokens de MSAL y los detalles del usuario en la sesión de usuario.
- Actualice el código de autenticación para usar la clase de almacén de tokens.
- Actualice la clase de controladora base para exponer los detalles de usuario almacenados a todas las vistas de la aplicación.

1. Haga clic con el botón secundario **en la carpeta graph-tutorial** en el Explorador de soluciones y seleccione **Agregar > nueva carpeta.** Asigne un nombre a la carpeta `TokenStorage` .

1. Haga clic con el botón secundario en esta nueva carpeta y **seleccione Agregar > clase...**. Asigne un nombre al `SessionTokenStore.cs` archivo y seleccione **Agregar**. Reemplace el contenido de este archivo por el código siguiente.

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

                    var userTenantId = user.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value ??
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

1. Agregue las siguientes `using` instrucciones en la parte superior del `App_Start/Startup.Auth.cs` archivo.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    ```

1. Reemplace la función `OnAuthorizationCodeReceivedAsync` existente por lo siguiente.

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
    > Los cambios en esta nueva versión de `OnAuthorizationCodeReceivedAsync` hacer lo siguiente:
    >
    > - El código ahora encapsula la memoria caché `ConfidentialClientApplication` de tokens de usuario predeterminada con la `SessionTokenStore` clase. La biblioteca de MSAL controlará la lógica de almacenamiento de los tokens y la actualizará cuando sea necesario.
    > - El código pasa ahora los detalles de usuario obtenidos de Microsoft Graph al `SessionTokenStore` objeto para almacenar en la sesión.
    > - Si se ejecuta correctamente, el código ya no redirige, solo devuelve. Esto permite que el software intermedio OWIN complete el proceso de autenticación.

1. Actualiza la `SignOut` acción para borrar el almacén de tokens antes de salir de sesión. Agregue la siguiente `using` instrucción al principio de `Controllers/AccountController.cs` .

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. Reemplace la función `SignOut` existente por lo siguiente.

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

1. Abra `Controllers/BaseController.cs` y agregue las siguientes instrucciones en la parte superior del `using` archivo.

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

1. Inicie el servidor y pase por el proceso de inicio de sesión. Debería volver a la página principal, pero la interfaz de usuario debe cambiar para indicar que ha iniciado sesión.

    ![Captura de pantalla de la página principal después de iniciar sesión](./images/add-aad-auth-01.png)

1. Haz clic en el avatar del usuario en la esquina superior derecha para acceder al vínculo **Cerrar** sesión. Al **hacer clic en** Cerrar sesión, se restablece la sesión y se vuelve a la página principal.

    ![Captura de pantalla del menú desplegable con el vínculo Cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualización de tokens

En este momento, la aplicación tiene un token de acceso, que se envía en el `Authorization` encabezado de las llamadas API. Este es el token que permite que la aplicación acceda a Microsoft Graph en nombre del usuario.

Sin embargo, este token es de corta duración. El token expira una hora después de su emisión. Aquí es donde el token de actualización resulta útil. El token de actualización permite a la aplicación solicitar un nuevo token de acceso sin necesidad de que el usuario vuelva a iniciar sesión.

Dado que la aplicación usa la biblioteca MSAL y serializa el objeto, no es necesario implementar ninguna lógica de actualización `TokenCache` de tokens. El `ConfidentialClientApplication.AcquireTokenSilentAsync` método realiza toda la lógica por usted. Primero comprueba el token almacenado en caché y, si no ha expirado, lo devuelve. Si ha expirado, usa el token de actualización en caché para obtener uno nuevo. Usará este método en el siguiente módulo.

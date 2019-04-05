# <a name="how-to-run-the-completed-project"></a>Cómo ejecutar el proyecto completado

## <a name="prerequisites"></a>Requisitos previos

Para ejecutar el proyecto completado en esta carpeta, necesita lo siguiente:

- [Visual Studio](https://visualstudio.microsoft.com/vs/) instalado en el equipo de desarrollo. Si no tiene Visual Studio, visite el vínculo anterior de opciones de descarga. (**Nota:** este tutorial se ha escrito con Visual Studio 2017 versión 15,81. Los pasos de esta guía pueden funcionar con otras versiones, pero no se han probado.
- Una cuenta de Microsoft personal con un buzón de correo en Outlook.com o una cuenta profesional o educativa de Microsoft.

Si no tiene una cuenta de Microsoft, hay un par de opciones para obtener una cuenta gratuita:

- Puede [registrarse para obtener una nueva cuenta Microsoft personal](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Puede [registrarse para el programa de desarrolladores de office 365](https://developer.microsoft.com/office/dev-program) para obtener una suscripción gratuita a Office 365.

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Registro de una aplicación web con el centro de administración de Azure Active Directory

1. Abra un explorador y vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com). Inicie sesión con una **cuenta personal** (también conocido como Microsoft Account) o una **cuenta profesional o educativa**.

1. Seleccione **Azure Active Directory** en el panel de navegación de la izquierda y, después, seleccione **registros de aplicaciones (vista previa)** en **administrar**.

    ![Una captura de pantalla de los registros de la aplicación ](/tutorial/images/aad-portal-app-registrations.png)

1. Seleccione **registro nuevo**. En la página **registrar una aplicación** , establezca los valores de la siguiente manera.

    - Establezca **el nombre** en `ASP.NET Graph Tutorial`.
    - Establezca **tipos de cuenta compatibles** en **cuentas de cualquier directorio de la organización y cuentas personales de Microsoft**.
    - En **URI**de redireccionamiento, establezca la primera lista desplegable para `Web` y establezca el `http://localhost:64107/`valor en.

    ![Captura de pantalla de la página registrar una aplicación](/tutorial/images/aad-register-an-app.png)

1. Elija **registrar**. En la página **tutorial de ASP.net Graph** , copie el valor del **identificador de la aplicación (cliente)** y guárdelo, lo necesitará en el paso siguiente.

    ![Captura de pantalla del identificador de la aplicación del nuevo registro de la aplicación](/tutorial/images/aad-application-id.png)

1. Seleccione **autenticación** en **administrar**. Busque la sección **concesión implícita** y habilite los tokens de **identificación**. Elija **Guardar**.

    ![Captura de pantalla de la sección de concesión implícita](/tutorial/images/aad-implicit-grant.png)

1. Seleccione **certificados & secretos** en **administrar**. Seleccione el botón **nuevo secreto de cliente** . Escriba un valor en **Descripción** y seleccione una de las opciones para **Expires** y elija **Agregar**.

    ![Captura de pantalla del cuadro de diálogo Agregar un secreto de cliente](/tutorial/images/aad-new-client-secret.png)

1. Copie el valor de secreto de cliente antes de salir de esta página. Lo necesitará en el paso siguiente.

    > [!IMPORTANT]
    > Este secreto de cliente no se vuelve a mostrar nunca, así que asegúrese de copiarlo ahora.

    ![Captura de pantalla del secreto de cliente recién agregado](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a>Configuración del ejemplo

1. Cambie el nombre `PrivateSettings.config.example` del archivo `PrivateSettings.config`a.
1. Edite `PrivateSettings.config` el archivo y realice los cambios siguientes.
    1. Reemplace `YOUR_APP_ID_HERE` por el **identificador de aplicación** que obtuvo desde el portal de registro de aplicaciones.
    1. Reemplace `YOUR_APP_PASSWORD_HERE` por la contraseña que obtuvo en el portal de registro de aplicaciones.
1. Abrir `graph-tutorial.sln` en Visual Studio. En el explorador de soluciones, haga clic con el botón secundario en la solución del **tutorial gráfico** y elija **restaurar paquetes NuGet**.

## <a name="run-the-sample"></a>Ejecutar el ejemplo

En Visual Studio, presione **F5** o elija **depurar _GT_ iniciar**depuración.
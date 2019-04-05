<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, creará un nuevo registro de aplicaciones Web de Azure AD con el centro de administración de Azure Active Directory.

1. DeTermine la dirección URL de la aplicación de ASP.NET. En el explorador de soluciones de Visual Studio, seleccione el proyecto **gráfico-tutorial** . En la ventana **propiedades** , busque el valor de **dirección URL**. Copie este valor.

    ![Captura de pantalla de la ventana Propiedades de Visual Studio](./images/vs-project-url.png)

1. Abra un explorador y vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com). Inicie sesión con una **cuenta personal** (también conocido como Microsoft Account) o una **cuenta profesional o educativa**.

1. Seleccione **Azure Active Directory** en el panel de navegación de la izquierda y, después, seleccione **registros de aplicaciones (vista previa)** en **administrar**.

    ![Una captura de pantalla de los registros de la aplicación ](./images/aad-portal-app-registrations.png)

1. Seleccione **registro nuevo**. En la página **registrar una aplicación** , establezca los valores de la siguiente manera.

    - Establezca **el nombre** en `ASP.NET Graph Tutorial`.
    - Establezca **tipos de cuenta compatibles** en **cuentas de cualquier directorio de la organización y cuentas personales de Microsoft**.
    - En **URI**de redireccionamiento, establezca la primera lista desplegable para `Web` y establezca el valor en la dirección URL de la aplicación de ASP.net que copió en el paso 1.

    ![Captura de pantalla de la página registrar una aplicación](./images/aad-register-an-app.png)

1. Elija **registrar**. En la página **tutorial de ASP.net Graph** , copie el valor del **identificador de la aplicación (cliente)** y guárdelo, lo necesitará en el paso siguiente.

    ![Captura de pantalla del identificador de la aplicación del nuevo registro de la aplicación](./images/aad-application-id.png)

1. Seleccione **autenticación** en **administrar**. Busque la sección **concesión implícita** y habilite los tokens de **identificación**. Elija **Guardar**.

    ![Captura de pantalla de la sección de concesión implícita](./images/aad-implicit-grant.png)

1. Seleccione **certificados & secretos** en **administrar**. Seleccione el botón **nuevo secreto de cliente** . Escriba un valor en **Descripción** y seleccione una de las opciones para **Expires** y elija **Agregar**.

    ![Captura de pantalla del cuadro de diálogo Agregar un secreto de cliente](./images/aad-new-client-secret.png)

1. Copie el valor de secreto de cliente antes de salir de esta página. Lo necesitará en el paso siguiente.

    > [!IMPORTANT]
    > Este secreto de cliente no se vuelve a mostrar nunca, así que asegúrese de copiarlo ahora.

    ![Captura de pantalla del secreto de cliente recién agregado](./images/aad-copy-client-secret.png)
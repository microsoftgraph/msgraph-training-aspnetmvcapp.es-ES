<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, creará un nuevo registro de aplicaciones Web de Azure AD mediante el portal de registro de aplicaciones (ARP).

1. Abra un explorador y vaya al [portal de registro de aplicaciones](https://apps.dev.microsoft.com). Inicie sesión con una **cuenta personal** (también conocido como Microsoft Account) o una **cuenta profesional o educativa**.

1. Seleccione **Agregar una aplicación** en la parte superior de la página.

    > [!NOTE]
    > Si ve más de un botón **Agregar una aplicación** en la página, seleccione el que corresponda a la lista de **aplicaciones convergentes** .

1. En la página **registrar la aplicación** , establezca el **nombre** de la aplicación en el **tutorial de ASP.net Graph** y seleccione **crear**.

    ![Captura de pantalla de la creación de una nueva aplicación en el sitio web del portal de registro de aplicaciones](./images/arp-create-app-01.png)

1. En la página **registro del tutorial de ASP.net Graph** , en la sección **propiedades** , copie el **identificador de aplicación** tal y como lo necesitará más adelante.

    ![Captura de pantalla del identificador de la aplicación recién creada](./images/arp-create-app-02.png)

1. Desplácese hacia abajo hasta la sección secretos de la **aplicación** .

    1. Seleccione **generar nueva contraseña**.
    1. En el cuadro de diálogo **nueva contraseña generada** , copie el contenido del cuadro tal y como lo necesitará más adelante.

        > **Importante:** Esta contraseña no se vuelve a mostrar nunca, por lo que debe asegurarse de que la copia ahora.

    ![Captura de pantalla de la contraseña de la aplicación recién creada](./images/arp-create-app-03.png)

1. DeTermine la dirección URL de la aplicación de ASP.NET. En el explorador de soluciones de Visual Studio, seleccione el proyecto **gráfico-tutorial** . En la ventana **propiedades** , busque el valor de **dirección URL**. Copie este valor.

    ![Captura de pantalla de la ventana Propiedades de Visual Studio](./images/vs-project-url.png)

1. Desplácese hacia abajo hasta la sección **plataformas** .

    1. Seleccione **Agregar plataforma**.
    1. En el cuadro de diálogo **Agregar plataforma** , seleccione **Web**.

        ![Captura de pantalla que crea una plataforma para la aplicación](./images/arp-create-app-04.png)

    1. En el cuadro plataforma **Web** , escriba la dirección URL que copió de las propiedades del proyecto de Visual Studio para las **direcciones URL**de redireccionamiento.

        ![Captura de pantalla de la plataforma web recién agregada para la aplicación](./images/arp-create-app-05.png)

1. Desplácese hasta la parte inferior de la página y seleccione **Guardar**.
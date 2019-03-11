# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="73183-101">Cómo ejecutar el proyecto completado</span><span class="sxs-lookup"><span data-stu-id="73183-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="73183-102">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="73183-102">Prerequisites</span></span>

<span data-ttu-id="73183-103">Para ejecutar el proyecto completado en esta carpeta, necesita lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="73183-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="73183-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) instalado en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="73183-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="73183-105">Si no tiene Visual Studio, visite el vínculo anterior de opciones de descarga.</span><span class="sxs-lookup"><span data-stu-id="73183-105">If you do not have Visual Studio, visit the previous link for download options.</span></span> <span data-ttu-id="73183-106">(**Nota:** este tutorial se ha escrito con Visual Studio 2017 versión 15,81.</span><span class="sxs-lookup"><span data-stu-id="73183-106">(**Note:** This tutorial was written with Visual Studio 2017 version 15.81.</span></span> <span data-ttu-id="73183-107">Los pasos de esta guía pueden funcionar con otras versiones, pero no se han probado.</span><span class="sxs-lookup"><span data-stu-id="73183-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="73183-108">Una cuenta de Microsoft personal con un buzón de correo en Outlook.com o una cuenta profesional o educativa de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="73183-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="73183-109">Si no tiene una cuenta de Microsoft, hay un par de opciones para obtener una cuenta gratuita:</span><span class="sxs-lookup"><span data-stu-id="73183-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="73183-110">Puede [registrarse para obtener una nueva cuenta Microsoft personal](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="73183-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="73183-111">Puede [registrarse para el programa de desarrolladores de office 365](https://developer.microsoft.com/office/dev-program) para obtener una suscripción gratuita a Office 365.</span><span class="sxs-lookup"><span data-stu-id="73183-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="73183-112">Registro de una aplicación web con el portal de registro de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="73183-112">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="73183-113">Abra un explorador y vaya al [portal de registro de aplicaciones](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="73183-113">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="73183-114">Inicie sesión con una **cuenta personal** (también conocido como Microsoft Account) o una **cuenta profesional o educativa**.</span><span class="sxs-lookup"><span data-stu-id="73183-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="73183-115">Seleccione **Agregar una aplicación** en la parte superior de la página.</span><span class="sxs-lookup"><span data-stu-id="73183-115">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="73183-116">**Nota:** Si ve más de un botón **Agregar una aplicación** en la página, seleccione el que corresponda a la lista de **aplicaciones convergentes** .</span><span class="sxs-lookup"><span data-stu-id="73183-116">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="73183-117">En la página **registrar la aplicación** , establezca el **nombre** de la aplicación en el **tutorial de ASP.net Graph** y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="73183-117">On the **Register your application** page, set the **Application Name** to **ASP.NET Graph Tutorial** and select **Create**.</span></span>

    ![Captura de pantalla de la creación de una nueva aplicación en el sitio web del portal de registro de aplicaciones](/tutorial/images/arp-create-app-01.png)

1. <span data-ttu-id="73183-119">En la página **registro del tutorial de ASP.net Graph** , en la sección **propiedades** , copie el **identificador de aplicación** tal y como lo necesitará más adelante.</span><span class="sxs-lookup"><span data-stu-id="73183-119">On the **ASP.NET Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Captura de pantalla del identificador de la aplicación recién creada](/tutorial/images/arp-create-app-02.png)

1. <span data-ttu-id="73183-121">Desplácese hacia abajo hasta la sección secretos de la **aplicación** .</span><span class="sxs-lookup"><span data-stu-id="73183-121">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="73183-122">Seleccione **generar nueva contraseña**.</span><span class="sxs-lookup"><span data-stu-id="73183-122">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="73183-123">En el cuadro de diálogo **nueva contraseña generada** , copie el contenido del cuadro tal y como lo necesitará más adelante.</span><span class="sxs-lookup"><span data-stu-id="73183-123">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="73183-124">**Importante:** Esta contraseña no se vuelve a mostrar nunca, por lo que debe asegurarse de que la copia ahora.</span><span class="sxs-lookup"><span data-stu-id="73183-124">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Captura de pantalla de la contraseña de la aplicación recién creada](/tutorial/images/arp-create-app-03.png)

1. <span data-ttu-id="73183-126">Desplácese hacia abajo hasta la sección **plataformas** .</span><span class="sxs-lookup"><span data-stu-id="73183-126">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="73183-127">Seleccione **Agregar plataforma**.</span><span class="sxs-lookup"><span data-stu-id="73183-127">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="73183-128">En el cuadro de diálogo **Agregar plataforma** , seleccione **Web**.</span><span class="sxs-lookup"><span data-stu-id="73183-128">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Captura de pantalla que crea una plataforma para la aplicación](/tutorial/images/arp-create-app-04.png)

    1. <span data-ttu-id="73183-130">En el cuadro plataforma **Web** , escriba la dirección `http://localhost:64107/` URL de las **direcciones URL**de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="73183-130">In the **Web** platform box, enter the URL `http://localhost:64107/` for the **Redirect URLs**.</span></span>

        ![Captura de pantalla de la plataforma web recién agregada para la aplicación](/tutorial/images/arp-create-app-05.png)

1. <span data-ttu-id="73183-132">Desplácese hasta la parte inferior de la página y seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="73183-132">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="73183-133">Configuración del ejemplo</span><span class="sxs-lookup"><span data-stu-id="73183-133">Configure the sample</span></span>

1. <span data-ttu-id="73183-134">Cambie el nombre `PrivateSettings.config.example` del archivo `PrivateSettings.config`a.</span><span class="sxs-lookup"><span data-stu-id="73183-134">Rename the `PrivateSettings.config.example` file to `PrivateSettings.config`.</span></span>
1. <span data-ttu-id="73183-135">Edite `PrivateSettings.config` el archivo y realice los cambios siguientes.</span><span class="sxs-lookup"><span data-stu-id="73183-135">Edit the `PrivateSettings.config` file and make the following changes.</span></span>
    1. <span data-ttu-id="73183-136">Reemplace `YOUR_APP_ID_HERE` por el **identificador de aplicación** que obtuvo desde el portal de registro de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="73183-136">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="73183-137">Reemplace `YOUR_APP_PASSWORD_HERE` por la contraseña que obtuvo en el portal de registro de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="73183-137">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="73183-138">Abrir `graph-tutorial.sln` en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73183-138">Open `graph-tutorial.sln` in Visual Studio.</span></span> <span data-ttu-id="73183-139">En el explorador de soluciones, haga clic con el botón secundario en la solución del **tutorial gráfico** y elija **restaurar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="73183-139">In Solution Explorer, right-click the **graph-tutorial** solution and choose **Restore NuGet Packages**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="73183-140">Ejecutar el ejemplo</span><span class="sxs-lookup"><span data-stu-id="73183-140">Run the sample</span></span>

<span data-ttu-id="73183-141">En Visual Studio, presione **F5** o elija **depurar _GT_ iniciar**depuración.</span><span class="sxs-lookup"><span data-stu-id="73183-141">In Visual Studio, press **F5** or choose **Debug > Start Debugging**.</span></span>
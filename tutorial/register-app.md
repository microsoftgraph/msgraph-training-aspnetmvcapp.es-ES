<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="71c67-101">En este ejercicio, creará un nuevo registro de aplicaciones Web de Azure AD mediante el portal de registro de aplicaciones (ARP).</span><span class="sxs-lookup"><span data-stu-id="71c67-101">In this exercise, you will create a new Azure AD web application registration using the Application Registry Portal (ARP).</span></span>

1. <span data-ttu-id="71c67-102">Abra un explorador y vaya al [portal de registro de aplicaciones](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="71c67-102">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="71c67-103">Inicie sesión con una **cuenta personal** (también conocido como Microsoft Account) o una **cuenta profesional o educativa**.</span><span class="sxs-lookup"><span data-stu-id="71c67-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="71c67-104">Seleccione **Agregar una aplicación** en la parte superior de la página.</span><span class="sxs-lookup"><span data-stu-id="71c67-104">Select **Add an app** at the top of the page.</span></span>

    > [!NOTE]
    > <span data-ttu-id="71c67-105">Si ve más de un botón **Agregar una aplicación** en la página, seleccione el que corresponda a la lista de **aplicaciones convergentes** .</span><span class="sxs-lookup"><span data-stu-id="71c67-105">If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="71c67-106">En la página **registrar la aplicación** , establezca el **nombre** de la aplicación en el **tutorial de ASP.net Graph** y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="71c67-106">On the **Register your application** page, set the **Application Name** to **ASP.NET Graph Tutorial** and select **Create**.</span></span>

    ![Captura de pantalla de la creación de una nueva aplicación en el sitio web del portal de registro de aplicaciones](./images/arp-create-app-01.png)

1. <span data-ttu-id="71c67-108">En la página **registro del tutorial de ASP.net Graph** , en la sección **propiedades** , copie el **identificador de aplicación** tal y como lo necesitará más adelante.</span><span class="sxs-lookup"><span data-stu-id="71c67-108">On the **ASP.NET Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Captura de pantalla del identificador de la aplicación recién creada](./images/arp-create-app-02.png)

1. <span data-ttu-id="71c67-110">Desplácese hacia abajo hasta la sección secretos de la **aplicación** .</span><span class="sxs-lookup"><span data-stu-id="71c67-110">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="71c67-111">Seleccione **generar nueva contraseña**.</span><span class="sxs-lookup"><span data-stu-id="71c67-111">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="71c67-112">En el cuadro de diálogo **nueva contraseña generada** , copie el contenido del cuadro tal y como lo necesitará más adelante.</span><span class="sxs-lookup"><span data-stu-id="71c67-112">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="71c67-113">**Importante:** Esta contraseña no se vuelve a mostrar nunca, por lo que debe asegurarse de que la copia ahora.</span><span class="sxs-lookup"><span data-stu-id="71c67-113">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Captura de pantalla de la contraseña de la aplicación recién creada](./images/arp-create-app-03.png)

1. <span data-ttu-id="71c67-115">DeTermine la dirección URL de la aplicación de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="71c67-115">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="71c67-116">En el explorador de soluciones de Visual Studio, seleccione el proyecto **gráfico-tutorial** .</span><span class="sxs-lookup"><span data-stu-id="71c67-116">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="71c67-117">En la ventana **propiedades** , busque el valor de **dirección URL**.</span><span class="sxs-lookup"><span data-stu-id="71c67-117">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="71c67-118">Copie este valor.</span><span class="sxs-lookup"><span data-stu-id="71c67-118">Copy this value.</span></span>

    ![Captura de pantalla de la ventana Propiedades de Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="71c67-120">Desplácese hacia abajo hasta la sección **plataformas** .</span><span class="sxs-lookup"><span data-stu-id="71c67-120">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="71c67-121">Seleccione **Agregar plataforma**.</span><span class="sxs-lookup"><span data-stu-id="71c67-121">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="71c67-122">En el cuadro de diálogo **Agregar plataforma** , seleccione **Web**.</span><span class="sxs-lookup"><span data-stu-id="71c67-122">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Captura de pantalla que crea una plataforma para la aplicación](./images/arp-create-app-04.png)

    1. <span data-ttu-id="71c67-124">En el cuadro plataforma **Web** , escriba la dirección URL que copió de las propiedades del proyecto de Visual Studio para las **direcciones URL**de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="71c67-124">In the **Web** platform box, enter the URL you copied from the Visual Studio project's properties for the **Redirect URLs**.</span></span>

        ![Captura de pantalla de la plataforma web recién agregada para la aplicación](./images/arp-create-app-05.png)

1. <span data-ttu-id="71c67-126">Desplácese hasta la parte inferior de la página y seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="71c67-126">Scroll to the bottom of the page and select **Save**.</span></span>
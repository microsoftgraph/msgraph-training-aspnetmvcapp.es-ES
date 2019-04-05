<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="72f7f-101">En este ejercicio, creará un nuevo registro de aplicaciones Web de Azure AD con el centro de administración de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="72f7f-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="72f7f-102">DeTermine la dirección URL de la aplicación de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="72f7f-102">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="72f7f-103">En el explorador de soluciones de Visual Studio, seleccione el proyecto **gráfico-tutorial** .</span><span class="sxs-lookup"><span data-stu-id="72f7f-103">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="72f7f-104">En la ventana **propiedades** , busque el valor de **dirección URL**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-104">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="72f7f-105">Copie este valor.</span><span class="sxs-lookup"><span data-stu-id="72f7f-105">Copy this value.</span></span>

    ![Captura de pantalla de la ventana Propiedades de Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="72f7f-107">Abra un explorador y vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="72f7f-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="72f7f-108">Inicie sesión con una **cuenta personal** (también conocido como Microsoft Account) o una **cuenta profesional o educativa**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-108">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="72f7f-109">Seleccione **Azure Active Directory** en el panel de navegación de la izquierda y, después, seleccione **registros de aplicaciones (vista previa)** en **administrar**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-109">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations (Preview)** under **Manage**.</span></span>

    ![<span data-ttu-id="72f7f-110">Una captura de pantalla de los registros de la aplicación</span><span class="sxs-lookup"><span data-stu-id="72f7f-110">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="72f7f-111">Seleccione **registro nuevo**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-111">Select **New registration**.</span></span> <span data-ttu-id="72f7f-112">En la página **registrar una aplicación** , establezca los valores de la siguiente manera.</span><span class="sxs-lookup"><span data-stu-id="72f7f-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="72f7f-113">Establezca **el nombre** en `ASP.NET Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="72f7f-113">Set **Name** to `ASP.NET Graph Tutorial`.</span></span>
    - <span data-ttu-id="72f7f-114">Establezca **tipos de cuenta compatibles** en **cuentas de cualquier directorio de la organización y cuentas personales de Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-114">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="72f7f-115">En **URI**de redireccionamiento, establezca la primera lista desplegable para `Web` y establezca el valor en la dirección URL de la aplicación de ASP.net que copió en el paso 1.</span><span class="sxs-lookup"><span data-stu-id="72f7f-115">Under **Redirect URI**, set the first drop-down to `Web` and set the value to the ASP.NET app URL you copied in step 1.</span></span>

    ![Captura de pantalla de la página registrar una aplicación](./images/aad-register-an-app.png)

1. <span data-ttu-id="72f7f-117">Elija **registrar**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-117">Choose **Register**.</span></span> <span data-ttu-id="72f7f-118">En la página **tutorial de ASP.net Graph** , copie el valor del **identificador de la aplicación (cliente)** y guárdelo, lo necesitará en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="72f7f-118">On the **ASP.NET Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Captura de pantalla del identificador de la aplicación del nuevo registro de la aplicación](./images/aad-application-id.png)

1. <span data-ttu-id="72f7f-120">Seleccione **autenticación** en **administrar**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-120">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="72f7f-121">Busque la sección **concesión implícita** y habilite los tokens de **identificación**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-121">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="72f7f-122">Elija **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-122">Choose **Save**.</span></span>

    ![Captura de pantalla de la sección de concesión implícita](./images/aad-implicit-grant.png)

1. <span data-ttu-id="72f7f-124">Seleccione **certificados & secretos** en **administrar**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-124">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="72f7f-125">Seleccione el botón **nuevo secreto de cliente** .</span><span class="sxs-lookup"><span data-stu-id="72f7f-125">Select the **New client secret** button.</span></span> <span data-ttu-id="72f7f-126">Escriba un valor en **Descripción** y seleccione una de las opciones para **Expires** y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="72f7f-126">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Captura de pantalla del cuadro de diálogo Agregar un secreto de cliente](./images/aad-new-client-secret.png)

1. <span data-ttu-id="72f7f-128">Copie el valor de secreto de cliente antes de salir de esta página.</span><span class="sxs-lookup"><span data-stu-id="72f7f-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="72f7f-129">Lo necesitará en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="72f7f-129">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="72f7f-130">Este secreto de cliente no se vuelve a mostrar nunca, así que asegúrese de copiarlo ahora.</span><span class="sxs-lookup"><span data-stu-id="72f7f-130">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Captura de pantalla del secreto de cliente recién agregado](./images/aad-copy-client-secret.png)
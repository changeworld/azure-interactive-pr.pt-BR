<span data-ttu-id="ee61b-101">O Armazenamento de Blobs do Azure é um serviço de baixo custo e altamente escalonável que pode ser usado para hospedar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="ee61b-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="ee61b-102">Para este tutorial, você o usará para fornecer conteúdo estático (por exemplo, HTML, JavaScript, CSS) ao aplicativo Web compilado.</span><span class="sxs-lookup"><span data-stu-id="ee61b-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="ee61b-103">Criar uma conta de Armazenamento</span><span class="sxs-lookup"><span data-stu-id="ee61b-103">Create a Storage account</span></span>

<span data-ttu-id="ee61b-104">Conta de armazenamento é um recurso do Azure que permite armazenar tabelas, filas, arquivos, blobs (objetos) e discos de máquina virtual.</span><span class="sxs-lookup"><span data-stu-id="ee61b-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="ee61b-105">Faça logon no Cloud Shell (Bash), selecionando o botão **Entrar no modo de foco**.</span><span class="sxs-lookup"><span data-stu-id="ee61b-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="ee61b-106">Esse botão está no canto superior direito ou no final da página, dependendo da amplitude da janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="ee61b-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="ee61b-107">O modo de foco encaixa uma janela do Cloud Shell no lado direito da janela do navegador e, portanto, você pode executar os comandos que são mostrados no tutorial facilmente.</span><span class="sxs-lookup"><span data-stu-id="ee61b-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="ee61b-108">No Azure, um grupo de recursos é um contêiner que mantém os recursos do Azure relacionados para facilitar o gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="ee61b-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="ee61b-109">Crie um novo grupo de recursos denominado **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="ee61b-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="ee61b-110">O conteúdo estático (arquivos HTML, CSS e JavaScript) para este tutorial é hospedado no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="ee61b-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="ee61b-111">O Armazenamento de Blobs exige uma conta de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="ee61b-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="ee61b-112">Crie uma conta de armazenamento (Uso geral V2) no grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="ee61b-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="ee61b-113">Substitua `<storage account name>` por um nome exclusivo.</span><span class="sxs-lookup"><span data-stu-id="ee61b-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="ee61b-114">Usando a barra de pesquisa na parte superior do [portal do Azure](https://portal.azure.com), localize e abra a conta de armazenamento que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="ee61b-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="ee61b-115">No painel de navegação esquerdo, selecione **Site estático (versão prévia)** a fim de configurar um contêiner para hospedagem de site estático.</span><span class="sxs-lookup"><span data-stu-id="ee61b-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="ee61b-116">Selecione **Habilitado** para habilitar o site estático.</span><span class="sxs-lookup"><span data-stu-id="ee61b-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="ee61b-117">Insira *index.html* como o nome do documento de índice.</span><span class="sxs-lookup"><span data-stu-id="ee61b-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="ee61b-118">O campo já tem *index.html* em uma fonte cinza, mas isso é apenas o texto de exemplo; você ainda precisará inserir esse valor no campo.</span><span class="sxs-lookup"><span data-stu-id="ee61b-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="ee61b-119">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="ee61b-119">Click **Save**.</span></span>
    
    ![Inserir configurações do site estático](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="ee61b-121">Salve o **Ponto de Extremidade Primário** em algum lugar conveniente de onde ele possa ser copiado durante o tutorial.</span><span class="sxs-lookup"><span data-stu-id="ee61b-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="ee61b-122">Esta é a URL do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ee61b-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="ee61b-123">Carregar o aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ee61b-123">Upload the web application</span></span>

1. <span data-ttu-id="ee61b-124">Os arquivos de origem do aplicativo compilado neste tutorial estão localizados em um [repositório do GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="ee61b-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="ee61b-125">Verifique se você está em seu diretório base no Cloud Shell e clone esse repositório.</span><span class="sxs-lookup"><span data-stu-id="ee61b-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="ee61b-126">O repositório é clonado em `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="ee61b-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="ee61b-127">O aplicativo Web no lado do cliente está localizado na pasta **www** pasta e é criado com a estrutura JavaScript Vue.js.</span><span class="sxs-lookup"><span data-stu-id="ee61b-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="ee61b-128">Alterne para a pasta e execute os comandos de npm para instalar as dependências e compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ee61b-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="ee61b-129">O último desses comandos pode levar vários minutos para ser concluído.</span><span class="sxs-lookup"><span data-stu-id="ee61b-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="ee61b-130">O aplicativo é gerado na pasta **dist**.</span><span class="sxs-lookup"><span data-stu-id="ee61b-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="ee61b-131">Alterne o diretório atual para **dist** e carregue o aplicativo no contêiner de blobs **$web**.</span><span class="sxs-lookup"><span data-stu-id="ee61b-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="ee61b-132">Abra a URL do ponto de extremidade primário dos sites estáticos do Armazenamento em um navegador para exibir o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ee61b-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Primeira home page do aplicativo Web sem servidor](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="ee61b-134">Resumo</span><span class="sxs-lookup"><span data-stu-id="ee61b-134">Summary</span></span>

<span data-ttu-id="ee61b-135">Neste módulo, você criou um grupo de recursos denominado **first-serverless-app** que contém uma conta de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="ee61b-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="ee61b-136">Um contêiner de blobs denominado **$web** na conta de armazenamento armazena o conteúdo estático do aplicativo Web e disponibiliza o conteúdo publicamente.</span><span class="sxs-lookup"><span data-stu-id="ee61b-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="ee61b-137">Em seguida, você aprenderá a usar uma função sem servidor para carregar imagens para o Armazenamento de Blobs a partir do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ee61b-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>

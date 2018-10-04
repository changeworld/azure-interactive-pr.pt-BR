---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460069"
---
<span data-ttu-id="1f027-103">No módulo anterior, você viu como uma função sem servidor pode facilitar o upload seguro de imagens de um aplicativo Web no armazenamento de blobs.</span><span class="sxs-lookup"><span data-stu-id="1f027-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="1f027-104">Neste módulo, você cria outra função sem servidor para detectar imagens carregadas e criar miniaturas delas.</span><span class="sxs-lookup"><span data-stu-id="1f027-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="1f027-105">Criar um contêiner de armazenamento de blobs para miniaturas</span><span class="sxs-lookup"><span data-stu-id="1f027-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="1f027-106">As imagens no tamanho original são armazenadas em um contêiner denominado **images**.</span><span class="sxs-lookup"><span data-stu-id="1f027-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="1f027-107">Você precisa de outro contêiner para armazenar as miniaturas dessas imagens.</span><span class="sxs-lookup"><span data-stu-id="1f027-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="1f027-108">Verifique se você ainda está conectado ao Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="1f027-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="1f027-109">Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="1f027-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="1f027-110">Crie um novo contêiner denominado **thumbnails** em sua conta de armazenamento com acesso público a todos os blobs.</span><span class="sxs-lookup"><span data-stu-id="1f027-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="1f027-111">Criar uma função sem servidor disparada por blob</span><span class="sxs-lookup"><span data-stu-id="1f027-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="1f027-112">Um gatilho define como uma função é invocada.</span><span class="sxs-lookup"><span data-stu-id="1f027-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="1f027-113">A função que você criará em seguida usa um gatilho de blob: a função é invocada automaticamente quando um blob (arquivo de imagem) é carregado no contêiner **images**.</span><span class="sxs-lookup"><span data-stu-id="1f027-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="1f027-114">Uma função precisa ter um gatilho.</span><span class="sxs-lookup"><span data-stu-id="1f027-114">A function must have one trigger.</span></span> <span data-ttu-id="1f027-115">Gatilhos têm dados associados, que geralmente é o conteúdo que disparou a função.</span><span class="sxs-lookup"><span data-stu-id="1f027-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="1f027-116">As associações definem como uma função lê ou grava dados no Azure ou em serviços de terceiros.</span><span class="sxs-lookup"><span data-stu-id="1f027-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="1f027-117">Essa função cria uma versão em miniatura da imagem que a dispara e salva a miniatura em um contêiner *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="1f027-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="1f027-118">Abra seu aplicativo de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="1f027-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="1f027-119">No painel de navegação à esquerda da janela do aplicativo de funções, passe o mouse sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor.</span><span class="sxs-lookup"><span data-stu-id="1f027-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="1f027-120">Se for exibida uma página de início rápido, clique em **Função personalizada** para ver uma lista de modelos de função.</span><span class="sxs-lookup"><span data-stu-id="1f027-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="1f027-121">Localize o modelo **BlobTrigger** e selecione-o.</span><span class="sxs-lookup"><span data-stu-id="1f027-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="1f027-122">Use esses valores para criar uma função que cria miniaturas quando as imagens são carregadas.</span><span class="sxs-lookup"><span data-stu-id="1f027-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="1f027-123">Configuração</span><span class="sxs-lookup"><span data-stu-id="1f027-123">Setting</span></span>      |  <span data-ttu-id="1f027-124">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="1f027-124">Suggested value</span></span>   | <span data-ttu-id="1f027-125">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="1f027-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="1f027-126">**Linguagem**</span><span class="sxs-lookup"><span data-stu-id="1f027-126">**Language**</span></span> | <span data-ttu-id="1f027-127">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="1f027-127">C# or JavaScript</span></span> | <span data-ttu-id="1f027-128">Escolha o idioma de preferência.</span><span class="sxs-lookup"><span data-stu-id="1f027-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="1f027-129">**Nomeie sua função**</span><span class="sxs-lookup"><span data-stu-id="1f027-129">**Name your function**</span></span> | <span data-ttu-id="1f027-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="1f027-130">ResizeImage</span></span> | <span data-ttu-id="1f027-131">Digite esse nome exatamente como mostrado para que o aplicativo possa descobrir a função.</span><span class="sxs-lookup"><span data-stu-id="1f027-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="1f027-132">**Caminho**</span><span class="sxs-lookup"><span data-stu-id="1f027-132">**Path**</span></span> | <span data-ttu-id="1f027-133">images/{nome}</span><span class="sxs-lookup"><span data-stu-id="1f027-133">images/{name}</span></span> | <span data-ttu-id="1f027-134">Execute a função quando um arquivo é exibido no contêiner **images**.</span><span class="sxs-lookup"><span data-stu-id="1f027-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="1f027-135">**Informações da conta de armazenamento**</span><span class="sxs-lookup"><span data-stu-id="1f027-135">**Storage account information**</span></span> | <span data-ttu-id="1f027-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="1f027-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="1f027-137">Use a variável de ambiente criada anteriormente com a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="1f027-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Inserir configurações para a nova função](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="1f027-139">Clique em **Criar** para criar a função.</span><span class="sxs-lookup"><span data-stu-id="1f027-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="1f027-140">Quando a função é criada, clique em **Integrar** para exibir as associações de gatilho, entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="1f027-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="1f027-141">Clique em **Nova saída** para criar uma nova associação de saída.</span><span class="sxs-lookup"><span data-stu-id="1f027-141">Click **New output** to create a new output binding.</span></span>

    ![Selecione Nova Saída na guia Integrar](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="1f027-143">Selecione **Armazenamento de Blobs do Azure** e clique em **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="1f027-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="1f027-144">Talvez seja necessário rolar para baixo para ver o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="1f027-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Selecionar Armazenamento de Blobs do Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="1f027-146">Insira os valores a seguir.</span><span class="sxs-lookup"><span data-stu-id="1f027-146">Enter the following values.</span></span>

    | <span data-ttu-id="1f027-147">Configuração</span><span class="sxs-lookup"><span data-stu-id="1f027-147">Setting</span></span>      |  <span data-ttu-id="1f027-148">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="1f027-148">Suggested value</span></span>   | <span data-ttu-id="1f027-149">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="1f027-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="1f027-150">**Nome do parâmetro do blob**</span><span class="sxs-lookup"><span data-stu-id="1f027-150">**Blob parameter name**</span></span> | <span data-ttu-id="1f027-151">miniatura</span><span class="sxs-lookup"><span data-stu-id="1f027-151">thumbnail</span></span> | <span data-ttu-id="1f027-152">A função usa o parâmetro com esse nome para gravar a miniatura.</span><span class="sxs-lookup"><span data-stu-id="1f027-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="1f027-153">**Usar valor retornado da função**</span><span class="sxs-lookup"><span data-stu-id="1f027-153">**Use function return value**</span></span> | <span data-ttu-id="1f027-154">Não </span><span class="sxs-lookup"><span data-stu-id="1f027-154">No</span></span> |  |
    | <span data-ttu-id="1f027-155">**Caminho**</span><span class="sxs-lookup"><span data-stu-id="1f027-155">**Path**</span></span> | <span data-ttu-id="1f027-156">thumbnails/{nome}</span><span class="sxs-lookup"><span data-stu-id="1f027-156">thumbnails/{name}</span></span> | <span data-ttu-id="1f027-157">As miniaturas são gravadas em um contêiner denominado **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="1f027-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="1f027-158">**Conexão da conta de armazenamento**</span><span class="sxs-lookup"><span data-stu-id="1f027-158">**Storage account connection**</span></span> | <span data-ttu-id="1f027-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="1f027-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="1f027-160">Use a variável de ambiente criada anteriormente com a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="1f027-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Inserir configurações para a associação de saída de blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="1f027-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="1f027-162">**JavaScript**</span></span>

    1. <span data-ttu-id="1f027-163">(JavaScript) Clique em **Editor avançado** no canto superior direito da janela para revelar o JSON que representa as associações.</span><span class="sxs-lookup"><span data-stu-id="1f027-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="1f027-164">(JavaScript) Na associação `blobTrigger`, adicione uma propriedade denominada `dataType` com um valor de `binary`.</span><span class="sxs-lookup"><span data-stu-id="1f027-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="1f027-165">Isso configura a associação para passar o conteúdo do blob para a função como dados binários.</span><span class="sxs-lookup"><span data-stu-id="1f027-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="1f027-166">Clique em **Salvar** para criar a nova associação.</span><span class="sxs-lookup"><span data-stu-id="1f027-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="1f027-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="1f027-167">**C#**</span></span>

    1. <span data-ttu-id="1f027-168">(C#) Selecione o nome de função **ResizeImage** na barra de navegação à esquerda para abrir o código-fonte da função.</span><span class="sxs-lookup"><span data-stu-id="1f027-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="1f027-169">(C#) A função requer um pacote NuGet chamado **ImageResizer** para gerar as miniaturas.</span><span class="sxs-lookup"><span data-stu-id="1f027-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="1f027-170">Os pacotes NuGet são adicionados às funções de C# com o uso de um arquivo **project.json**.</span><span class="sxs-lookup"><span data-stu-id="1f027-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="1f027-171">Para criar o arquivo, clique em **Exibir Arquivos** à direita para revelar os arquivos que compõem a função.</span><span class="sxs-lookup"><span data-stu-id="1f027-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="1f027-172">(C#) Clique em **Adicionar** para adicionar um novo arquivo chamado **project.json**.</span><span class="sxs-lookup"><span data-stu-id="1f027-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="1f027-173">(C#) Copie o conteúdo de [ **/csharp/ResizeImage/project.json** ](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) no arquivo recém-criado.</span><span class="sxs-lookup"><span data-stu-id="1f027-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="1f027-174">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="1f027-174">Save the file.</span></span> <span data-ttu-id="1f027-175">Os pacotes são restaurados automaticamente quando o arquivo é atualizado.</span><span class="sxs-lookup"><span data-stu-id="1f027-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![Arquivo project.json com ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="1f027-177">(C#) Selecione **run.csx** em **Exibir Arquivos** e substitua seu conteúdo pelo conteúdo de [ **/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="1f027-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="1f027-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="1f027-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="1f027-179">(JavaScript) Essa função requer o pacote `jimp` do npm para redimensionar a foto.</span><span class="sxs-lookup"><span data-stu-id="1f027-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="1f027-180">Para instalar o pacote npm, clique no nome do Aplicativo de funções na barra de navegação à esquerda e clique em **Recursos da plataforma**.</span><span class="sxs-lookup"><span data-stu-id="1f027-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="1f027-181">(JavaScript) Clique em **Console** para revelar uma janela do console.</span><span class="sxs-lookup"><span data-stu-id="1f027-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="1f027-182">(JavaScript) Execute o comando `npm install jimp` no console.</span><span class="sxs-lookup"><span data-stu-id="1f027-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="1f027-183">Pode levar um minuto ou dois para a operação ser concluída.</span><span class="sxs-lookup"><span data-stu-id="1f027-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="1f027-184">(JavaScript) Clique nome da função **ResizeImage** no painel de navegação esquerdo para revelar a função e substitua tudo em **index.js** pelo conteúdo de [ **/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="1f027-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="1f027-185">Clique em **Logs** abaixo da janela de código para expandir o painel de logs.</span><span class="sxs-lookup"><span data-stu-id="1f027-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="1f027-186">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="1f027-186">Click **Save**.</span></span> <span data-ttu-id="1f027-187">Verifique se a função foi salva com êxito e se não há erros no painel de logs.</span><span class="sxs-lookup"><span data-stu-id="1f027-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="1f027-188">Testar a função sem servidor</span><span class="sxs-lookup"><span data-stu-id="1f027-188">Test the serverless function</span></span>

1. <span data-ttu-id="1f027-189">Abra o aplicativo em um navegador.</span><span class="sxs-lookup"><span data-stu-id="1f027-189">Open the application in a browser.</span></span> <span data-ttu-id="1f027-190">Selecione um arquivo de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="1f027-190">Select an image file and upload it.</span></span> <span data-ttu-id="1f027-191">O carregamento é concluído, mas como ainda não adicionamos a capacidade de exibir imagens, o aplicativo não mostra a foto carregada.</span><span class="sxs-lookup"><span data-stu-id="1f027-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="1f027-192">No Cloud Shell, confirme se a imagem foi carregada no contêiner **images**.</span><span class="sxs-lookup"><span data-stu-id="1f027-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="1f027-193">Confirme se a miniatura foi criada em um contêiner denominado **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="1f027-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="1f027-194">Obtenha a URL da miniatura.</span><span class="sxs-lookup"><span data-stu-id="1f027-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="1f027-195">Abra a URL em um navegador e confirme se a miniatura foi criada corretamente.</span><span class="sxs-lookup"><span data-stu-id="1f027-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="1f027-196">Antes de passar para o próximo tutorial, exclua todos os arquivos nos contêineres **images** e **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="1f027-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="1f027-197">Resumo</span><span class="sxs-lookup"><span data-stu-id="1f027-197">Summary</span></span>

<span data-ttu-id="1f027-198">Neste módulo, você criou uma função sem servidor para criar uma miniatura sempre que uma imagem é carregada em um contêiner de armazenamento de blobs.</span><span class="sxs-lookup"><span data-stu-id="1f027-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="1f027-199">Em seguida, você aprenderá a usar o Azure Cosmos DB para armazenar e listar metadados de imagem.</span><span class="sxs-lookup"><span data-stu-id="1f027-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>

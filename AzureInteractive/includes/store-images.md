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
ms.openlocfilehash: 194a25dbf9abda80379aa5aab408ac4ffe9ab7f5
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460051"
---
<span data-ttu-id="e7856-103">O Azure Cosmos DB é o banco de dados multimodelo sem servidor distribuído globalmente da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e7856-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="e7856-104">Neste módulo, você aprenderá a usar o Azure Functions para armazenar e recuperar metadados de imagem como documentos JSON no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="e7856-105">Criar uma conta, um banco de dados e uma coleção do Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="e7856-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="e7856-106">Uma conta do Cosmos DB é um recurso do Azure que contém bancos de dados do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="e7856-107">Verifique se você ainda está conectado ao Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="e7856-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="e7856-108">Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="e7856-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="e7856-109">Crie uma conta do Cosmos DB com um nome exclusivo no mesmo grupo de recursos dos outros recursos deste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e7856-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="e7856-110">Depois que a conta do Cosmos DB for criada, crie um novo banco de dados denominado **imagesdb** na conta.</span><span class="sxs-lookup"><span data-stu-id="e7856-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="e7856-111">Depois que o banco de dados fpr criado, crie uma nova coleção chamada **images** no banco de dados com uma taxa de transferência de 400 RUs (unidades de solicitação).</span><span class="sxs-lookup"><span data-stu-id="e7856-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="e7856-112">Salvar um documento no Cosmos DB quando uma miniatura é criada</span><span class="sxs-lookup"><span data-stu-id="e7856-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="e7856-113">A associação de saída do Cosmos DB permite que você crie documentos em uma coleção do Cosmos DB a partir do Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="e7856-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="e7856-114">Nas etapas a seguir, você configura uma associação de saída do Cosmos DB na função **ResizeImage** e modifica a função para retornar um documento (objeto) a ser salvo.</span><span class="sxs-lookup"><span data-stu-id="e7856-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="e7856-115">Abra o aplicativo de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e7856-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="e7856-116">No painel de navegação à esquerda, expanda a função **ResizeImage** e selecione **Integrar**.</span><span class="sxs-lookup"><span data-stu-id="e7856-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="e7856-117">Em **Saídas**, clique em **Nova Saída**.</span><span class="sxs-lookup"><span data-stu-id="e7856-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="e7856-118">Localize o item **Azure Cosmos DB** e selecione-o.</span><span class="sxs-lookup"><span data-stu-id="e7856-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="e7856-119">Em seguida, clique em **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="e7856-119">Then click **Select**.</span></span>

    ![Selecionar nova saída](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="e7856-121">Preencha os campos em **Saída do Azure Cosmos DB** com os valores a seguir.</span><span class="sxs-lookup"><span data-stu-id="e7856-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="e7856-122">Configuração</span><span class="sxs-lookup"><span data-stu-id="e7856-122">Setting</span></span>      |  <span data-ttu-id="e7856-123">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="e7856-123">Suggested value</span></span>   | <span data-ttu-id="e7856-124">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="e7856-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="e7856-125">**Nome do parâmetro do documento**</span><span class="sxs-lookup"><span data-stu-id="e7856-125">**Document parameter name**</span></span> | <span data-ttu-id="e7856-126">Selecione **Usar valor retornado da função**</span><span class="sxs-lookup"><span data-stu-id="e7856-126">Select **Use function return value**</span></span> | <span data-ttu-id="e7856-127">O valor da caixa de texto é automaticamente definido como **$return**.</span><span class="sxs-lookup"><span data-stu-id="e7856-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="e7856-128">**Nome do banco de dados**</span><span class="sxs-lookup"><span data-stu-id="e7856-128">**Database name**</span></span> | <span data-ttu-id="e7856-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="e7856-129">imagesdb</span></span> | <span data-ttu-id="e7856-130">Use o nome do banco de dados que você criou.</span><span class="sxs-lookup"><span data-stu-id="e7856-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="e7856-131">**Nome da coleção**</span><span class="sxs-lookup"><span data-stu-id="e7856-131">**Collection name**</span></span> | <span data-ttu-id="e7856-132">images</span><span class="sxs-lookup"><span data-stu-id="e7856-132">images</span></span> | <span data-ttu-id="e7856-133">Use o nome da coleção que você criou.</span><span class="sxs-lookup"><span data-stu-id="e7856-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="e7856-134">Ao lado da **conexão de conta do Azure Cosmos DB**, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="e7856-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="e7856-135">Selecione a conta do Cosmos DB que você criou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e7856-135">Select the Cosmos DB account you previously created.</span></span>

    ![Insira as configurações da associação de saída do Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="e7856-137">Clique em **Salvar** para criar a associação de saída do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="e7856-138">Clique no nome de função **ResizeImage** à esquerda para abrir a função.</span><span class="sxs-lookup"><span data-stu-id="e7856-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="e7856-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="e7856-139">**C#**</span></span>

    1. <span data-ttu-id="e7856-140">(C#) Altere o tipo de retorno da função de **void** para **object**.</span><span class="sxs-lookup"><span data-stu-id="e7856-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="e7856-141">(C#) No final da função, adicione o seguinte bloco de código para retornar o documento a ser salvo:</span><span class="sxs-lookup"><span data-stu-id="e7856-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx para a função ResizeImages após as modificações](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="e7856-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="e7856-143">**JavaScript**</span></span>

    1. <span data-ttu-id="e7856-144">(JavaScript) Altere a instrução `context.done()` na cláusula `else` para retornar o documento a ser salvo no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="e7856-145">Clique em **Logs** abaixo da janela de código para expandir o painel de logs.</span><span class="sxs-lookup"><span data-stu-id="e7856-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="e7856-146">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="e7856-146">Click **Save**.</span></span> <span data-ttu-id="e7856-147">Verifique se a função foi salva com êxito e se não há erros no painel de logs.</span><span class="sxs-lookup"><span data-stu-id="e7856-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="e7856-148">Criar uma função para listar imagens do Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="e7856-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="e7856-149">O aplicativo Web requer uma API recuperar metadados de imagem do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="e7856-150">Nas etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="e7856-150">In the following steps.</span></span> <span data-ttu-id="e7856-151">Você criará uma função de gatilho HTTP que usa uma associação de entrada do Cosmos DB para consultar a coleção de bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="e7856-151">you create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="e7856-152">Em seu aplicativo de funções, passe o mouse sobre **Functions** à esquerda e clique em **+** para criar uma nova função.</span><span class="sxs-lookup"><span data-stu-id="e7856-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="e7856-153">Localize o modelo **HttpTrigger** e selecione-o.</span><span class="sxs-lookup"><span data-stu-id="e7856-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="e7856-154">Use esses valores para criar uma função que gera uma URL de obtenção de imagens.</span><span class="sxs-lookup"><span data-stu-id="e7856-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="e7856-155">Configuração</span><span class="sxs-lookup"><span data-stu-id="e7856-155">Setting</span></span>      |  <span data-ttu-id="e7856-156">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="e7856-156">Suggested value</span></span>   | <span data-ttu-id="e7856-157">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="e7856-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="e7856-158">**Nomeie sua função**</span><span class="sxs-lookup"><span data-stu-id="e7856-158">**Name your function**</span></span> | <span data-ttu-id="e7856-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="e7856-159">GetImages</span></span> | <span data-ttu-id="e7856-160">Digite esse nome exatamente como mostrado para que o aplicativo possa descobrir a função.</span><span class="sxs-lookup"><span data-stu-id="e7856-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="e7856-161">**Nível de autorização**</span><span class="sxs-lookup"><span data-stu-id="e7856-161">**Authorization level**</span></span> | <span data-ttu-id="e7856-162">Anônima</span><span class="sxs-lookup"><span data-stu-id="e7856-162">Anonymous</span></span> | <span data-ttu-id="e7856-163">Permita que a função seja acessada publicamente.</span><span class="sxs-lookup"><span data-stu-id="e7856-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="e7856-164">Clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e7856-164">Click **Create**.</span></span>

1. <span data-ttu-id="e7856-165">Quando a nova função for criada, clique em **Integrar** no nome da função na barra de navegação à esquerda.</span><span class="sxs-lookup"><span data-stu-id="e7856-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="e7856-166">Clique em **Nova Entrada** e selecione **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="e7856-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Selecionar nova entrada](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="e7856-168">Clique em **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="e7856-168">Click **Select**.</span></span>

1. <span data-ttu-id="e7856-169">Preencha os seguintes campos:</span><span class="sxs-lookup"><span data-stu-id="e7856-169">Fill out the following values:</span></span>

    | <span data-ttu-id="e7856-170">Configuração</span><span class="sxs-lookup"><span data-stu-id="e7856-170">Setting</span></span>      |  <span data-ttu-id="e7856-171">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="e7856-171">Suggested value</span></span>   | <span data-ttu-id="e7856-172">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="e7856-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="e7856-173">**Nome do parâmetro do documento**</span><span class="sxs-lookup"><span data-stu-id="e7856-173">**Document parameter name**</span></span> | <span data-ttu-id="e7856-174">documentos</span><span class="sxs-lookup"><span data-stu-id="e7856-174">documents</span></span> | <span data-ttu-id="e7856-175">Corresponde ao nome do parâmetro na função.</span><span class="sxs-lookup"><span data-stu-id="e7856-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="e7856-176">**Nome do banco de dados**</span><span class="sxs-lookup"><span data-stu-id="e7856-176">**Database name**</span></span> | <span data-ttu-id="e7856-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="e7856-177">imagesdb</span></span> |  |
    | <span data-ttu-id="e7856-178">**Nome da coleção**</span><span class="sxs-lookup"><span data-stu-id="e7856-178">**Collection name**</span></span> | <span data-ttu-id="e7856-179">images</span><span class="sxs-lookup"><span data-stu-id="e7856-179">images</span></span> |  |
    | <span data-ttu-id="e7856-180">**SQL query**</span><span class="sxs-lookup"><span data-stu-id="e7856-180">**SQL query**</span></span> | <span data-ttu-id="e7856-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="e7856-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="e7856-182">Obter documentos, documentos mais recentes primeiro.</span><span class="sxs-lookup"><span data-stu-id="e7856-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="e7856-183">**Conexão de conta do Azure Cosmos DB**</span><span class="sxs-lookup"><span data-stu-id="e7856-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="e7856-184">Selecionar cadeia de conexão existente</span><span class="sxs-lookup"><span data-stu-id="e7856-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="e7856-185">Clique em **Salvar** para criar a associação de entrada.</span><span class="sxs-lookup"><span data-stu-id="e7856-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="e7856-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="e7856-186">**C#**</span></span>

    1. <span data-ttu-id="e7856-187">Clique no nome da função para abrir a janela de código e substitua tudo em **run.csx** pelo conteúdo em [ **/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="e7856-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="e7856-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="e7856-188">**JavaScript**</span></span>

    1. <span data-ttu-id="e7856-189">Clique no nome da função para abrir a janela de código e substitua tudo em **index.js** pelo conteúdo em [ **/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="e7856-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="e7856-190">Clique em **Logs** abaixo da janela de código para expandir o painel de logs.</span><span class="sxs-lookup"><span data-stu-id="e7856-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="e7856-191">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="e7856-191">Click **Save**.</span></span> <span data-ttu-id="e7856-192">Verifique se a função foi salva com êxito e se não há erros no painel de logs.</span><span class="sxs-lookup"><span data-stu-id="e7856-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="e7856-193">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e7856-193">Test the application</span></span>

1. <span data-ttu-id="e7856-194">Abra o aplicativo em um navegador.</span><span class="sxs-lookup"><span data-stu-id="e7856-194">Open the application in a browser.</span></span> <span data-ttu-id="e7856-195">Selecione um arquivo de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="e7856-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="e7856-196">Depois de alguns segundos, a miniatura da nova imagem será exibida na página.</span><span class="sxs-lookup"><span data-stu-id="e7856-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="e7856-197">No portal do Azure, use a caixa de pesquisa para pesquisar pelo nome de sua conta do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="e7856-198">Clique nela para abri-la.</span><span class="sxs-lookup"><span data-stu-id="e7856-198">Click it to open it.</span></span>

1. <span data-ttu-id="e7856-199">Clique em **Data Explorer** à esquerda para procurar coleções e documentos.</span><span class="sxs-lookup"><span data-stu-id="e7856-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="e7856-200">No banco de dados **imagesdb**, selecione a coleção **images**.</span><span class="sxs-lookup"><span data-stu-id="e7856-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="e7856-201">Confirme se um documento foi criado para a imagem carregada.</span><span class="sxs-lookup"><span data-stu-id="e7856-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Data Explorer mostrando um documento de uma imagem carregada](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="e7856-203">Resumo</span><span class="sxs-lookup"><span data-stu-id="e7856-203">Summary</span></span>

<span data-ttu-id="e7856-204">Neste módulo, você aprendeu a criar uma conta, um banco de dados e uma coleção do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="e7856-205">Você também aprendeu a usar as associações do Cosmos DB para salvar e recuperar metadados de imagem na coleção do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7856-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="e7856-206">Em seguida, você aprenderá a gerar automaticamente uma legenda para cada imagem carregada usando os Serviços Cognitivos da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e7856-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>

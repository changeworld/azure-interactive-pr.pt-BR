---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079069"
---
<span data-ttu-id="0b082-103">O aplicativo que você está compilando é uma galeria de fotos.</span><span class="sxs-lookup"><span data-stu-id="0b082-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="0b082-104">Ele usa o JavaScript do lado do cliente a fim de chamar as APIs para carregar e exibir imagens.</span><span class="sxs-lookup"><span data-stu-id="0b082-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="0b082-105">Neste módulo, você criará uma API usando uma função sem servidor que gera uma URL de tempo limitado para carregar uma imagem.</span><span class="sxs-lookup"><span data-stu-id="0b082-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="0b082-106">O aplicativo Web usa a URL gerada para carregar uma imagem no Armazenamento de Blobs usando a [API REST do Armazenamento de Blobs](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="0b082-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="0b082-107">Criar um contêiner de armazenamento de Blobs para imagens</span><span class="sxs-lookup"><span data-stu-id="0b082-107">Create a blob storage container for images</span></span>

<span data-ttu-id="0b082-108">O aplicativo requer um contêiner de armazenamento diferente para carregar e hospedar imagens.</span><span class="sxs-lookup"><span data-stu-id="0b082-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="0b082-109">Verifique se você ainda está conectado ao Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="0b082-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="0b082-110">Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="0b082-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="0b082-111">Crie um novo contêiner denominado **images** em sua conta de armazenamento com acesso público a todos os blobs.</span><span class="sxs-lookup"><span data-stu-id="0b082-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="0b082-112">Criar um Aplicativo de funções do Azure</span><span class="sxs-lookup"><span data-stu-id="0b082-112">Create an Azure Function app</span></span>

<span data-ttu-id="0b082-113">O Azure Functions é um serviço que executa funções sem servidor.</span><span class="sxs-lookup"><span data-stu-id="0b082-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="0b082-114">Uma função sem servidor pode ser acionada (chamada) por eventos como uma solicitação HTTP ou quando um blob é criado em um contêiner de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="0b082-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="0b082-115">Um aplicativo do Azure Functions é um contêiner para uma ou mais funções sem servidor.</span><span class="sxs-lookup"><span data-stu-id="0b082-115">An Azure Function app is a container for one or more serverless functions.</span></span>

1. <span data-ttu-id="0b082-116">Crie um novo aplicativo do Azure Functions com um nome exclusivo no grupo de recursos que você criou anteriormente denominado **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="0b082-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="0b082-117">Os aplicativos de funções exigem uma conta de armazenamento. Neste tutorial, você deve usar a conta de armazenamento existente.</span><span class="sxs-lookup"><span data-stu-id="0b082-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="0b082-118">Criar uma função sem servidor disparada por HTTP</span><span class="sxs-lookup"><span data-stu-id="0b082-118">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="0b082-119">O aplicativo Web de galeria de fotos faz uma solicitação HTTP para uma função sem servidor a fim de gerar uma URL de tempo limitado para carregar uma imagem no Armazenamento de Blobs com segurança.</span><span class="sxs-lookup"><span data-stu-id="0b082-119">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="0b082-120">A função é disparada por uma solicitação HTTP e usa o SDK do Armazenamento do Azure para gerar e retornar a URL segura.</span><span class="sxs-lookup"><span data-stu-id="0b082-120">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="0b082-121">Depois que o aplicativo de funções é criado, pesquise-o no Portal do Azure usando a caixa de pesquisa e clique para abri-lo.</span><span class="sxs-lookup"><span data-stu-id="0b082-121">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Abrir o aplicativo de funções](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="0b082-123">No painel de navegação à esquerda da janela do aplicativo de funções, passe o mouse sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor.</span><span class="sxs-lookup"><span data-stu-id="0b082-123">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Criar uma nova função](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="0b082-125">Clique em **Função personalizada** para ver uma lista de modelos de função.</span><span class="sxs-lookup"><span data-stu-id="0b082-125">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="0b082-126">Localize o modelo **HttpTrigger** e clique no idioma para usá-lo (C# ou JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0b082-126">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="0b082-127">Use esses valores para criar uma função que gera uma URL de carregamento de blob.</span><span class="sxs-lookup"><span data-stu-id="0b082-127">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="0b082-128">Configuração</span><span class="sxs-lookup"><span data-stu-id="0b082-128">Setting</span></span>      |  <span data-ttu-id="0b082-129">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="0b082-129">Suggested value</span></span>   | <span data-ttu-id="0b082-130">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="0b082-130">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="0b082-131">**Linguagem**</span><span class="sxs-lookup"><span data-stu-id="0b082-131">**Language**</span></span> | <span data-ttu-id="0b082-132">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b082-132">C# or JavaScript</span></span> | <span data-ttu-id="0b082-133">Selecione o idioma que deseja usar.</span><span class="sxs-lookup"><span data-stu-id="0b082-133">Select the language you want to use.</span></span> |
    | <span data-ttu-id="0b082-134">**Nomeie sua função**</span><span class="sxs-lookup"><span data-stu-id="0b082-134">**Name your function**</span></span> | <span data-ttu-id="0b082-135">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="0b082-135">GetUploadUrl</span></span> | <span data-ttu-id="0b082-136">Digite esse nome exatamente como mostrado para que o aplicativo possa descobrir a função.</span><span class="sxs-lookup"><span data-stu-id="0b082-136">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="0b082-137">**Nível de autorização**</span><span class="sxs-lookup"><span data-stu-id="0b082-137">**Authorization level**</span></span> | <span data-ttu-id="0b082-138">Anônima</span><span class="sxs-lookup"><span data-stu-id="0b082-138">Anonymous</span></span> | <span data-ttu-id="0b082-139">Permita que a função seja acessada publicamente.</span><span class="sxs-lookup"><span data-stu-id="0b082-139">Allow the function to be accessed publicly.</span></span> |

    ![Insira as configurações para uma nova função disparada por HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="0b082-141">Clique em **Criar** para criar a função.</span><span class="sxs-lookup"><span data-stu-id="0b082-141">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="0b082-142">**C#**</span><span class="sxs-lookup"><span data-stu-id="0b082-142">**C#**</span></span> 

    1. <span data-ttu-id="0b082-143">Quando o código-fonte da função é exibido, substitua tudo em **run.csx** pelo conteúdo de [ **csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="0b082-143">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="0b082-144">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="0b082-144">**JavaScript**</span></span> 

    1. <span data-ttu-id="0b082-145">(JavaScript) Essa função requer o pacote do npm `azure-storage` a fim de gerar o token SAS (Assinatura de Acesso Compartilhado) necessário para criar a URL segura.</span><span class="sxs-lookup"><span data-stu-id="0b082-145">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="0b082-146">Para instalar o pacote npm, clique no nome do Aplicativo de funções na barra de navegação à esquerda e clique em **Recursos da plataforma**.</span><span class="sxs-lookup"><span data-stu-id="0b082-146">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="0b082-147">(JavaScript) Clique em **Console** para revelar uma janela do console.</span><span class="sxs-lookup"><span data-stu-id="0b082-147">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Abrir uma janela do console](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="0b082-149">(JavaScript) Verifique se o diretório atual é **d:\home\site\wwwroot** executando o comando `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="0b082-149">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="0b082-150">(JavaScript) Execute o comando `npm init -y` para criar um arquivo **package.json** vazio.</span><span class="sxs-lookup"><span data-stu-id="0b082-150">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="0b082-151">(JavaScript) Execute o comando `npm install --save azure-storage` no console para instalar o pacote e salve-o em **package.json**.</span><span class="sxs-lookup"><span data-stu-id="0b082-151">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="0b082-152">Pode levar um minuto ou dois para a operação ser concluída.</span><span class="sxs-lookup"><span data-stu-id="0b082-152">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="0b082-153">(JavaScript) Clique nome da função (**GetUploadUrl**) no painel de navegação esquerdo para revelar a função e substitua tudo em **index.js** pelo conteúdo de [ **/javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="0b082-153">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js após a atualização](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="0b082-155">Clique em **Logs** abaixo da janela de código para expandir o painel de logs.</span><span class="sxs-lookup"><span data-stu-id="0b082-155">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="0b082-156">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="0b082-156">Click **Save**.</span></span> <span data-ttu-id="0b082-157">Verifique no painel de logs se a função foi compilada com êxito.</span><span class="sxs-lookup"><span data-stu-id="0b082-157">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="0b082-158">A função gera o que chamamos de URL de SAS (Assinatura de Acesso Compartilhado), que é usada para carregar um arquivo no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="0b082-158">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="0b082-159">A URL de SAS é válida por um curto período de tempo e permite o carregamento de apenas um arquivo.</span><span class="sxs-lookup"><span data-stu-id="0b082-159">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="0b082-160">Consulte a documentação do Armazenamento de Blobs para obter mais informações sobre [como usar assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="0b082-160">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="0b082-161">Adicionar uma variável de ambiente para a cadeia de conexão de armazenamento</span><span class="sxs-lookup"><span data-stu-id="0b082-161">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="0b082-162">A função que você acabou de criar requer uma cadeia de conexão para a conta de armazenamento, para que ela possa gerar a URL de SAS.</span><span class="sxs-lookup"><span data-stu-id="0b082-162">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="0b082-163">Em vez de codificar a cadeia de conexão no corpo da função, ela pode ser armazenada como uma configuração de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0b082-163">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="0b082-164">As configurações do aplicativo podem ser acessadas como variáveis de ambiente por todas as funções no aplicativo de funções.</span><span class="sxs-lookup"><span data-stu-id="0b082-164">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="0b082-165">No Cloud Shell, consulte a cadeia de conexão da conta de armazenamento e salve-a em uma variável de bash denominada **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="0b082-165">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="0b082-166">Confirme se a variável foi definida com êxito.</span><span class="sxs-lookup"><span data-stu-id="0b082-166">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="0b082-167">Crie uma nova configuração de aplicativo denominada **AZURE_STORAGE_CONNECTION_STRING** usando o valor salvo na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="0b082-167">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="0b082-168">Confirme se a saída do comando contém a nova configuração de aplicativo com o valor correto.</span><span class="sxs-lookup"><span data-stu-id="0b082-168">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="0b082-169">Testar a função sem servidor</span><span class="sxs-lookup"><span data-stu-id="0b082-169">Test the serverless function</span></span>

<span data-ttu-id="0b082-170">Além de criar e editar funções, o portal do Azure também fornece uma ferramenta interna para testar funções.</span><span class="sxs-lookup"><span data-stu-id="0b082-170">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="0b082-171">Para testar a função HTTP sem servidor, clique na guia **Testar** à direita da janela de código para expandir o painel de teste.</span><span class="sxs-lookup"><span data-stu-id="0b082-171">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="0b082-172">Altere o **método HTTP** para **GET**.</span><span class="sxs-lookup"><span data-stu-id="0b082-172">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="0b082-173">Em **Consulta**, clique em *Adicionar parâmetro*\* e adicione os seguintes parâmetros:</span><span class="sxs-lookup"><span data-stu-id="0b082-173">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="0b082-174">Nome</span><span class="sxs-lookup"><span data-stu-id="0b082-174">name</span></span>      |  <span data-ttu-id="0b082-175">value</span><span class="sxs-lookup"><span data-stu-id="0b082-175">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="0b082-176">nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="0b082-176">filename</span></span> | <span data-ttu-id="0b082-177">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="0b082-177">image1.jpg</span></span> |

1. <span data-ttu-id="0b082-178">Clique em **Executar** no painel de teste a fim de enviar uma solicitação HTTP para a função.</span><span class="sxs-lookup"><span data-stu-id="0b082-178">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="0b082-179">A função retorna uma URL de carregamento na saída.</span><span class="sxs-lookup"><span data-stu-id="0b082-179">The function returns an upload URL in the output.</span></span> <span data-ttu-id="0b082-180">A execução da função aparece no painel de logs.</span><span class="sxs-lookup"><span data-stu-id="0b082-180">The function execution appears in the logs panel.</span></span>

    ![Logs mostrando que a função foi executada com êxito](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="0b082-182">Configurar CORS no aplicativo de funções</span><span class="sxs-lookup"><span data-stu-id="0b082-182">Configure CORS in the function app</span></span>

<span data-ttu-id="0b082-183">Como o front-end do aplicativo está hospedado no Armazenamento de Blobs, ele tem um nome de domínio diferente do aplicativo do Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="0b082-183">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="0b082-184">Para o JavaScript do lado do cliente poder chamar com êxito a função que você acabou de criar, o aplicativo de funções precisa ser configurado para CORS (Compartilhamento de Recursos entre Origens).</span><span class="sxs-lookup"><span data-stu-id="0b082-184">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="0b082-185">Na barra de navegação à esquerda da janela do aplicativo de funções, clique no nome de seu aplicativo de funções.</span><span class="sxs-lookup"><span data-stu-id="0b082-185">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="0b082-186">Clique em **Recursos da plataforma** para exibir uma lista dos recursos avançados.</span><span class="sxs-lookup"><span data-stu-id="0b082-186">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="0b082-187">Em **API**, clique em **CORS**.</span><span class="sxs-lookup"><span data-stu-id="0b082-187">Under **API**, click **CORS**.</span></span>

    ![Selecionar CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="0b082-189">Adicionar uma origem permitida para a URL do aplicativo do módulo anterior, omitindo a **/** à direita (por exemplo: `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="0b082-189">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Configurações de CORS mostrando a URL do aplicativo Web sem servidor adicionado](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="0b082-191">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="0b082-191">Click **Save**.</span></span>

1. <span data-ttu-id="0b082-192">C#</span><span class="sxs-lookup"><span data-stu-id="0b082-192">C#</span></span>

   1. <span data-ttu-id="0b082-193">(C#) Navegue de volta para a função `GetUploadUrl` e selecione a guia **Integrar**.</span><span class="sxs-lookup"><span data-stu-id="0b082-193">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="0b082-194">(C#) Em Métodos HTTP selecionados, selecione **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="0b082-194">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="0b082-195">**GET**, **POST** e **OPTIONS** devem estar todos selecionados.</span><span class="sxs-lookup"><span data-stu-id="0b082-195">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="0b082-196">O CORS usa o método OPTIONS, que não é selecionado por padrão em funções de C#.</span><span class="sxs-lookup"><span data-stu-id="0b082-196">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="0b082-197">(C#) Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="0b082-197">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="0b082-198">Ainda no portal, navegue até o aplicativo de funções, selecione a guia **Visão geral** guia e clique em **Reiniciar** para ter certeza de que as alterações do CORS estão funcionando.</span><span class="sxs-lookup"><span data-stu-id="0b082-198">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="0b082-199">Configurar o CORS na conta de armazenamento</span><span class="sxs-lookup"><span data-stu-id="0b082-199">Configure CORS in the Storage account</span></span>

<span data-ttu-id="0b082-200">Como o aplicativo também faz chamadas de JavaScript do lado do cliente para o Armazenamento de Blobs carregar arquivos, você precisa configurar a conta de armazenamento para o CORS.</span><span class="sxs-lookup"><span data-stu-id="0b082-200">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="0b082-201">Execute o comando a seguir para permitir que todas as origens carreguem arquivos na conta de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="0b082-201">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="0b082-202">Modificar o aplicativo Web para carregar imagens</span><span class="sxs-lookup"><span data-stu-id="0b082-202">Modify the web app to upload images</span></span>

<span data-ttu-id="0b082-203">O aplicativo Web recupera as configurações de um arquivo chamado **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="0b082-203">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="0b082-204">Nas etapas a seguir, você criará o arquivo usando o Cloud Shell, definirá `window.apiBaseUrl` como a URL do aplicativo de funções e `window.blobBaseUrl` como a URL do ponto de extremidade do Armazenamento de Blobs do Azure.</span><span class="sxs-lookup"><span data-stu-id="0b082-204">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="0b082-205">No Cloud Shell, verifique se o diretório atual é a pasta **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="0b082-205">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="0b082-206">Consulte a URL do aplicativo de funções e a armazene em uma variável de bash chamada **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="0b082-206">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="0b082-207">Confirme se a variável está definida corretamente.</span><span class="sxs-lookup"><span data-stu-id="0b082-207">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="0b082-208">Para definir o URI base das chamadas à API para seu aplicativo de funções, crie **settings.js** e adicione a URL do aplicativo de funções como mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="0b082-208">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="0b082-209">Você pode fazer a alteração executando o comando a seguir ou usando um editor de linha de comando, como o VIM.</span><span class="sxs-lookup"><span data-stu-id="0b082-209">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="0b082-210">Confirme se o arquivo foi gravado com êxito.</span><span class="sxs-lookup"><span data-stu-id="0b082-210">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="0b082-211">Consulte a URL base do Armazenamento de Blobs e armazene-o em uma variável de bash chamada **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="0b082-211">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="0b082-212">Confirme se a variável está definida corretamente.</span><span class="sxs-lookup"><span data-stu-id="0b082-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="0b082-213">Para definir o URI base das chamadas à API do aplicativo de funções, anexe a URL de armazenamento, semelhante à linha de código a seguir, a **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="0b082-213">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="0b082-214">Você pode fazer a alteração executando o comando a seguir ou usando um editor de linha de comando, como o VIM.</span><span class="sxs-lookup"><span data-stu-id="0b082-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="0b082-215">Confirme se o arquivo foi gravado com êxito e contém agora duas linhas.</span><span class="sxs-lookup"><span data-stu-id="0b082-215">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="0b082-216">Carregue o arquivo no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="0b082-216">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="0b082-217">Testar o aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="0b082-217">Test the web application</span></span>

<span data-ttu-id="0b082-218">Agora, o aplicativo de galeria é capaz de carregar uma imagem no Armazenamento de Blobs, mas ainda não consegue exibir imagens.</span><span class="sxs-lookup"><span data-stu-id="0b082-218">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="0b082-219">Ele tentará chamar uma função `GetImages` que ainda não existe porque você vai criá-la em um módulo posterior.</span><span class="sxs-lookup"><span data-stu-id="0b082-219">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="0b082-220">Essa chamada falhará e a página da Web parecerá estar paralisada no status "Analisando...", mas a imagem selecionada será carregada com êxito.</span><span class="sxs-lookup"><span data-stu-id="0b082-220">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="0b082-221">Você pode verificar se a imagem for carregada com êxito verificando o conteúdo do contêiner **images** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="0b082-221">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="0b082-222">Em uma janela do navegador, navegue até o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0b082-222">In a browser window, browse to the application.</span></span> <span data-ttu-id="0b082-223">Selecione um arquivo de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="0b082-223">Select an image file and upload it.</span></span> <span data-ttu-id="0b082-224">O carregamento é concluído, mas como ainda não adicionamos a capacidade de exibir imagens, o aplicativo não mostra a foto carregada.</span><span class="sxs-lookup"><span data-stu-id="0b082-224">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="0b082-225">(A página da Web parece estar paralisada no status "Analisando imagem..."; você corrigirá isso mais tarde.)</span><span class="sxs-lookup"><span data-stu-id="0b082-225">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="0b082-226">No Cloud Shell, confirme se a imagem foi carregada no contêiner **images**.</span><span class="sxs-lookup"><span data-stu-id="0b082-226">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="0b082-227">Antes de passar para o próximo tutorial, exclua todos os arquivos no contêiner **images**.</span><span class="sxs-lookup"><span data-stu-id="0b082-227">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="0b082-228">Resumo</span><span class="sxs-lookup"><span data-stu-id="0b082-228">Summary</span></span>

<span data-ttu-id="0b082-229">Neste módulo, você criou um aplicativo de funções do Azure e aprendeu a usar uma função sem servidor para permitir que um aplicativo Web carregue imagens no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="0b082-229">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="0b082-230">Em seguida, você aprenderá a criar miniaturas para as imagens carregadas usando uma função sem servidor disparadas por blob.</span><span class="sxs-lookup"><span data-stu-id="0b082-230">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>

---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 10/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3779c2e130afa7ee8d5879f30a924e258b7a41e9
ms.sourcegitcommit: fdb43556b8dcf67cb39c18e532b5fab7ac53eaee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49315969"
---
<span data-ttu-id="c8d2a-103">O aplicativo que você está compilando é uma galeria de fotos.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="c8d2a-104">Ele usa o JavaScript do lado do cliente a fim de chamar as APIs para carregar e exibir imagens.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="c8d2a-105">Neste módulo, você criará uma API usando uma função sem servidor que gera uma URL de tempo limitado para carregar uma imagem.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="c8d2a-106">O aplicativo Web usa a URL gerada para carregar uma imagem no Armazenamento de Blobs usando a [API REST do Armazenamento de Blobs](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="c8d2a-107">Criar um contêiner de armazenamento de Blobs para imagens</span><span class="sxs-lookup"><span data-stu-id="c8d2a-107">Create a blob storage container for images</span></span>

<span data-ttu-id="c8d2a-108">O aplicativo requer um contêiner de armazenamento diferente para carregar e hospedar imagens.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="c8d2a-109">Verifique se você ainda está conectado ao Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="c8d2a-110">Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="c8d2a-111">Crie um novo contêiner denominado **images** em sua conta de armazenamento com acesso público a todos os blobs.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="c8d2a-112">Criar um Aplicativo de funções do Azure</span><span class="sxs-lookup"><span data-stu-id="c8d2a-112">Create an Azure Function app</span></span>

<span data-ttu-id="c8d2a-113">O Azure Functions é um serviço que executa funções sem servidor.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="c8d2a-114">Uma função sem servidor pode ser acionada (chamada) por eventos como uma solicitação HTTP ou quando um blob é criado em um contêiner de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="c8d2a-115">Um aplicativo do Azure Functions é um contêiner para uma ou mais funções sem servidor.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-115">An Azure Function app is a container for one or more serverless functions.</span></span>

<span data-ttu-id="c8d2a-116">Crie um novo aplicativo do Azure Functions com um nome exclusivo no grupo de recursos que você criou anteriormente denominado **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="c8d2a-117">Os aplicativos de funções exigem uma conta de armazenamento. Neste tutorial, você deve usar a conta de armazenamento existente.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a><span data-ttu-id="c8d2a-118">Configurar o aplicativo de funções</span><span class="sxs-lookup"><span data-stu-id="c8d2a-118">Configure the function app</span></span>

<span data-ttu-id="c8d2a-119">O aplicativo de funções neste tutorial requer a versão 1.x da execução do Functions.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-119">The function app in this tutorial requires version 1.x of the Functions runtime.</span></span> <span data-ttu-id="c8d2a-120">Definir a `FUNCTIONS_EXTENSION_VERSION` configuração do aplicativo para `~1` afixa o aplicativo de funções na última versão 1.x.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-120">Setting the `FUNCTIONS_EXTENSION_VERSION` application setting to `~1` pins the function app to the latest 1.x version.</span></span> <span data-ttu-id="c8d2a-121">Defina as configurações de aplicativo com o comando [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-121">Set application settings with the [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) command.</span></span>

<span data-ttu-id="c8d2a-122">O seguinte comando da CLI do Azure, '<nome_aplicativo> é o nome do seu aplicativo de funções.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-122">In the following Azure CLI command, \`<app_name> is the name of your function app.</span></span>

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_EXTENSION_VERSION=~1
```

## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="c8d2a-123">Criar uma função sem servidor disparada por HTTP</span><span class="sxs-lookup"><span data-stu-id="c8d2a-123">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="c8d2a-124">O aplicativo Web de galeria de fotos faz uma solicitação HTTP para uma função sem servidor a fim de gerar uma URL de tempo limitado para carregar uma imagem no Armazenamento de Blobs com segurança.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-124">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="c8d2a-125">A função é disparada por uma solicitação HTTP e usa o SDK do Armazenamento do Azure para gerar e retornar a URL segura.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-125">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="c8d2a-126">Depois que o aplicativo de funções é criado, pesquise-o no Portal do Azure usando a caixa de pesquisa e clique para abri-lo.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-126">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Abrir o aplicativo de funções](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="c8d2a-128">No painel de navegação à esquerda da janela do aplicativo de funções, passe o mouse sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-128">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Criar uma nova função](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="c8d2a-130">Clique em **Função personalizada** para ver uma lista de modelos de função.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-130">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="c8d2a-131">Localize o modelo **HttpTrigger** e clique no idioma para usá-lo (C# ou JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-131">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="c8d2a-132">Use esses valores para criar uma função que gera uma URL de carregamento de blob.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-132">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="c8d2a-133">Configuração</span><span class="sxs-lookup"><span data-stu-id="c8d2a-133">Setting</span></span>      |  <span data-ttu-id="c8d2a-134">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="c8d2a-134">Suggested value</span></span>   | <span data-ttu-id="c8d2a-135">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="c8d2a-135">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="c8d2a-136">**Linguagem**</span><span class="sxs-lookup"><span data-stu-id="c8d2a-136">**Language**</span></span> | <span data-ttu-id="c8d2a-137">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8d2a-137">C# or JavaScript</span></span> | <span data-ttu-id="c8d2a-138">Selecione o idioma que deseja usar.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-138">Select the language you want to use.</span></span> |
    | <span data-ttu-id="c8d2a-139">**Nomeie sua função**</span><span class="sxs-lookup"><span data-stu-id="c8d2a-139">**Name your function**</span></span> | <span data-ttu-id="c8d2a-140">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="c8d2a-140">GetUploadUrl</span></span> | <span data-ttu-id="c8d2a-141">Digite esse nome exatamente como mostrado para que o aplicativo possa descobrir a função.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-141">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="c8d2a-142">**Nível de autorização**</span><span class="sxs-lookup"><span data-stu-id="c8d2a-142">**Authorization level**</span></span> | <span data-ttu-id="c8d2a-143">Anônima</span><span class="sxs-lookup"><span data-stu-id="c8d2a-143">Anonymous</span></span> | <span data-ttu-id="c8d2a-144">Permita que a função seja acessada publicamente.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-144">Allow the function to be accessed publicly.</span></span> |

    ![Insira as configurações para uma nova função disparada por HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="c8d2a-146">Clique em **Criar** para criar a função.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-146">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="c8d2a-147">**C#**</span><span class="sxs-lookup"><span data-stu-id="c8d2a-147">**C#**</span></span> 

    1. <span data-ttu-id="c8d2a-148">Quando o código-fonte da função é exibido, substitua tudo em **run.csx** pelo conteúdo de [ **csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-148">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="c8d2a-149">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="c8d2a-149">**JavaScript**</span></span> 

    1. <span data-ttu-id="c8d2a-150">(JavaScript) Essa função requer o pacote do npm `azure-storage` a fim de gerar o token SAS (Assinatura de Acesso Compartilhado) necessário para criar a URL segura.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-150">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="c8d2a-151">Para instalar o pacote npm, clique no nome do Aplicativo de funções na barra de navegação à esquerda e clique em **Recursos da plataforma**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-151">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="c8d2a-152">(JavaScript) Clique em **Console** para revelar uma janela do console.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-152">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Abrir uma janela do console](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="c8d2a-154">(JavaScript) Verifique se o diretório atual é **d:\home\site\wwwroot** executando o comando `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-154">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="c8d2a-155">(JavaScript) Execute o comando `npm init -y` para criar um arquivo **package.json** vazio.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-155">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="c8d2a-156">(JavaScript) Execute o comando `npm install --save azure-storage` no console para instalar o pacote e salve-o em **package.json**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-156">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="c8d2a-157">Pode levar um minuto ou dois para a operação ser concluída.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-157">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="c8d2a-158">(JavaScript) Clique nome da função (**GetUploadUrl**) no painel de navegação esquerdo para revelar a função e substitua tudo em **index.js** pelo conteúdo de [ **/javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-158">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js após a atualização](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="c8d2a-160">Clique em **Logs** abaixo da janela de código para expandir o painel de logs.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-160">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="c8d2a-161">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-161">Click **Save**.</span></span> <span data-ttu-id="c8d2a-162">Verifique no painel de logs se a função foi compilada com êxito.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-162">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="c8d2a-163">A função gera o que chamamos de URL de SAS (Assinatura de Acesso Compartilhado), que é usada para carregar um arquivo no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-163">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="c8d2a-164">A URL de SAS é válida por um curto período de tempo e permite o carregamento de apenas um arquivo.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-164">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="c8d2a-165">Consulte a documentação do Armazenamento de Blobs para obter mais informações sobre [como usar assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-165">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="c8d2a-166">Adicionar uma variável de ambiente para a cadeia de conexão de armazenamento</span><span class="sxs-lookup"><span data-stu-id="c8d2a-166">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="c8d2a-167">A função que você acabou de criar requer uma cadeia de conexão para a conta de armazenamento, para que ela possa gerar a URL de SAS.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-167">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="c8d2a-168">Em vez de codificar a cadeia de conexão no corpo da função, ela pode ser armazenada como uma configuração de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-168">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="c8d2a-169">As configurações do aplicativo podem ser acessadas como variáveis de ambiente por todas as funções no aplicativo de funções.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-169">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="c8d2a-170">No Cloud Shell, consulte a cadeia de conexão da conta de armazenamento e salve-a em uma variável de bash denominada **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-170">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="c8d2a-171">Confirme se a variável foi definida com êxito.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-171">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="c8d2a-172">Crie uma nova configuração de aplicativo denominada **AZURE_STORAGE_CONNECTION_STRING** usando o valor salvo na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-172">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="c8d2a-173">Confirme se a saída do comando contém a nova configuração de aplicativo com o valor correto.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-173">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="c8d2a-174">Testar a função sem servidor</span><span class="sxs-lookup"><span data-stu-id="c8d2a-174">Test the serverless function</span></span>

<span data-ttu-id="c8d2a-175">Além de criar e editar funções, o portal do Azure também fornece uma ferramenta interna para testar funções.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-175">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="c8d2a-176">Para testar a função HTTP sem servidor, clique na guia **Testar** à direita da janela de código para expandir o painel de teste.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-176">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="c8d2a-177">Altere o **método HTTP** para **GET**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-177">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="c8d2a-178">Em **Consulta**, clique em *Adicionar parâmetro*\* e adicione os seguintes parâmetros:</span><span class="sxs-lookup"><span data-stu-id="c8d2a-178">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="c8d2a-179">Nome</span><span class="sxs-lookup"><span data-stu-id="c8d2a-179">name</span></span>      |  <span data-ttu-id="c8d2a-180">value</span><span class="sxs-lookup"><span data-stu-id="c8d2a-180">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="c8d2a-181">nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="c8d2a-181">filename</span></span> | <span data-ttu-id="c8d2a-182">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="c8d2a-182">image1.jpg</span></span> |

1. <span data-ttu-id="c8d2a-183">Clique em **Executar** no painel de teste a fim de enviar uma solicitação HTTP para a função.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-183">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="c8d2a-184">A função retorna uma URL de carregamento na saída.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-184">The function returns an upload URL in the output.</span></span> <span data-ttu-id="c8d2a-185">A execução da função aparece no painel de logs.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-185">The function execution appears in the logs panel.</span></span>

    ![Logs mostrando que a função foi executada com êxito](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="c8d2a-187">Configurar CORS no aplicativo de funções</span><span class="sxs-lookup"><span data-stu-id="c8d2a-187">Configure CORS in the function app</span></span>

<span data-ttu-id="c8d2a-188">Como o front-end do aplicativo está hospedado no Armazenamento de Blobs, ele tem um nome de domínio diferente do aplicativo do Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-188">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="c8d2a-189">Para o JavaScript do lado do cliente poder chamar com êxito a função que você acabou de criar, o aplicativo de funções precisa ser configurado para CORS (Compartilhamento de Recursos entre Origens).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-189">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="c8d2a-190">Na barra de navegação à esquerda da janela do aplicativo de funções, clique no nome de seu aplicativo de funções.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-190">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="c8d2a-191">Clique em **Recursos da plataforma** para exibir uma lista dos recursos avançados.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-191">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="c8d2a-192">Em **API**, clique em **CORS**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-192">Under **API**, click **CORS**.</span></span>

    ![Selecionar CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="c8d2a-194">Adicionar uma origem permitida para a URL do aplicativo do módulo anterior, omitindo a **/** à direita (por exemplo: `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="c8d2a-194">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Configurações de CORS mostrando a URL do aplicativo Web sem servidor adicionado](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="c8d2a-196">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-196">Click **Save**.</span></span>

1. <span data-ttu-id="c8d2a-197">C#</span><span class="sxs-lookup"><span data-stu-id="c8d2a-197">C#</span></span>

   1. <span data-ttu-id="c8d2a-198">(C#) Navegue de volta para a função `GetUploadUrl` e selecione a guia **Integrar**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-198">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="c8d2a-199">(C#) Em Métodos HTTP selecionados, selecione **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-199">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="c8d2a-200">**GET**, **POST** e **OPTIONS** devem estar todos selecionados.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-200">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="c8d2a-201">O CORS usa o método OPTIONS, que não é selecionado por padrão em funções de C#.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-201">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="c8d2a-202">(C#) Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-202">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="c8d2a-203">Ainda no portal, navegue até o aplicativo de funções, selecione a guia **Visão geral** guia e clique em **Reiniciar** para ter certeza de que as alterações do CORS estão funcionando.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-203">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="c8d2a-204">Configurar o CORS na conta de armazenamento</span><span class="sxs-lookup"><span data-stu-id="c8d2a-204">Configure CORS in the Storage account</span></span>

<span data-ttu-id="c8d2a-205">Como o aplicativo também faz chamadas de JavaScript do lado do cliente para o Armazenamento de Blobs carregar arquivos, você precisa configurar a conta de armazenamento para o CORS.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-205">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="c8d2a-206">Execute o comando a seguir para permitir que todas as origens carreguem arquivos na conta de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-206">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="c8d2a-207">Modificar o aplicativo Web para carregar imagens</span><span class="sxs-lookup"><span data-stu-id="c8d2a-207">Modify the web app to upload images</span></span>

<span data-ttu-id="c8d2a-208">O aplicativo Web recupera as configurações de um arquivo chamado **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-208">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="c8d2a-209">Nas etapas a seguir, você criará o arquivo usando o Cloud Shell, definirá `window.apiBaseUrl` como a URL do aplicativo de funções e `window.blobBaseUrl` como a URL do ponto de extremidade do Armazenamento de Blobs do Azure.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-209">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="c8d2a-210">No Cloud Shell, verifique se o diretório atual é a pasta **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-210">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="c8d2a-211">Consulte a URL do aplicativo de funções e a armazene em uma variável de bash chamada **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-211">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="c8d2a-212">Confirme se a variável está definida corretamente.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="c8d2a-213">Para definir o URI base das chamadas à API para seu aplicativo de funções, crie **settings.js** e adicione a URL do aplicativo de funções como mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-213">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="c8d2a-214">Você pode fazer a alteração executando o comando a seguir ou usando um editor de linha de comando, como o VIM.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="c8d2a-215">Confirme se o arquivo foi gravado com êxito.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-215">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="c8d2a-216">Consulte a URL base do Armazenamento de Blobs e armazene-o em uma variável de bash chamada **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-216">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="c8d2a-217">Confirme se a variável está definida corretamente.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-217">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="c8d2a-218">Para definir o URI base das chamadas à API do aplicativo de funções, anexe a URL de armazenamento, semelhante à linha de código a seguir, a **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-218">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="c8d2a-219">Você pode fazer a alteração executando o comando a seguir ou usando um editor de linha de comando, como o VIM.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-219">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="c8d2a-220">Confirme se o arquivo foi gravado com êxito e contém agora duas linhas.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-220">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="c8d2a-221">Carregue o arquivo no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-221">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="c8d2a-222">Testar o aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="c8d2a-222">Test the web application</span></span>

<span data-ttu-id="c8d2a-223">Agora, o aplicativo de galeria é capaz de carregar uma imagem no Armazenamento de Blobs, mas ainda não consegue exibir imagens.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-223">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="c8d2a-224">Ele tentará chamar uma função `GetImages` que ainda não existe porque você vai criá-la em um módulo posterior.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-224">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="c8d2a-225">Essa chamada falhará e a página da Web parecerá estar paralisada no status "Analisando...", mas a imagem selecionada será carregada com êxito.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-225">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="c8d2a-226">Você pode verificar se a imagem for carregada com êxito verificando o conteúdo do contêiner **images** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-226">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="c8d2a-227">Em uma janela do navegador, navegue até o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-227">In a browser window, browse to the application.</span></span> <span data-ttu-id="c8d2a-228">Selecione um arquivo de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-228">Select an image file and upload it.</span></span> <span data-ttu-id="c8d2a-229">O carregamento é concluído, mas como ainda não adicionamos a capacidade de exibir imagens, o aplicativo não mostra a foto carregada.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-229">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="c8d2a-230">(A página da Web parece estar paralisada no status "Analisando imagem..."; você corrigirá isso mais tarde.)</span><span class="sxs-lookup"><span data-stu-id="c8d2a-230">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="c8d2a-231">No Cloud Shell, confirme se a imagem foi carregada no contêiner **images**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-231">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="c8d2a-232">Antes de passar para o próximo tutorial, exclua todos os arquivos no contêiner **images**.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-232">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="c8d2a-233">Resumo</span><span class="sxs-lookup"><span data-stu-id="c8d2a-233">Summary</span></span>

<span data-ttu-id="c8d2a-234">Neste módulo, você criou um aplicativo de funções do Azure e aprendeu a usar uma função sem servidor para permitir que um aplicativo Web carregue imagens no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-234">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="c8d2a-235">Em seguida, você aprenderá a criar miniaturas para as imagens carregadas usando uma função sem servidor disparadas por blob.</span><span class="sxs-lookup"><span data-stu-id="c8d2a-235">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>

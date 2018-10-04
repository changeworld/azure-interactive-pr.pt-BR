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
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459984"
---
<span data-ttu-id="91f2d-103">Por enquanto, o aplicativo é uma galeria funcional que permite carregar e exibir imagens.</span><span class="sxs-lookup"><span data-stu-id="91f2d-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="91f2d-104">Neste módulo, você aprenderá a usar a API da Pesquisa Visual Computacional dos Serviços Cognitivos da Microsoft a fim de gerar legendas para as imagens carregadas e salvar as legendas com os metadados de imagem no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="91f2d-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="91f2d-105">Criar uma conta da Pesquisa Visual Computacional</span><span class="sxs-lookup"><span data-stu-id="91f2d-105">Create a Computer Vision account</span></span>

<span data-ttu-id="91f2d-106">Os Serviços Cognitivos da Microsoft são uma coleção de serviços disponíveis para desenvolvedores a fim de tornar os aplicativos mais inteligentes.</span><span class="sxs-lookup"><span data-stu-id="91f2d-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="91f2d-107">A API de Pesquisa Visual Computacional é um serviço sem servidor que processa imagens usando algoritmos avançados e retorna informações sobre cada imagem.</span><span class="sxs-lookup"><span data-stu-id="91f2d-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="91f2d-108">Ela tem uma camada gratuita que oferece até 5000 chamadas à API por mês.</span><span class="sxs-lookup"><span data-stu-id="91f2d-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="91f2d-109">Verifique se você ainda está conectado ao Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="91f2d-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="91f2d-110">Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="91f2d-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="91f2d-111">Crie uma nova conta dos Serviços Cognitivos do tipo **ComputerVision** com um nome exclusivo no grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="91f2d-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="91f2d-112">Para a camada gratuita, use **F0** como o SKU.</span><span class="sxs-lookup"><span data-stu-id="91f2d-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="91f2d-113">Se você já tiver uma conta da Pesquisa Visual Computacional, talvez precise criar uma conta Standard (S1); no entanto, isso pode gerar alguns custos.</span><span class="sxs-lookup"><span data-stu-id="91f2d-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="91f2d-114">Criar configurações de aplicativo de funções para a URL e a chave da Pesquisa Visual Computacional</span><span class="sxs-lookup"><span data-stu-id="91f2d-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="91f2d-115">Para chamar a API de Pesquisa Visual Computacional, são necessárias uma URL e uma chave.</span><span class="sxs-lookup"><span data-stu-id="91f2d-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="91f2d-116">Obtenha a chave e a URL da API de Pesquisa Visual Computacional e salve-as em variáveis de bash.</span><span class="sxs-lookup"><span data-stu-id="91f2d-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="91f2d-117">Crie configurações de aplicativo chamadas **COMP_VISION_KEY** e **COMP_VISION_URL**, respectivamente, no aplicativo de funções.</span><span class="sxs-lookup"><span data-stu-id="91f2d-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="91f2d-118">Chamar a API de Pesquisa Visual Computacional da função ResizeImage</span><span class="sxs-lookup"><span data-stu-id="91f2d-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="91f2d-119">Nas etapas a seguir, você modificará a função **ResizeImage** para chamar a API de Pesquisa Visual Computacional a fim de descrever cada imagem carregada e salvar a descrição no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="91f2d-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="91f2d-120">Abra seu aplicativo de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="91f2d-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="91f2d-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="91f2d-121">**C#**</span></span>

    1. <span data-ttu-id="91f2d-122">(C#) Usando o painel de navegação esquerdo, localize a função **ResizeImage** e abra a janela de código.</span><span class="sxs-lookup"><span data-stu-id="91f2d-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="91f2d-123">(C#) Substitua o código pelo conteúdo de [ **/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="91f2d-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="91f2d-124">Esse código usa `HttpClient` para chamar a API de Pesquisa Visual Computacional e salvar o resultado no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="91f2d-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="91f2d-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="91f2d-125">**JavaScript**</span></span>

    1. <span data-ttu-id="91f2d-126">(JavaScript) Essa função requer o pacote do npm `axios` para fazer uma chamada HTTP à API de Pesquisa Visual Computacional.</span><span class="sxs-lookup"><span data-stu-id="91f2d-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="91f2d-127">Para instalar o pacote npm, clique no nome do Aplicativo de funções na barra de navegação à esquerda e clique em **Recursos da plataforma**.</span><span class="sxs-lookup"><span data-stu-id="91f2d-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="91f2d-128">(JavaScript) Clique em **Console** para revelar uma janela do console.</span><span class="sxs-lookup"><span data-stu-id="91f2d-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="91f2d-129">(JavaScript) Execute o comando `npm install axios` no console.</span><span class="sxs-lookup"><span data-stu-id="91f2d-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="91f2d-130">Pode levar um minuto ou dois para a operação ser concluída.</span><span class="sxs-lookup"><span data-stu-id="91f2d-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="91f2d-131">(JavaScript) Clique nome da função (**ResizeImage**) no painel de navegação esquerdo para revelar a função e substitua tudo em **index.js** pelo conteúdo de [ **/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="91f2d-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="91f2d-132">Clique em **Logs** abaixo da janela de código para expandir o painel de logs.</span><span class="sxs-lookup"><span data-stu-id="91f2d-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="91f2d-133">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="91f2d-133">Click **Save**.</span></span> <span data-ttu-id="91f2d-134">Verifique se a função foi salva com êxito e se não há erros no painel de logs.</span><span class="sxs-lookup"><span data-stu-id="91f2d-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="91f2d-135">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="91f2d-135">Test the application</span></span>

1. <span data-ttu-id="91f2d-136">Abra o aplicativo em um navegador.</span><span class="sxs-lookup"><span data-stu-id="91f2d-136">Open the application in a browser.</span></span> <span data-ttu-id="91f2d-137">Selecione um arquivo de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="91f2d-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="91f2d-138">Depois de alguns segundos, a miniatura da nova imagem deverá ser exibida na página.</span><span class="sxs-lookup"><span data-stu-id="91f2d-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="91f2d-139">Passe o mouse sobre a imagem para ver a descrição gerada pela Pesquisa Visual Computacional.</span><span class="sxs-lookup"><span data-stu-id="91f2d-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="91f2d-140">Resumo</span><span class="sxs-lookup"><span data-stu-id="91f2d-140">Summary</span></span>

<span data-ttu-id="91f2d-141">Neste módulo, você aprendeu a gerar legendas para cada imagem carregada automaticamente usando a API da Pesquisa Visual Computacional dos Serviços Cognitivos da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="91f2d-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="91f2d-142">Em seguida, você aprenderá a adicionar autenticação ao aplicativo usando a autenticação do Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="91f2d-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>

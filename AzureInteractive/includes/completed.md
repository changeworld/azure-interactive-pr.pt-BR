---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079070"
---
<span data-ttu-id="4e115-103">Você criou com êxito um aplicativo completo sem servidor usando os serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="4e115-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="4e115-104">Limpar recursos</span><span class="sxs-lookup"><span data-stu-id="4e115-104">Clean up resources</span></span>

<span data-ttu-id="4e115-105">Quando você terminar de trabalhar com este aplicativo, poderá o seguinte comando para excluir todos os recursos criados durante o tutorial:</span><span class="sxs-lookup"><span data-stu-id="4e115-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="4e115-106">Quando solicitado, digite `y`.</span><span class="sxs-lookup"><span data-stu-id="4e115-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="4e115-107">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="4e115-107">Next steps</span></span>

<span data-ttu-id="4e115-108">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="4e115-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="4e115-109">Configure o Armazenamento de blobs do Azure para hospedar um site estático e as imagens carregadas.</span><span class="sxs-lookup"><span data-stu-id="4e115-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="4e115-110">Carregue as imagens para o Armazenamento de blobs do Azure usando o Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="4e115-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="4e115-111">Redimensione imagens usando o Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="4e115-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="4e115-112">Armazene os metadados das imagens no Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="4e115-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="4e115-113">Use a API da Pesquisa Visual dos Serviços Cognitivos para gerar as legendas da imagem automaticamente.</span><span class="sxs-lookup"><span data-stu-id="4e115-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="4e115-114">Use o Azure Active Directory para proteger o aplicativo Web por meio da autenticação de usuários.</span><span class="sxs-lookup"><span data-stu-id="4e115-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="4e115-115">Para saber como conectar outros serviços ao Functions, prossiga para o tutorial de Aplicativo Lógico.</span><span class="sxs-lookup"><span data-stu-id="4e115-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="4e115-116">Functions com Aplicativo Lógico</span><span class="sxs-lookup"><span data-stu-id="4e115-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
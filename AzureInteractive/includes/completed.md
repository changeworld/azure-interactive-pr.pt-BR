---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459983"
---
<span data-ttu-id="fce85-103">Você criou com êxito um aplicativo completo sem servidor usando os serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="fce85-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="fce85-104">Limpar recursos</span><span class="sxs-lookup"><span data-stu-id="fce85-104">Clean up resources</span></span>

<span data-ttu-id="fce85-105">Quando você terminar de trabalhar com este aplicativo, poderá o seguinte comando para excluir todos os recursos criados durante o tutorial:</span><span class="sxs-lookup"><span data-stu-id="fce85-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="fce85-106">Quando solicitado, digite `y`.</span><span class="sxs-lookup"><span data-stu-id="fce85-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="fce85-107">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="fce85-107">Next steps</span></span>

<span data-ttu-id="fce85-108">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="fce85-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="fce85-109">Configure o Armazenamento de blobs do Azure para hospedar um site estático e as imagens carregadas.</span><span class="sxs-lookup"><span data-stu-id="fce85-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="fce85-110">Carregue as imagens para o Armazenamento de blobs do Azure usando o Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="fce85-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="fce85-111">Redimensione imagens usando o Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="fce85-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="fce85-112">Armazene os metadados das imagens no Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="fce85-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="fce85-113">Use a API da Pesquisa Visual dos Serviços Cognitivos para gerar as legendas da imagem automaticamente.</span><span class="sxs-lookup"><span data-stu-id="fce85-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="fce85-114">Use o Azure Active Directory para proteger o aplicativo Web por meio da autenticação de usuários.</span><span class="sxs-lookup"><span data-stu-id="fce85-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="fce85-115">Para saber como conectar outros serviços ao Functions, prossiga para o tutorial de Aplicativo Lógico.</span><span class="sxs-lookup"><span data-stu-id="fce85-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="fce85-116">Functions com Aplicativo Lógico</span><span class="sxs-lookup"><span data-stu-id="fce85-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)

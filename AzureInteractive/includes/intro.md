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
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079067"
---
<span data-ttu-id="3ea6b-103">Neste tutorial, você implantará um aplicativo Web simples que apresenta uma interface de usuário baseada em HTML.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="3ea6b-104">Um back-end sem servidor permite que o aplicativo carregue imagens e obtenha automaticamente as legendas que as descrevem.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Aplicativo Web em execução](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="3ea6b-106">O diagrama abaixo mostra os serviços do Azure usados pelo aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3ea6b-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="3ea6b-107">O Armazenamento de Blobs fornece o conteúdo Web estático (HTML, CSS, JS) e armazena imagens.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="3ea6b-108">O Azure Functions gerencia os carregamentos de imagem, o redimensionamento e o armazenamento de metadados.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="3ea6b-109">O Cosmos DB armazena metadados de imagem.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="3ea6b-110">O Aplicativo Lógico obtém as legendas da imagem na API da Pesquisa Visual Computacional.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="3ea6b-111">O Azure Active Directory gerencia a autenticação dos usuários.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-111">Azure Active Directory manages user authentication.</span></span>

![Diagrama da arquitetura da solução](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="3ea6b-113">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="3ea6b-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="3ea6b-114">Configure o Armazenamento de blobs do Azure para hospedar um site estático e as imagens carregadas.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="3ea6b-115">Carregue as imagens para o Armazenamento de blobs do Azure usando o Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="3ea6b-116">Redimensione imagens usando o Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="3ea6b-117">Armazene os metadados das imagens no Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="3ea6b-118">Use a API da Pesquisa Visual dos Serviços Cognitivos para gerar as legendas da imagem automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="3ea6b-119">Use o Azure Active Directory para proteger o aplicativo Web por meio da autenticação de usuários.</span><span class="sxs-lookup"><span data-stu-id="3ea6b-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
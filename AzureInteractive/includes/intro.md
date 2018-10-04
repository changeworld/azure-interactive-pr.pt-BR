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
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460017"
---
Neste tutorial, você implantará um aplicativo Web simples que apresenta uma interface de usuário baseada em HTML. Um back-end sem servidor permite que o aplicativo carregue imagens e obtenha automaticamente as legendas que as descrevem.

![Aplicativo Web em execução](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

O diagrama abaixo mostra os serviços do Azure usados pelo aplicativo:

1. O Armazenamento de Blobs fornece o conteúdo Web estático (HTML, CSS, JS) e armazena imagens.
2. O Azure Functions gerencia os carregamentos de imagem, o redimensionamento e o armazenamento de metadados.
3. O Cosmos DB armazena metadados de imagem.
4. O Aplicativo Lógico obtém as legendas da imagem na API da Pesquisa Visual Computacional.
5. O Azure Active Directory gerencia a autenticação dos usuários.

![Diagrama da arquitetura da solução](media/functions-first-serverless-web-app/0-architecture.jpg)

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configure o Armazenamento de blobs do Azure para hospedar um site estático e as imagens carregadas.
> * Carregue as imagens para o Armazenamento de blobs do Azure usando o Azure Functions.
> * Redimensione imagens usando o Azure Functions.
> * Armazene os metadados das imagens no Azure Cosmos DB.
> * Use a API da Pesquisa Visual dos Serviços Cognitivos para gerar as legendas da imagem automaticamente.
> * Use o Azure Active Directory para proteger o aplicativo Web por meio da autenticação de usuários.

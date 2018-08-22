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
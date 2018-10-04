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
Você criou com êxito um aplicativo completo sem servidor usando os serviços do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de trabalhar com este aplicativo, poderá o seguinte comando para excluir todos os recursos criados durante o tutorial:

```azurecli
az group delete --name first-serverless-app
```

Quando solicitado, digite `y`.  

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:
> [!div class="checklist"]
> * Configure o Armazenamento de blobs do Azure para hospedar um site estático e as imagens carregadas.
> * Carregue as imagens para o Armazenamento de blobs do Azure usando o Azure Functions.
> * Redimensione imagens usando o Azure Functions.
> * Armazene os metadados das imagens no Azure Cosmos DB.
> * Use a API da Pesquisa Visual dos Serviços Cognitivos para gerar as legendas da imagem automaticamente.
> * Use o Azure Active Directory para proteger o aplicativo Web por meio da autenticação de usuários.

Para saber como conectar outros serviços ao Functions, prossiga para o tutorial de Aplicativo Lógico. 

> [!div class="nextstepaction"]
> [Functions com Aplicativo Lógico](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)

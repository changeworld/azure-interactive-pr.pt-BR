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
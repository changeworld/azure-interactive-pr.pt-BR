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
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079068"
---
O Azure Cosmos DB é o banco de dados multimodelo sem servidor distribuído globalmente da Microsoft. Neste módulo, você aprenderá a usar o Azure Functions para armazenar e recuperar metadados de imagem como documentos JSON no Cosmos DB.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Criar uma conta, um banco de dados e uma coleção do Cosmos DB

Uma conta do Cosmos DB é um recurso do Azure que contém bancos de dados do Cosmos DB.

1. Verifique se você ainda está conectado ao Cloud Shell.  Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell. 

1. Crie uma conta do Cosmos DB com um nome exclusivo no mesmo grupo de recursos dos outros recursos deste tutorial.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Depois que a conta do Cosmos DB for criada, crie um novo banco de dados denominado **imagesdb** na conta.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Depois que o banco de dados fpr criado, crie uma nova coleção chamada **images** no banco de dados com uma taxa de transferência de 400 RUs (unidades de solicitação).

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Salvar um documento no Cosmos DB quando uma miniatura é criada

A associação de saída do Cosmos DB permite que você crie documentos em uma coleção do Cosmos DB a partir do Azure Functions. Nas etapas a seguir, você configura uma associação de saída do Cosmos DB na função **ResizeImage** e modifica a função para retornar um documento (objeto) a ser salvo.

1. Abra o aplicativo de funções no Portal do Azure.

1. No painel de navegação à esquerda, expanda a função **ResizeImage** e selecione **Integrar**.

1. Em **Saídas**, clique em **Nova Saída**.

1. Localize o item **Azure Cosmos DB** e selecione-o. Em seguida, clique em **Selecionar**.

    ![Selecionar nova saída](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Preencha os campos em **Saída do Azure Cosmos DB** com os valores a seguir.

    | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
    | --- | --- | ---|
    | **Nome do parâmetro do documento** | Selecione **Usar valor retornado da função** | O valor da caixa de texto é automaticamente definido como **$return**. |
    | **Nome do banco de dados** | imagesdb | Use o nome do banco de dados que você criou. |
    | **Nome da coleção** | images | Use o nome da coleção que você criou. |

1. Ao lado da **conexão de conta do Azure Cosmos DB**, clique em **Novo**. Selecione a conta do Cosmos DB que você criou anteriormente.

    ![Insira as configurações da associação de saída do Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. Clique em **Salvar** para criar a associação de saída do Cosmos DB.

1. Clique no nome de função **ResizeImage** à esquerda para abrir a função.

1. **C#**

    1. (C#) Altere o tipo de retorno da função de **void** para **object**.

    1. (C#) No final da função, adicione o seguinte bloco de código para retornar o documento a ser salvo:
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx para a função ResizeImages após as modificações](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Altere a instrução `context.done()` na cláusula `else` para retornar o documento a ser salvo no Cosmos DB.

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. Clique em **Logs** abaixo da janela de código para expandir o painel de logs.

1. Clique em **Salvar**. Verifique se a função foi salva com êxito e se não há erros no painel de logs.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Criar uma função para listar imagens do Cosmos DB

O aplicativo Web requer uma API recuperar metadados de imagem do Cosmos DB. Nas etapas a seguir. Você criará uma função disparada por HTTP que usa uma associação de entrada do Cosmos DB para consultar a coleção de banco de dados.

1. Em seu aplicativo de funções, passe o mouse sobre **Functions** à esquerda e clique em **+** para criar uma nova função.

1. Localize o modelo **HttpTrigger** e selecione-o.

1. Use esses valores para criar uma função que gera uma URL de obtenção de imagens.

    | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
    | --- | --- | ---|
    | **Nomeie sua função** | GetImages | Digite esse nome exatamente como mostrado para que o aplicativo possa descobrir a função. |
    | **Nível de autorização** | Anônima | Permita que a função seja acessada publicamente. |

1. Clique em **Criar**.

1. Quando a nova função for criada, clique em **Integrar** no nome da função na barra de navegação à esquerda.

1. Clique em **Nova Entrada** e selecione **Azure Cosmos DB**. 

    ![Selecionar nova entrada](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Clique em **Selecionar**.

1. Preencha os seguintes campos:

    | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
    | --- | --- | ---|
    | **Nome do parâmetro do documento** | documentos | Corresponde ao nome do parâmetro na função. |
    | **Nome do banco de dados** | imagesdb |  |
    | **Nome da coleção** | images |  |
    | **SQL query** | select * from c order by c._ts desc | Obter documentos, documentos mais recentes primeiro. |
    | **Conexão de conta do Azure Cosmos DB** | Selecionar cadeia de conexão existente |  |

1. Clique em **Salvar** para criar a associação de entrada.

1. **C#**

    1. Clique no nome da função para abrir a janela de código e substitua tudo em **run.csx** pelo conteúdo em [ **/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).

1. **JavaScript**

    1. Clique no nome da função para abrir a janela de código e substitua tudo em **index.js** pelo conteúdo em [ **/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).

1. Clique em **Logs** abaixo da janela de código para expandir o painel de logs.

1. Clique em **Salvar**. Verifique se a função foi salva com êxito e se não há erros no painel de logs.


## <a name="test-the-application"></a>Testar o aplicativo

1. Abra o aplicativo em um navegador. Selecione um arquivo de imagem e carregue-o.

1. Depois de alguns segundos, a miniatura da nova imagem será exibida na página.

1. No portal do Azure, use a caixa de pesquisa para pesquisar pelo nome de sua conta do Cosmos DB. Clique nela para abri-la.

1. Clique em **Data Explorer** à esquerda para procurar coleções e documentos.

1. No banco de dados **imagesdb**, selecione a coleção **images**.

1. Confirme se um documento foi criado para a imagem carregada.

    ![Data Explorer mostrando um documento de uma imagem carregada](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Resumo

Neste módulo, você aprendeu a criar uma conta, um banco de dados e uma coleção do Cosmos DB. Você também aprendeu a usar as associações do Cosmos DB para salvar e recuperar metadados de imagem na coleção do Cosmos DB. Em seguida, você aprenderá a gerar automaticamente uma legenda para cada imagem carregada usando os Serviços Cognitivos da Microsoft.
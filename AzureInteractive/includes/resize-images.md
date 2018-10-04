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
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460069"
---
No módulo anterior, você viu como uma função sem servidor pode facilitar o upload seguro de imagens de um aplicativo Web no armazenamento de blobs. Neste módulo, você cria outra função sem servidor para detectar imagens carregadas e criar miniaturas delas.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Criar um contêiner de armazenamento de blobs para miniaturas

As imagens no tamanho original são armazenadas em um contêiner denominado **images**. Você precisa de outro contêiner para armazenar as miniaturas dessas imagens.

1. Verifique se você ainda está conectado ao Cloud Shell (bash).  Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell. 

1. Crie um novo contêiner denominado **thumbnails** em sua conta de armazenamento com acesso público a todos os blobs.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Criar uma função sem servidor disparada por blob

Um gatilho define como uma função é invocada. A função que você criará em seguida usa um gatilho de blob: a função é invocada automaticamente quando um blob (arquivo de imagem) é carregado no contêiner **images**. Uma função precisa ter um gatilho. Gatilhos têm dados associados, que geralmente é o conteúdo que disparou a função.

As associações definem como uma função lê ou grava dados no Azure ou em serviços de terceiros. Essa função cria uma versão em miniatura da imagem que a dispara e salva a miniatura em um contêiner *thumbnails*.

1. Abra seu aplicativo de funções no Portal do Azure.

1. No painel de navegação à esquerda da janela do aplicativo de funções, passe o mouse sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor. Se for exibida uma página de início rápido, clique em **Função personalizada** para ver uma lista de modelos de função.

1. Localize o modelo **BlobTrigger** e selecione-o.

1. Use esses valores para criar uma função que cria miniaturas quando as imagens são carregadas.

    | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
    | --- | --- | ---|
    | **Linguagem** | C# ou JavaScript | Escolha o idioma de preferência. |
    | **Nomeie sua função** | ResizeImage | Digite esse nome exatamente como mostrado para que o aplicativo possa descobrir a função. |
    | **Caminho** | images/{nome} | Execute a função quando um arquivo é exibido no contêiner **images**. |
    | **Informações da conta de armazenamento** | AZURE_STORAGE_CONNECTION_STRING | Use a variável de ambiente criada anteriormente com a cadeia de conexão. |

    ![Inserir configurações para a nova função](media/functions-first-serverless-web-app/3-new-function.png)

1. Clique em **Criar** para criar a função.

1. Quando a função é criada, clique em **Integrar** para exibir as associações de gatilho, entrada e saída.

1. Clique em **Nova saída** para criar uma nova associação de saída.

    ![Selecione Nova Saída na guia Integrar](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Selecione **Armazenamento de Blobs do Azure** e clique em **Selecionar**. Talvez seja necessário rolar para baixo para ver o botão **Selecionar**.

    ![Selecionar Armazenamento de Blobs do Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Insira os valores a seguir.

    | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
    | --- | --- | ---|
    | **Nome do parâmetro do blob** | miniatura | A função usa o parâmetro com esse nome para gravar a miniatura. |
    | **Usar valor retornado da função** | Não  |  |
    | **Caminho** | thumbnails/{nome} | As miniaturas são gravadas em um contêiner denominado **thumbnails**. |
    | **Conexão da conta de armazenamento** | AZURE_STORAGE_CONNECTION_STRING | Use a variável de ambiente criada anteriormente com a cadeia de conexão. |

    ![Inserir configurações para a associação de saída de blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Clique em **Editor avançado** no canto superior direito da janela para revelar o JSON que representa as associações.

    1. (JavaScript) Na associação `blobTrigger`, adicione uma propriedade denominada `dataType` com um valor de `binary`. Isso configura a associação para passar o conteúdo do blob para a função como dados binários.

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. Clique em **Salvar** para criar a nova associação.

1. **C#**

    1. (C#) Selecione o nome de função **ResizeImage** na barra de navegação à esquerda para abrir o código-fonte da função.

    1. (C#) A função requer um pacote NuGet chamado **ImageResizer** para gerar as miniaturas. Os pacotes NuGet são adicionados às funções de C# com o uso de um arquivo **project.json**. Para criar o arquivo, clique em **Exibir Arquivos** à direita para revelar os arquivos que compõem a função.
    
    1. (C#) Clique em **Adicionar** para adicionar um novo arquivo chamado **project.json**.
    
    1. (C#) Copie o conteúdo de [ **/csharp/ResizeImage/project.json** ](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) no arquivo recém-criado. Salve o arquivo. Os pacotes são restaurados automaticamente quando o arquivo é atualizado.
    
        ![Arquivo project.json com ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) Selecione **run.csx** em **Exibir Arquivos** e substitua seu conteúdo pelo conteúdo de [ **/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).

1. **JavaScript** 

    1. (JavaScript) Essa função requer o pacote `jimp` do npm para redimensionar a foto. Para instalar o pacote npm, clique no nome do Aplicativo de funções na barra de navegação à esquerda e clique em **Recursos da plataforma**.

    1. (JavaScript) Clique em **Console** para revelar uma janela do console.

    1. (JavaScript) Execute o comando `npm install jimp` no console. Pode levar um minuto ou dois para a operação ser concluída.

    1. (JavaScript) Clique nome da função **ResizeImage** no painel de navegação esquerdo para revelar a função e substitua tudo em **index.js** pelo conteúdo de [ **/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).

1. Clique em **Logs** abaixo da janela de código para expandir o painel de logs.

1. Clique em **Salvar**. Verifique se a função foi salva com êxito e se não há erros no painel de logs.


## <a name="test-the-serverless-function"></a>Testar a função sem servidor

1. Abra o aplicativo em um navegador. Selecione um arquivo de imagem e carregue-o. O carregamento é concluído, mas como ainda não adicionamos a capacidade de exibir imagens, o aplicativo não mostra a foto carregada.

1. No Cloud Shell, confirme se a imagem foi carregada no contêiner **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Confirme se a miniatura foi criada em um contêiner denominado **thumbnails**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Obtenha a URL da miniatura.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Abra a URL em um navegador e confirme se a miniatura foi criada corretamente.

1. Antes de passar para o próximo tutorial, exclua todos os arquivos nos contêineres **images** e **thumbnails**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Resumo

Neste módulo, você criou uma função sem servidor para criar uma miniatura sempre que uma imagem é carregada em um contêiner de armazenamento de blobs. Em seguida, você aprenderá a usar o Azure Cosmos DB para armazenar e listar metadados de imagem.

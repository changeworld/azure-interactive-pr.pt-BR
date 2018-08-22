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
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079069"
---
O aplicativo que você está compilando é uma galeria de fotos. Ele usa o JavaScript do lado do cliente a fim de chamar as APIs para carregar e exibir imagens. Neste módulo, você criará uma API usando uma função sem servidor que gera uma URL de tempo limitado para carregar uma imagem. O aplicativo Web usa a URL gerada para carregar uma imagem no Armazenamento de Blobs usando a [API REST do Armazenamento de Blobs](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

## <a name="create-a-blob-storage-container-for-images"></a>Criar um contêiner de armazenamento de Blobs para imagens

O aplicativo requer um contêiner de armazenamento diferente para carregar e hospedar imagens.

1. Verifique se você ainda está conectado ao Cloud Shell (bash). Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell.

1.  Crie um novo contêiner denominado **images** em sua conta de armazenamento com acesso público a todos os blobs.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

O Azure Functions é um serviço que executa funções sem servidor. Uma função sem servidor pode ser acionada (chamada) por eventos como uma solicitação HTTP ou quando um blob é criado em um contêiner de armazenamento.

Um aplicativo do Azure Functions é um contêiner para uma ou mais funções sem servidor.

1. Crie um novo aplicativo do Azure Functions com um nome exclusivo no grupo de recursos que você criou anteriormente denominado **first-serverless-app**. Os aplicativos de funções exigem uma conta de armazenamento. Neste tutorial, você deve usar a conta de armazenamento existente.

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a>Criar uma função sem servidor disparada por HTTP

O aplicativo Web de galeria de fotos faz uma solicitação HTTP para uma função sem servidor a fim de gerar uma URL de tempo limitado para carregar uma imagem no Armazenamento de Blobs com segurança. A função é disparada por uma solicitação HTTP e usa o SDK do Armazenamento do Azure para gerar e retornar a URL segura.

1. Depois que o aplicativo de funções é criado, pesquise-o no Portal do Azure usando a caixa de pesquisa e clique para abri-lo.

    ![Abrir o aplicativo de funções](media/functions-first-serverless-web-app/2-search-function-app.png)

1. No painel de navegação à esquerda da janela do aplicativo de funções, passe o mouse sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor.

    ![Criar uma nova função](media/functions-first-serverless-web-app/2-new-function.png)

1. Clique em **Função personalizada** para ver uma lista de modelos de função.

1. Localize o modelo **HttpTrigger** e clique no idioma para usá-lo (C# ou JavaScript).

1. Use esses valores para criar uma função que gera uma URL de carregamento de blob.

    | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
    | --- | --- | ---|
    | **Linguagem** | C# ou JavaScript | Selecione o idioma que deseja usar. |
    | **Nomeie sua função** | GetUploadUrl | Digite esse nome exatamente como mostrado para que o aplicativo possa descobrir a função. |
    | **Nível de autorização** | Anônima | Permita que a função seja acessada publicamente. |

    ![Insira as configurações para uma nova função disparada por HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. Clique em **Criar** para criar a função.

1. **C#** 

    1. Quando o código-fonte da função é exibido, substitua tudo em **run.csx** pelo conteúdo de [ **csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).

1. **JavaScript** 

    1. (JavaScript) Essa função requer o pacote do npm `azure-storage` a fim de gerar o token SAS (Assinatura de Acesso Compartilhado) necessário para criar a URL segura. Para instalar o pacote npm, clique no nome do Aplicativo de funções na barra de navegação à esquerda e clique em **Recursos da plataforma**.

    1. (JavaScript) Clique em **Console** para revelar uma janela do console.

        ![Abrir uma janela do console](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) Verifique se o diretório atual é **d:\home\site\wwwroot** executando o comando `cd d:\home\site\wwwroot`.

    1. (JavaScript) Execute o comando `npm init -y` para criar um arquivo **package.json** vazio.

    1. (JavaScript) Execute o comando `npm install --save azure-storage` no console para instalar o pacote e salve-o em **package.json**. Pode levar um minuto ou dois para a operação ser concluída.

    1. (JavaScript) Clique nome da função (**GetUploadUrl**) no painel de navegação esquerdo para revelar a função e substitua tudo em **index.js** pelo conteúdo de [ **/javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).
    
        ![index.js após a atualização](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Clique em **Logs** abaixo da janela de código para expandir o painel de logs.

1. Clique em **Salvar**. Verifique no painel de logs se a função foi compilada com êxito.

A função gera o que chamamos de URL de SAS (Assinatura de Acesso Compartilhado), que é usada para carregar um arquivo no Armazenamento de Blobs. A URL de SAS é válida por um curto período de tempo e permite o carregamento de apenas um arquivo. Consulte a documentação do Armazenamento de Blobs para obter mais informações sobre [como usar assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Adicionar uma variável de ambiente para a cadeia de conexão de armazenamento

A função que você acabou de criar requer uma cadeia de conexão para a conta de armazenamento, para que ela possa gerar a URL de SAS. Em vez de codificar a cadeia de conexão no corpo da função, ela pode ser armazenada como uma configuração de aplicativo. As configurações do aplicativo podem ser acessadas como variáveis de ambiente por todas as funções no aplicativo de funções.

1. No Cloud Shell, consulte a cadeia de conexão da conta de armazenamento e salve-a em uma variável de bash denominada **STORAGE_CONNECTION_STRING**.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Confirme se a variável foi definida com êxito.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Crie uma nova configuração de aplicativo denominada **AZURE_STORAGE_CONNECTION_STRING** usando o valor salvo na etapa anterior.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Confirme se a saída do comando contém a nova configuração de aplicativo com o valor correto.


## <a name="test-the-serverless-function"></a>Testar a função sem servidor

Além de criar e editar funções, o portal do Azure também fornece uma ferramenta interna para testar funções.

1. Para testar a função HTTP sem servidor, clique na guia **Testar** à direita da janela de código para expandir o painel de teste.

1. Altere o **método HTTP** para **GET**.

1. Em **Consulta**, clique em *Adicionar parâmetro** e adicione os seguintes parâmetros:

    | Nome      |  value   | 
    | --- | --- |
    | nome do arquivo | image1.jpg |

1. Clique em **Executar** no painel de teste a fim de enviar uma solicitação HTTP para a função.

1. A função retorna uma URL de carregamento na saída. A execução da função aparece no painel de logs.

    ![Logs mostrando que a função foi executada com êxito](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>Configurar CORS no aplicativo de funções

Como o front-end do aplicativo está hospedado no Armazenamento de Blobs, ele tem um nome de domínio diferente do aplicativo do Azure Functions. Para o JavaScript do lado do cliente poder chamar com êxito a função que você acabou de criar, o aplicativo de funções precisa ser configurado para CORS (Compartilhamento de Recursos entre Origens).

1. Na barra de navegação à esquerda da janela do aplicativo de funções, clique no nome de seu aplicativo de funções.

1. Clique em **Recursos da plataforma** para exibir uma lista dos recursos avançados.

1. Em **API**, clique em **CORS**.

    ![Selecionar CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Adicionar uma origem permitida para a URL do aplicativo do módulo anterior, omitindo a **/** à direita (por exemplo: `https://firstserverlessweb.z4.web.core.windows.net`).

    ![Configurações de CORS mostrando a URL do aplicativo Web sem servidor adicionado](media/functions-first-serverless-web-app/2-add-cors.png)

1. Clique em **Salvar**.

1. C#

   1. (C#) Navegue de volta para a função `GetUploadUrl` e selecione a guia **Integrar**.

   1. (C#) Em Métodos HTTP selecionados, selecione **OPTIONS**.

      **GET**, **POST** e **OPTIONS** devem estar todos selecionados. O CORS usa o método OPTIONS, que não é selecionado por padrão em funções de C#.  

   1. (C#) Clique em **Salvar**.

1. Ainda no portal, navegue até o aplicativo de funções, selecione a guia **Visão geral** guia e clique em **Reiniciar** para ter certeza de que as alterações do CORS estão funcionando.

## <a name="configure-cors-in-the-storage-account"></a>Configurar o CORS na conta de armazenamento

Como o aplicativo também faz chamadas de JavaScript do lado do cliente para o Armazenamento de Blobs carregar arquivos, você precisa configurar a conta de armazenamento para o CORS.

1. Execute o comando a seguir para permitir que todas as origens carreguem arquivos na conta de armazenamento.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>Modificar o aplicativo Web para carregar imagens

O aplicativo Web recupera as configurações de um arquivo chamado **settings.js**. Nas etapas a seguir, você criará o arquivo usando o Cloud Shell, definirá `window.apiBaseUrl` como a URL do aplicativo de funções e `window.blobBaseUrl` como a URL do ponto de extremidade do Armazenamento de Blobs do Azure.

1. No Cloud Shell, verifique se o diretório atual é a pasta **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Consulte a URL do aplicativo de funções e a armazene em uma variável de bash chamada **FUNCTION_APP_URL**.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Confirme se a variável está definida corretamente.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Para definir o URI base das chamadas à API para seu aplicativo de funções, crie **settings.js** e adicione a URL do aplicativo de funções como mostrado abaixo.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    Você pode fazer a alteração executando o comando a seguir ou usando um editor de linha de comando, como o VIM.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Confirme se o arquivo foi gravado com êxito.

    ```azurecli
    cat settings.js
    ```

1. Consulte a URL base do Armazenamento de Blobs e armazene-o em uma variável de bash chamada **BLOB_BASE_URL**.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Confirme se a variável está definida corretamente.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Para definir o URI base das chamadas à API do aplicativo de funções, anexe a URL de armazenamento, semelhante à linha de código a seguir, a **settings.js**.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    Você pode fazer a alteração executando o comando a seguir ou usando um editor de linha de comando, como o VIM.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Confirme se o arquivo foi gravado com êxito e contém agora duas linhas.

    ```azurecli
    cat settings.js
    ```

1. Carregue o arquivo no Armazenamento de Blobs.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>Testar o aplicativo Web

Agora, o aplicativo de galeria é capaz de carregar uma imagem no Armazenamento de Blobs, mas ainda não consegue exibir imagens. Ele tentará chamar uma função `GetImages` que ainda não existe porque você vai criá-la em um módulo posterior. Essa chamada falhará e a página da Web parecerá estar paralisada no status "Analisando...", mas a imagem selecionada será carregada com êxito.

Você pode verificar se a imagem for carregada com êxito verificando o conteúdo do contêiner **images** no portal do Azure.

1. Em uma janela do navegador, navegue até o aplicativo. Selecione um arquivo de imagem e carregue-o. O carregamento é concluído, mas como ainda não adicionamos a capacidade de exibir imagens, o aplicativo não mostra a foto carregada. (A página da Web parece estar paralisada no status "Analisando imagem..."; você corrigirá isso mais tarde.)

1. No Cloud Shell, confirme se a imagem foi carregada no contêiner **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Antes de passar para o próximo tutorial, exclua todos os arquivos no contêiner **images**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Resumo

Neste módulo, você criou um aplicativo de funções do Azure e aprendeu a usar uma função sem servidor para permitir que um aplicativo Web carregue imagens no Armazenamento de Blobs. Em seguida, você aprenderá a criar miniaturas para as imagens carregadas usando uma função sem servidor disparadas por blob.

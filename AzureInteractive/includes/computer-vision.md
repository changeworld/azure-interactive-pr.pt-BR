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
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079073"
---
Por enquanto, o aplicativo é uma galeria funcional que permite carregar e exibir imagens. Neste módulo, você aprenderá a usar a API da Pesquisa Visual Computacional dos Serviços Cognitivos da Microsoft a fim de gerar legendas para as imagens carregadas e salvar as legendas com os metadados de imagem no Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Criar uma conta da Pesquisa Visual Computacional

Os Serviços Cognitivos da Microsoft são uma coleção de serviços disponíveis para desenvolvedores a fim de tornar os aplicativos mais inteligentes. A API de Pesquisa Visual Computacional é um serviço sem servidor que processa imagens usando algoritmos avançados e retorna informações sobre cada imagem. Ela tem uma camada gratuita que oferece até 5000 chamadas à API por mês.

1. Verifique se você ainda está conectado ao Cloud Shell. Do contrário, selecione **Entrar no modo de foco** para abrir uma janela do Cloud Shell. 

1. Crie uma nova conta dos Serviços Cognitivos do tipo **ComputerVision** com um nome exclusivo no grupo de recursos. Para a camada gratuita, use **F0** como o SKU. Se você já tiver uma conta da Pesquisa Visual Computacional, talvez precise criar uma conta Standard (S1); no entanto, isso pode gerar alguns custos.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Criar configurações de aplicativo de funções para a URL e a chave da Pesquisa Visual Computacional

Para chamar a API de Pesquisa Visual Computacional, são necessárias uma URL e uma chave.

1. Obtenha a chave e a URL da API de Pesquisa Visual Computacional e salve-as em variáveis de bash.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Crie configurações de aplicativo chamadas **COMP_VISION_KEY** e **COMP_VISION_URL**, respectivamente, no aplicativo de funções.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Chamar a API de Pesquisa Visual Computacional da função ResizeImage

Nas etapas a seguir, você modificará a função **ResizeImage** para chamar a API de Pesquisa Visual Computacional a fim de descrever cada imagem carregada e salvar a descrição no Cosmos DB.

1. Abra seu aplicativo de funções no Portal do Azure.

1. **C#**

    1. (C#) Usando o painel de navegação esquerdo, localize a função **ResizeImage** e abra a janela de código.

    1. (C#) Substitua o código pelo conteúdo de [ **/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). Esse código usa `HttpClient` para chamar a API de Pesquisa Visual Computacional e salvar o resultado no Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Essa função requer o pacote do npm `axios` para fazer uma chamada HTTP à API de Pesquisa Visual Computacional. Para instalar o pacote npm, clique no nome do Aplicativo de funções na barra de navegação à esquerda e clique em **Recursos da plataforma**.

    1. (JavaScript) Clique em **Console** para revelar uma janela do console.

    1. (JavaScript) Execute o comando `npm install axios` no console. Pode levar um minuto ou dois para a operação ser concluída.

    1. (JavaScript) Clique nome da função (**ResizeImage**) no painel de navegação esquerdo para revelar a função e substitua tudo em **index.js** pelo conteúdo de [ **/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Clique em **Logs** abaixo da janela de código para expandir o painel de logs.

1. Clique em **Salvar**. Verifique se a função foi salva com êxito e se não há erros no painel de logs.


## <a name="test-the-application"></a>Testar o aplicativo

1. Abra o aplicativo em um navegador. Selecione um arquivo de imagem e carregue-o.

1. Depois de alguns segundos, a miniatura da nova imagem deverá ser exibida na página. Passe o mouse sobre a imagem para ver a descrição gerada pela Pesquisa Visual Computacional.


## <a name="summary"></a>Resumo

Neste módulo, você aprendeu a gerar legendas para cada imagem carregada automaticamente usando a API da Pesquisa Visual Computacional dos Serviços Cognitivos da Microsoft. Em seguida, você aprenderá a adicionar autenticação ao aplicativo usando a autenticação do Serviço de Aplicativo do Azure.
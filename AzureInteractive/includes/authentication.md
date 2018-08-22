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
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079072"
---
A autenticação do Serviço de Aplicativo do Azure habilita o suporte de autenticação turnkey em um aplicativo de funções do Azure. Ele se integra perfeitamente ao Facebook, ao Twitter, à Conta da Microsoft, ao Google e ao Azure Active Directory. Você adicionará a autenticação do Serviço de Aplicativo para proteger as APIs de back-end do aplicativo Web.

## <a name="enable-app-service-authentication"></a>Habilitar autenticação do Serviço de Aplicativo

1. Abra o aplicativo de funções no Portal do Azure.

1. Em **Recursos da plataforma**, selecione **Autenticação/Autorização**.

    ![Selecionar Autenticação/Autorização](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Digite os valores a seguir:
    
    | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
    | --- | --- | ---|
    | **Autenticação do Serviço de Aplicativo** | Por | Habilite a autenticação. |
    | **Ação quando a solicitação não for autenticada** | Fazer logon com o Azure Active Directory | Selecione um método de autenticação configurado (abaixo). |
    | **Provedores de autenticação** | Veja abaixo | Veja abaixo |
    | **Token Store** | Por | Permitir que o Serviço de Aplicativo armazene e gerencie tokens. |
    | **URLs de redirecionamento externo Permitidas** | A URL do seu aplicativo, por exemplo: https://firstserverlessweb.z4.web.core.windows.net/ | URLs para as quais o Serviço de Aplicativo pode redirecionar depois que um usuário é autenticado. |

1. Selecione **Azure Active Directory** para revelar as **Configurações do Azure Active Directory**.

    1. Selecione **Expresso** como o **Modo de Gerenciamento** e preencha as informações a seguir.
    
        | Configuração      |  Valor sugerido   | DESCRIÇÃO                                        |
        | --- | --- | ---|
        | **Modo de gerenciamento** | Expresso, Criar novo aplicativo do AD | Configure automaticamente uma entidade de serviço e a autenticação do Azure Active Directory. |
        | **Como criar o aplicativo** | my-serverless-webapp | Insira um nome de aplicativo exclusivo. |
    
    1. Clique em **OK** para salvar as configurações do Azure Active Directory.

    ![Autenticação/Autorização e configurações do Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. Clique em **Salvar**.


## <a name="modify-the-web-app-to-enable-authentication"></a>Modificar o aplicativo Web para habilitar a autenticação

1. No Cloud Shell, verifique se o diretório atual é a pasta **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Para habilitar a autenticação em seu aplicativo de funções, acrescente a linha de código a seguir em **settings.js**.

    `window.authEnabled = true`

    Faça a alteração e exiba o resultado usando os comandos a seguir ou usando um editor de linha de comando, como o VIM.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Confirme se a alteração foi feita no arquivo.

    ```azurecli
    cat settings.js
    ```

1. Carregue o arquivo no Armazenamento de Blobs.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>Testar o aplicativo

1. Abra o aplicativo em um navegador. Clique em **Fazer logon** e entre.

1. Selecione um arquivo de imagem e carregue-o.

    ![Página de entrada](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Resumo

Neste módulo, você aprendeu a adicionar autenticação aos aplicativos usando a autenticação do Serviço de Aplicativo do Azure.

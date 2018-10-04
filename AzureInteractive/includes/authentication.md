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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460034"
---
<span data-ttu-id="0b207-103">A autenticação do Serviço de Aplicativo do Azure habilita o suporte de autenticação turnkey em um aplicativo de funções do Azure.</span><span class="sxs-lookup"><span data-stu-id="0b207-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="0b207-104">Ele se integra perfeitamente ao Facebook, ao Twitter, à Conta da Microsoft, ao Google e ao Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="0b207-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="0b207-105">Você adicionará a autenticação do Serviço de Aplicativo para proteger as APIs de back-end do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0b207-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="0b207-106">Habilitar autenticação do Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="0b207-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="0b207-107">Abra o aplicativo de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="0b207-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="0b207-108">Em **Recursos da plataforma**, selecione **Autenticação/Autorização**.</span><span class="sxs-lookup"><span data-stu-id="0b207-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Selecionar Autenticação/Autorização](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="0b207-110">Digite os valores a seguir:</span><span class="sxs-lookup"><span data-stu-id="0b207-110">Select the following values:</span></span>
    
    | <span data-ttu-id="0b207-111">Configuração</span><span class="sxs-lookup"><span data-stu-id="0b207-111">Setting</span></span>      |  <span data-ttu-id="0b207-112">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="0b207-112">Suggested value</span></span>   | <span data-ttu-id="0b207-113">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="0b207-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="0b207-114">**Autenticação do Serviço de Aplicativo**</span><span class="sxs-lookup"><span data-stu-id="0b207-114">**App Service Authentication**</span></span> | <span data-ttu-id="0b207-115">Por</span><span class="sxs-lookup"><span data-stu-id="0b207-115">On</span></span> | <span data-ttu-id="0b207-116">Habilite a autenticação.</span><span class="sxs-lookup"><span data-stu-id="0b207-116">Enable authentication.</span></span> |
    | <span data-ttu-id="0b207-117">**Ação quando a solicitação não for autenticada**</span><span class="sxs-lookup"><span data-stu-id="0b207-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="0b207-118">Fazer logon com o Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="0b207-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="0b207-119">Selecione um método de autenticação configurado (abaixo).</span><span class="sxs-lookup"><span data-stu-id="0b207-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="0b207-120">**Provedores de autenticação**</span><span class="sxs-lookup"><span data-stu-id="0b207-120">**Authentication Providers**</span></span> | <span data-ttu-id="0b207-121">Veja abaixo</span><span class="sxs-lookup"><span data-stu-id="0b207-121">See below</span></span> | <span data-ttu-id="0b207-122">Veja abaixo</span><span class="sxs-lookup"><span data-stu-id="0b207-122">See below</span></span> |
    | <span data-ttu-id="0b207-123">**Token Store**</span><span class="sxs-lookup"><span data-stu-id="0b207-123">**Token store**</span></span> | <span data-ttu-id="0b207-124">Por</span><span class="sxs-lookup"><span data-stu-id="0b207-124">On</span></span> | <span data-ttu-id="0b207-125">Permitir que o Serviço de Aplicativo armazene e gerencie tokens.</span><span class="sxs-lookup"><span data-stu-id="0b207-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="0b207-126">**URLs de redirecionamento externo Permitidas**</span><span class="sxs-lookup"><span data-stu-id="0b207-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="0b207-127">A URL do seu aplicativo, por exemplo: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="0b207-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="0b207-128">URLs para as quais o Serviço de Aplicativo pode redirecionar depois que um usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="0b207-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="0b207-129">Selecione **Azure Active Directory** para revelar as **Configurações do Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="0b207-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="0b207-130">Selecione **Expresso** como o **Modo de Gerenciamento** e preencha as informações a seguir.</span><span class="sxs-lookup"><span data-stu-id="0b207-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="0b207-131">Configuração</span><span class="sxs-lookup"><span data-stu-id="0b207-131">Setting</span></span>      |  <span data-ttu-id="0b207-132">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="0b207-132">Suggested value</span></span>   | <span data-ttu-id="0b207-133">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="0b207-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="0b207-134">**Modo de gerenciamento**</span><span class="sxs-lookup"><span data-stu-id="0b207-134">**Management mode**</span></span> | <span data-ttu-id="0b207-135">Expresso, Criar novo aplicativo do AD</span><span class="sxs-lookup"><span data-stu-id="0b207-135">Express, Create new AD app</span></span> | <span data-ttu-id="0b207-136">Configure automaticamente uma entidade de serviço e a autenticação do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="0b207-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="0b207-137">**Como criar o aplicativo**</span><span class="sxs-lookup"><span data-stu-id="0b207-137">**Create app**</span></span> | <span data-ttu-id="0b207-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="0b207-138">my-serverless-webapp</span></span> | <span data-ttu-id="0b207-139">Insira um nome de aplicativo exclusivo.</span><span class="sxs-lookup"><span data-stu-id="0b207-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="0b207-140">Clique em **OK** para salvar as configurações do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="0b207-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Autenticação/Autorização e configurações do Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="0b207-142">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="0b207-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="0b207-143">Modificar o aplicativo Web para habilitar a autenticação</span><span class="sxs-lookup"><span data-stu-id="0b207-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="0b207-144">No Cloud Shell, verifique se o diretório atual é a pasta **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="0b207-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="0b207-145">Para habilitar a autenticação em seu aplicativo de funções, acrescente a linha de código a seguir em **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="0b207-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="0b207-146">Faça a alteração e exiba o resultado usando os comandos a seguir ou usando um editor de linha de comando, como o VIM.</span><span class="sxs-lookup"><span data-stu-id="0b207-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="0b207-147">Confirme se a alteração foi feita no arquivo.</span><span class="sxs-lookup"><span data-stu-id="0b207-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="0b207-148">Carregue o arquivo no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="0b207-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="0b207-149">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0b207-149">Test the application</span></span>

1. <span data-ttu-id="0b207-150">Abra o aplicativo em um navegador.</span><span class="sxs-lookup"><span data-stu-id="0b207-150">Open the application in a browser.</span></span> <span data-ttu-id="0b207-151">Clique em **Fazer logon** e entre.</span><span class="sxs-lookup"><span data-stu-id="0b207-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="0b207-152">Selecione um arquivo de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="0b207-152">Select an image file and upload it.</span></span>

    ![Página de entrada](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="0b207-154">Resumo</span><span class="sxs-lookup"><span data-stu-id="0b207-154">Summary</span></span>

<span data-ttu-id="0b207-155">Neste módulo, você aprendeu a adicionar autenticação aos aplicativos usando a autenticação do Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="0b207-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>

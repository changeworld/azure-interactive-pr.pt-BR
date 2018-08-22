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
<span data-ttu-id="acffc-103">A autenticação do Serviço de Aplicativo do Azure habilita o suporte de autenticação turnkey em um aplicativo de funções do Azure.</span><span class="sxs-lookup"><span data-stu-id="acffc-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="acffc-104">Ele se integra perfeitamente ao Facebook, ao Twitter, à Conta da Microsoft, ao Google e ao Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="acffc-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="acffc-105">Você adicionará a autenticação do Serviço de Aplicativo para proteger as APIs de back-end do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="acffc-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="acffc-106">Habilitar autenticação do Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="acffc-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="acffc-107">Abra o aplicativo de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="acffc-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="acffc-108">Em **Recursos da plataforma**, selecione **Autenticação/Autorização**.</span><span class="sxs-lookup"><span data-stu-id="acffc-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Selecionar Autenticação/Autorização](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="acffc-110">Digite os valores a seguir:</span><span class="sxs-lookup"><span data-stu-id="acffc-110">Select the following values:</span></span>
    
    | <span data-ttu-id="acffc-111">Configuração</span><span class="sxs-lookup"><span data-stu-id="acffc-111">Setting</span></span>      |  <span data-ttu-id="acffc-112">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="acffc-112">Suggested value</span></span>   | <span data-ttu-id="acffc-113">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="acffc-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="acffc-114">**Autenticação do Serviço de Aplicativo**</span><span class="sxs-lookup"><span data-stu-id="acffc-114">**App Service Authentication**</span></span> | <span data-ttu-id="acffc-115">Por</span><span class="sxs-lookup"><span data-stu-id="acffc-115">On</span></span> | <span data-ttu-id="acffc-116">Habilite a autenticação.</span><span class="sxs-lookup"><span data-stu-id="acffc-116">Enable authentication.</span></span> |
    | <span data-ttu-id="acffc-117">**Ação quando a solicitação não for autenticada**</span><span class="sxs-lookup"><span data-stu-id="acffc-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="acffc-118">Fazer logon com o Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="acffc-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="acffc-119">Selecione um método de autenticação configurado (abaixo).</span><span class="sxs-lookup"><span data-stu-id="acffc-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="acffc-120">**Provedores de autenticação**</span><span class="sxs-lookup"><span data-stu-id="acffc-120">**Authentication Providers**</span></span> | <span data-ttu-id="acffc-121">Veja abaixo</span><span class="sxs-lookup"><span data-stu-id="acffc-121">See below</span></span> | <span data-ttu-id="acffc-122">Veja abaixo</span><span class="sxs-lookup"><span data-stu-id="acffc-122">See below</span></span> |
    | <span data-ttu-id="acffc-123">**Token Store**</span><span class="sxs-lookup"><span data-stu-id="acffc-123">**Token store**</span></span> | <span data-ttu-id="acffc-124">Por</span><span class="sxs-lookup"><span data-stu-id="acffc-124">On</span></span> | <span data-ttu-id="acffc-125">Permitir que o Serviço de Aplicativo armazene e gerencie tokens.</span><span class="sxs-lookup"><span data-stu-id="acffc-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="acffc-126">**URLs de redirecionamento externo Permitidas**</span><span class="sxs-lookup"><span data-stu-id="acffc-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="acffc-127">A URL do seu aplicativo, por exemplo: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="acffc-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="acffc-128">URLs para as quais o Serviço de Aplicativo pode redirecionar depois que um usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="acffc-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="acffc-129">Selecione **Azure Active Directory** para revelar as **Configurações do Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="acffc-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="acffc-130">Selecione **Expresso** como o **Modo de Gerenciamento** e preencha as informações a seguir.</span><span class="sxs-lookup"><span data-stu-id="acffc-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="acffc-131">Configuração</span><span class="sxs-lookup"><span data-stu-id="acffc-131">Setting</span></span>      |  <span data-ttu-id="acffc-132">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="acffc-132">Suggested value</span></span>   | <span data-ttu-id="acffc-133">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="acffc-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="acffc-134">**Modo de gerenciamento**</span><span class="sxs-lookup"><span data-stu-id="acffc-134">**Management mode**</span></span> | <span data-ttu-id="acffc-135">Expresso, Criar novo aplicativo do AD</span><span class="sxs-lookup"><span data-stu-id="acffc-135">Express, Create new AD app</span></span> | <span data-ttu-id="acffc-136">Configure automaticamente uma entidade de serviço e a autenticação do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="acffc-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="acffc-137">**Como criar o aplicativo**</span><span class="sxs-lookup"><span data-stu-id="acffc-137">**Create app**</span></span> | <span data-ttu-id="acffc-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="acffc-138">my-serverless-webapp</span></span> | <span data-ttu-id="acffc-139">Insira um nome de aplicativo exclusivo.</span><span class="sxs-lookup"><span data-stu-id="acffc-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="acffc-140">Clique em **OK** para salvar as configurações do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="acffc-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Autenticação/Autorização e configurações do Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="acffc-142">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="acffc-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="acffc-143">Modificar o aplicativo Web para habilitar a autenticação</span><span class="sxs-lookup"><span data-stu-id="acffc-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="acffc-144">No Cloud Shell, verifique se o diretório atual é a pasta **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="acffc-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="acffc-145">Para habilitar a autenticação em seu aplicativo de funções, acrescente a linha de código a seguir em **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="acffc-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="acffc-146">Faça a alteração e exiba o resultado usando os comandos a seguir ou usando um editor de linha de comando, como o VIM.</span><span class="sxs-lookup"><span data-stu-id="acffc-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="acffc-147">Confirme se a alteração foi feita no arquivo.</span><span class="sxs-lookup"><span data-stu-id="acffc-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="acffc-148">Carregue o arquivo no Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="acffc-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="acffc-149">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="acffc-149">Test the application</span></span>

1. <span data-ttu-id="acffc-150">Abra o aplicativo em um navegador.</span><span class="sxs-lookup"><span data-stu-id="acffc-150">Open the application in a browser.</span></span> <span data-ttu-id="acffc-151">Clique em **Fazer logon** e entre.</span><span class="sxs-lookup"><span data-stu-id="acffc-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="acffc-152">Selecione um arquivo de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="acffc-152">Select an image file and upload it.</span></span>

    ![Página de entrada](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="acffc-154">Resumo</span><span class="sxs-lookup"><span data-stu-id="acffc-154">Summary</span></span>

<span data-ttu-id="acffc-155">Neste módulo, você aprendeu a adicionar autenticação aos aplicativos usando a autenticação do Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="acffc-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>

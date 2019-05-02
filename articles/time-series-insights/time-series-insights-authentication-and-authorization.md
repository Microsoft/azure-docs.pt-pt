---
title: Como autenticar e autorizar pela API no Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API de informações de série de tempo do Azure.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: 66a3c40bf1e1e1dc6253520a555e19ebf011297c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698576"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para a API do Azure Time Series Insights

Este artigo explica como configurar a autenticação e autorização usado num aplicativo personalizado que chama a API de informações de série de tempo do Azure.

## <a name="service-principal"></a>Principal de serviço

Esta secção explica como configurar uma aplicação para aceder à API de informações de série de tempo em nome do aplicativo. A aplicação pode, em seguida, consultar dados ou publicar dados de referência no ambiente do Time Series Insights com as credenciais do aplicativo em vez das credenciais do utilizador.

Quando tiver uma aplicação que tem acesso Time Series Insights, tem de configurar uma aplicação do Azure Active Directory e atribuir as políticas de acesso de dados no ambiente do Time Series Insights. Esta abordagem é preferencial para executar o aplicativo em suas próprias credenciais porque:

* Pode atribuir permissões para a identidade de aplicação que são diferentes das suas próprias permissões. Normalmente, estas permissões são limitadas a apenas o que a aplicação requer. Por exemplo, pode permitir que a aplicação leia apenas dados num ambiente de Time Series Insights específico.
* Não precisa de alterar as credenciais da aplicação se alterar as suas responsabilidades.
* Pode utilizar um certificado ou uma chave de aplicativo para automatizar a autenticação ao executar um script automático.

Este artigo mostra como executar esses passos através do portal do Azure. Ele se concentra num aplicativo de inquilino único onde o aplicativo foi desenvolvido para executar na organização apenas um. Geralmente usa aplicações de inquilino único para aplicações de linha de negócio que são executados na sua organização.

O fluxo do programa de configuração consiste em três etapas de alto nível:

1. Crie uma aplicação no Azure Active Directory.
2. Autorize a aplicação a aceder ao ambiente do Time Series Insights.
3. Utilize o ID da aplicação e a chave para adquirir um token para o `"https://api.timeseries.azure.com/"` audiência ou recurso. O token, em seguida, pode ser usado para chamar a API de informações de série de tempo.

Eis os passos detalhados:

1. No portal do Azure, selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo de aplicação**.

   ![Novo registo de aplicação no Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Dê um nome ao aplicativo, selecione o tipo seja **aplicação Web / API**, selecione qualquer URI válida para **URL de início de sessão**e clique em **criar**.

   ![Criar a aplicação no Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Selecione o novo aplicativo criado e copie o seu ID da aplicação para o seu editor de texto favorito.

   ![Copie o ID da aplicação](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Selecione **chaves**, introduza o nome da chave, selecione a expiração e clique em **guardar**.

   ![Selecione as chaves da aplicação](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Introduza o nome da chave e a expiração e clique em Guardar](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Copie a chave para o seu editor de texto favorito.

   ![Copie a chave da aplicação](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Para o ambiente do Time Series Insights, selecione **políticas de acesso de dados** e clique em **Add**.

   ![Adicionar nova política de acesso de dados para o ambiente do Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. Na **selecionar utilizador** caixa de diálogo, cole o nome da aplicação (a partir do passo 2) ou ID da aplicação (a partir do passo 3).

   ![Localizar uma aplicação na caixa de diálogo Selecionar utilizador](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Selecione a função (**leitor** para consultar os dados, **contribuinte** para consultar dados e alterar dados de referência) e clique em **Ok**.

   ![Escolher o leitor ou contribuinte na caixa de diálogo Selecionar função](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Guardar a política ao clicar em **Ok**.

10. Utilize o ID da aplicação (a partir do passo 3) e a chave de aplicação (a partir do passo 5) para obter o token em nome do aplicativo. O token, em seguida, pode ser transmitido a `Authorization` cabeçalho quando o aplicativo chama a API de informações de série de tempo.

    Se estiver usando c#, pode utilizar o seguinte código para obter o token em nome do aplicativo. Para obter um exemplo completo, consulte [consultar dados com c#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Utilize o ID da aplicação e a chave na sua aplicação para autenticar com o Azure Time Series Insights. 

## <a name="next-steps"></a>Passos Seguintes

- Para o código de exemplo que chama a API de informações de série de tempo, consulte [consultar dados com c#](time-series-insights-query-data-csharp.md).

- Para informações de referência de API, consulte [referência da API de consulta](/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar um principal de serviço](../active-directory/develop/howto-create-service-principal-portal.md).
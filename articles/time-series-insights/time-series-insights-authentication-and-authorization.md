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
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5fb2802bfe9cc0a4d3297e6fa749e5b94008c616
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472674"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para a API do Azure Time Series Insights

Este artigo explica como configurar a autenticação e autorização usado num aplicativo personalizado que chama a API de informações de série de tempo do Azure.

> [!TIP]
> Leia sobre [conceder acesso a dados](./time-series-insights-data-access.md) ao seu ambiente do Time Series Insights no Azure Active Directory.

## <a name="service-principal"></a>Principal de serviço

Isso e secções a seguir descrevem como configurar uma aplicação para aceder à API de informações de série de tempo em nome do aplicativo. A aplicação pode, em seguida, consultar ou publicar dados de referência no ambiente do Time Series Insights com as credenciais do aplicativo em vez das credenciais do utilizador.

## <a name="best-practices"></a>Melhores práticas

Se tiver uma aplicação que tem acesso Time Series Insights:

1. Configure uma aplicação do Azure Active Directory.
1. [Atribuir políticas de acesso de dados](./time-series-insights-data-access.md) no ambiente do Time Series Insights.

Utilizar o aplicativo em vez das suas credenciais de utilizador é desejável desde:

* Pode atribuir permissões para a identidade de aplicação que são diferentes dos suas próprias permissões. Normalmente, estas permissões são limitadas a apenas o que a aplicação requer. Por exemplo, pode permitir que a aplicação leia apenas dados num ambiente de Time Series Insights específico.
* Não tem de alterar as credenciais da aplicação, se alterar as suas responsabilidades.
* Pode utilizar um certificado ou uma chave de aplicativo para automatizar a autenticação ao executar um script automático.

As seções a seguir demonstram como executar esses passos através do portal do Azure. O artigo se concentra num aplicativo de inquilino único onde o aplicativo foi desenvolvido para executar na organização apenas um. Normalmente, irá utilizar aplicações de inquilino único para aplicações de linha de negócio que são executados na sua organização.

## <a name="set-up-summary"></a>Configurar resumo

O fluxo do programa de configuração consiste em três passos:

1. Crie uma aplicação no Azure Active Directory.
1. Autorize a aplicação a aceder ao ambiente do Time Series Insights.
1. Utilize o ID da aplicação e a chave para adquirir um token de `https://api.timeseries.azure.com/`. O token, em seguida, pode ser usado para chamar a API de informações de série de tempo.

## <a name="detailed-setup"></a>Configuração detalhada

1. No portal do Azure, selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo de aplicação**.

   [![Novo registo de aplicação no Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Dê um nome ao aplicativo, selecione o tipo seja **aplicação Web / API**, selecione qualquer URI válida para **URL de início de sessão**e clique em **criar**.

   [![Criar a aplicação no Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Selecione o novo aplicativo criado e copie o seu ID da aplicação para o seu editor de texto favorito.

   [![Copie o ID da aplicação](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Selecione **chaves**, introduza o nome da chave, selecione a expiração e clique em **guardar**.

   [![Selecione as chaves da aplicação](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Introduza o nome da chave e a expiração e clique em Guardar](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Copie a chave para o seu editor de texto favorito.

   [![Copie a chave da aplicação](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Para o ambiente do Time Series Insights, selecione **políticas de acesso de dados** e clique em **Add**.

   [![Adicionar nova política de acesso de dados para o ambiente do Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na **selecionar utilizador** caixa de diálogo caixa, cole o nome da aplicação (partir **passo 2**) ou ID da aplicação (de **passo 3**).

   [![Localizar uma aplicação na caixa de diálogo Selecionar utilizador](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função (**leitor** para consultar os dados, **contribuinte** para consultar dados e alterar dados de referência) e clique em **OK**.

   [![Escolher o leitor ou contribuinte na caixa de diálogo Selecionar função](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Guardar a política ao clicar em **OK**.

1. Utilizar o ID da aplicação (de **passo 3**) e chave da aplicação (partir **passo 5**) para obter o token em nome da aplicação. O token, em seguida, pode ser transmitido a `Authorization` cabeçalho quando o aplicativo chama a API de informações de série de tempo.

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
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

Utilize o **ID da aplicação** e **chave** na sua aplicação para autenticar com o Azure Time Series Insights.

## <a name="next-steps"></a>Passos Seguintes

- Para o código de exemplo que chama a API de informações de série de tempo, consulte [consultar dados com c#](time-series-insights-query-data-csharp.md).

- Para informações de referência de API, consulte [referência da API de consulta](/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar um principal de serviço](../active-directory/develop/howto-create-service-principal-portal.md).

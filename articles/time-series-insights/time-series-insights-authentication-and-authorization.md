---
title: Autenticar e autorizar com uma API no Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API de informações de série de tempo do Azure.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543938"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para a API do Azure Time Series Insights

Este documento descreve como registar uma aplicação no Azure Active Directory com o novo painel do Azure Active Directory. Aplicações registadas no Azure Active Directory permitem aos utilizadores autenticar para e de estar autorizado a utilizar a API de série de tempo do Azure Insight associados a um ambiente do Time Series Insights.

## <a name="service-principal"></a>Principal de serviço

As secções seguintes descrevem como configurar uma aplicação para aceder à API de informações de série de tempo em nome de uma aplicação. A aplicação poderá consultar ou publicar dados de referência no ambiente do Time Series Insights com suas próprias credenciais de aplicação no Azure Active Directory.

## <a name="summary-and-best-practices"></a>Resumidas e práticas recomendadas

O fluxo de registo de aplicação do Azure Active Directory envolve três etapas principais.

1. [Registar uma aplicação](#azure-active-directory-app-registration) no Azure Active Directory.
1. Autorizar a aplicação ter [acesso a dados para o ambiente do Time Series Insights](#granting-data-access).
1. Utilize o **ID da aplicação** e **segredo do cliente** para adquirir um token de `https://api.timeseries.azure.com/` no seu [aplicação de cliente](#client-app-initialization). O token, em seguida, pode ser usado para chamar a API de informações de série de tempo.

Por **passo 3**, separar as suas credenciais de utilizador e da sua aplicação permite-lhe:

* Atribua permissões para a identidade de aplicação que são diferentes dos suas próprias permissões. Normalmente, estas permissões são limitadas a apenas o que a aplicação requer. Por exemplo, pode permitir que a aplicação leia dados apenas a partir de um determinado ambiente do Time Series Insights.
* Isolar a segurança da aplicação de credenciais de autenticação de criação do utilizador utilizando um **segredo do cliente** ou certificado de segurança. Como resultado, as credenciais da aplicação não são dependentes de credenciais de um utilizador específico. Se a função do utilizador for alterado, o aplicativo não requer necessariamente novas credenciais ou configuração adicional. Se o usuário alterar a palavra-passe, todos os acessos para a aplicação não requerem novas credenciais ou chaves.
* Executar um script automático, com um **segredo do cliente** ou de segurança de certificado em vez de credenciais de um utilizador específico (exigir que eles estejam presentes).
* Utilize um certificado de segurança em vez de uma palavra-passe para proteger o acesso à sua API de informações de série de tempo do Azure.

> [!IMPORTANT]
> Acompanhar o princípio da **separação de preocupações** (descrito para este cenário acima) quando configurar a política de segurança do Azure Time Series Insights.

> [!NOTE]
> * O artigo se concentra num aplicativo de inquilino único onde o aplicativo foi desenvolvido para executar na organização apenas um.
> * Normalmente, irá utilizar aplicações de inquilino único para aplicações de linha de negócio que são executados na sua organização.

## <a name="detailed-setup"></a>Configuração detalhada

### <a name="azure-active-directory-app-registration"></a>Registo de aplicação do Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Conceder acesso a dados

1. Para o ambiente do Time Series Insights, selecione **políticas de acesso de dados** e selecione **Add**.

   [![Adicionar nova política de acesso de dados para o ambiente do Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na **selecionar utilizador** caixa de diálogo caixa, cole-o a **nome da aplicação** ou o **ID da aplicação** da secção de registo de aplicação do Azure Active Directory.

   [![Localizar uma aplicação na caixa de diálogo Selecionar utilizador](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função. Selecione **leitor** para consultar dados ou **contribuinte** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Escolher o leitor ou contribuinte na caixa de diálogo Selecionar função de utilizador](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Guardar a política ao selecionar **OK**.

   > [!TIP]
   > Leia sobre [conceder acesso a dados](./time-series-insights-data-access.md) ao seu ambiente do Time Series Insights no Azure Active Directory.

### <a name="client-app-initialization"></a>Inicialização de aplicação do cliente

1. Utilize o **ID da aplicação** e **segredo do cliente** (chave da aplicação) da secção de registo de aplicação do Azure Active Directory para adquirir o token em nome do aplicativo.

    No C#, o código a seguir pode adquirir o token em nome do aplicativo. Para obter um exemplo completo, consulte [consultar dados com c#](time-series-insights-query-data-csharp.md).

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

1. O token, em seguida, pode ser transmitido a `Authorization` cabeçalho quando o aplicativo chama a API de informações de série de tempo.

## <a name="next-steps"></a>Passos Seguintes

- Para o código de exemplo que chama a API de informações de série de tempo de disponibilidade geral, consulte [consulta dados com C# ](./time-series-insights-query-data-csharp.md).

- Para exemplos de código de pré-visualização Time Series Insights API, consulte [pré-visualização de consulta de dados com C# ](./time-series-insights-update-query-data-csharp.md).

- Para informações de referência de API, consulte [referência da API de consulta](/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar um principal de serviço](../active-directory/develop/howto-create-service-principal-portal.md).
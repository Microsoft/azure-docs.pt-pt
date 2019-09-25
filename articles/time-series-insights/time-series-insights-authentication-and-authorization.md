---
title: Autenticar e autorizar usando uma API no Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e a autorização para um aplicativo personalizado que chama a API de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 88734b0ee05f5193da89f33e1639e4e7a187f225
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264652"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para Azure Time Series Insights API

Este documento descreve como registrar um aplicativo no Azure Active Directory usando a folha novo Azure Active Directory. Os aplicativos registrados no Azure Active Directory permitem que os usuários se autentiquem no e estejam autorizados a usar a API do Azure Time Series Insight associada a um ambiente de Time Series Insights.

## <a name="service-principal"></a>Principal de serviço

As seções a seguir descrevem como configurar um aplicativo para acessar a API de Time Series Insights em nome de um aplicativo. O aplicativo pode então consultar ou publicar dados de referência no ambiente de Time Series Insights usando suas próprias credenciais de aplicativo por meio de Azure Active Directory.

## <a name="summary-and-best-practices"></a>Resumo e práticas recomendadas

O fluxo de registro do aplicativo Azure Active Directory envolve três etapas principais.

1. [Registrar um aplicativo](#azure-active-directory-app-registration) no Azure Active Directory.
1. Autorize o aplicativo a ter [acesso a dados ao ambiente de time Series insights](#granting-data-access).
1. Use a **ID do aplicativo** e o **segredo do cliente** para adquirir `https://api.timeseries.azure.com/` um token do no seu [aplicativo cliente](#client-app-initialization). O token pode ser usado para chamar a API de Time Series Insights.

Por **etapa 3**, separar o seu aplicativo e suas credenciais de usuário permite que você:

* Atribua permissões à identidade do aplicativo que sejam diferentes das suas próprias permissões. Normalmente, essas permissões são restritas apenas ao que o aplicativo requer. Por exemplo, você pode permitir que o aplicativo leia dados somente de um ambiente de Time Series Insights específico.
* Isole a segurança do aplicativo das credenciais de autenticação do usuário de criação usando um **segredo do cliente** ou certificado de segurança. Como resultado, as credenciais do aplicativo não dependem das credenciais de um usuário específico. Se a função do usuário for alterada, o aplicativo não precisa necessariamente de novas credenciais ou configuração adicional. Se o usuário alterar sua senha, todo o acesso ao aplicativo não exigirá novas credenciais ou chaves.
* Executar um script autônomo usando um segredo do **cliente** ou certificado de segurança em vez de credenciais de um usuário específico (exigindo que estejam presentes).
* Use um certificado de segurança em vez de uma senha para proteger o acesso à API do Azure Time Series Insights.

> [!IMPORTANT]
> Siga o princípio de **separação de preocupações** (descritas para este cenário acima) ao configurar sua política de segurança de Azure Time Series insights.

> [!NOTE]
> * O artigo se concentra em um aplicativo de locatário único em que o aplicativo deve ser executado em apenas uma organização.
> * Normalmente, você usará aplicativos de locatário único para aplicativos de linha de negócios que são executados em sua organização.

## <a name="detailed-setup"></a>Configuração detalhada

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory o registro do aplicativo

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concedendo acesso a dados

1. Para o ambiente de Time Series Insights, selecione **políticas de acesso a dados** e selecione **Adicionar**.

   [![Adicionar nova política de acesso a dados ao ambiente de Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na caixa de diálogo **Selecionar usuário** , Cole o **nome do aplicativo** ou a **ID do aplicativo** na seção Azure Active Directory registro do aplicativo.

   [![Localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função. Selecione **leitor** para consultar dados ou **colaborador** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Selecione leitor ou colaborador na caixa de diálogo Selecionar função de usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salve a política selecionando **OK**.

   > [!TIP]
   > Leia sobre como [conceder acesso a dados](./time-series-insights-data-access.md) ao seu ambiente de Time Series Insights no Azure Active Directory.

### <a name="client-app-initialization"></a>Inicialização do aplicativo cliente

1. Use a **ID do aplicativo** e o **segredo do cliente** (chave do aplicativo) na seção Azure Active Directory de registro do aplicativo para adquirir o token em nome do aplicativo.

    No C#, o código a seguir pode adquirir o token em nome do aplicativo. Para obter um exemplo completo, consulte [consultar dados C#usando ](time-series-insights-query-data-csharp.md).

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

1. Em seguida, o token pode ser passado `Authorization` no cabeçalho quando o aplicativo chama a API Time Series insights.

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta seção descreve os cabeçalhos de solicitação HTTP comuns e os parâmetros usados para fazer consultas em relação às APIs de versão Time Series Insights GA e versão prévia. Os requisitos específicos da API são abordados com mais detalhes na [documentação de referência da API REST do time Series insights](https://docs.microsoft.com/rest/api/time-series-insights/).

### <a name="authentication"></a>Authentication

Para executar consultas autenticadas em relação às [APIs REST do time Series insights](https://docs.microsoft.com/rest/api/time-series-insights/), um token de portador OAuth 2,0 válido deve ser passado no [cabeçalho Authorization](/rest/api/apimanagement/authorizationserver/createorupdate) usando um cliente REST de sua escolha (post, JavaScript C#,). 

> [!IMPORTANT]
> O token deve ser emitido exatamente para o `https://api.timeseries.azure.com/` recurso (também conhecido como "público" do token).
> * O [](https://www.getpostman.com/) **AuthURL** de postmaster com, portanto, está em conformidade com:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Consulte o tutorial [explorar o Azure Time Series insights biblioteca de cliente JavaScript](tutorial-explore-js-client-lib.md#authentication) para saber como autenticar com as APIs de time Series insights de forma programática usando o [SDK do cliente JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

### <a name="http-headers"></a>Cabeçalhos HTTP

Cabeçalhos de solicitação necessários:

- `Authorization`para autenticação e autorização, um token de portador OAuth 2,0 válido deve ser passado no cabeçalho de autorização. O token deve ser emitido exatamente para o `https://api.timeseries.azure.com/` recurso (também conhecido como "público" do token).

Cabeçalhos de solicitação opcionais:

- `Content-type`-Há `application/json` suporte apenas para.
- `x-ms-client-request-id`-uma ID de solicitação do cliente. O serviço registra esse valor. Permite que o serviço rastreie a operação entre serviços.
- `x-ms-client-session-id`-uma ID de sessão de cliente. O serviço registra esse valor. Permite que o serviço rastreie um grupo de operações relacionadas entre serviços.
- `x-ms-client-application-name`-nome do aplicativo que gerou esta solicitação. O serviço registra esse valor.

Cabeçalhos de resposta:

- `Content-type`-Há `application/json` suporte apenas para.
- `x-ms-request-id`-ID da solicitação gerada pelo servidor. Pode ser usado para contatar a Microsoft para investigar uma solicitação.

### <a name="http-parameters"></a>Parâmetros HTTP

Parâmetros de cadeia de caracteres de consulta de URL necessários:

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Parâmetros opcionais da cadeia de consulta de URL:

- `timeout=<timeout>`– tempo limite do lado do servidor para a execução da solicitação. Aplicável somente para as APIs [obter eventos de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e [obter agregações de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . O valor de tempo limite deve estar no formato de duração ISO `"PT20S"` 8601, por exemplo, e `1-30 s`deve estar no intervalo. O valor padrão `30 s`é.

## <a name="next-steps"></a>Passos seguintes

- Para obter o código de exemplo que chama a API de Time Series Insights GA, consulte [consultar dados usando C# ](./time-series-insights-query-data-csharp.md).

- Para visualizar Time Series Insights exemplos de código de API, consulte [consultar dados C#de visualização usando ](./time-series-insights-update-query-data-csharp.md).

- Para obter informações de referência de API, consulte [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).
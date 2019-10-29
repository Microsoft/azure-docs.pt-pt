---
title: Autenticar e autorizar usando uma API no Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e a autorização para um aplicativo personalizado que chama a API de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4fd68f770cbe48b15646ec41c0bf94be5e760a50
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990192"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para Azure Time Series Insights API

Este documento descreve como registrar um aplicativo no Azure Active Directory usando a folha novo Azure Active Directory. Os aplicativos registrados no Azure Active Directory permitem que os usuários se autentiquem no e estejam autorizados a usar a API do Azure Time Series Insight associada a um ambiente de Time Series Insights.

## <a name="service-principal"></a>Entidade de serviço

As seções a seguir descrevem como configurar um aplicativo para acessar a API de Time Series Insights em nome de um aplicativo. O aplicativo pode então consultar ou publicar dados de referência no ambiente de Time Series Insights usando suas próprias credenciais de aplicativo por meio de Azure Active Directory.

## <a name="summary-and-best-practices"></a>Resumo e práticas recomendadas

O fluxo de registro do aplicativo Azure Active Directory envolve três etapas principais.

1. [Registrar um aplicativo](#azure-active-directory-app-registration) no Azure Active Directory.
1. Autorize o aplicativo a ter [acesso a dados ao ambiente de time Series insights](#granting-data-access).
1. Use a **ID do aplicativo** e o **segredo do cliente** para adquirir um token de `https://api.timeseries.azure.com/` em seu [aplicativo cliente](#client-app-initialization). O token pode ser usado para chamar a API de Time Series Insights.

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

   [![localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função. Selecione **leitor** para consultar dados ou **colaborador** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![selecionar leitor ou colaborador na caixa de diálogo Selecionar função de usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

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

1. Em seguida, o token pode ser passado no cabeçalho `Authorization` quando o aplicativo chama a API Time Series Insights.

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta seção descreve os cabeçalhos de solicitação HTTP comuns e os parâmetros usados para fazer consultas em relação às APIs de versão Time Series Insights GA e versão prévia. Os requisitos específicos da API são abordados com mais detalhes na [documentação de referência da API REST do time Series insights](https://docs.microsoft.com/rest/api/time-series-insights/).

### <a name="authentication"></a>Autenticação

Para executar consultas autenticadas em relação às [APIs REST do time Series insights](https://docs.microsoft.com/rest/api/time-series-insights/), um token de portador OAuth 2,0 válido deve ser passado no [cabeçalho Authorization](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) usando um cliente REST de sua escolha (post, JavaScript C#,). 

> [!IMPORTANT]
> O token deve ser emitido exatamente para o recurso de `https://api.timeseries.azure.com/` (também conhecido como "público" do token).
> * O [](https://www.getpostman.com/) **AuthURL** de postmaster com, portanto, está em conformidade com: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Consulte a visualização de [exemplo do SDK do cliente](https://tsiclientsample.azurewebsites.net/) Azure Time Series insights hospedado para ver como autenticar com as APIs do time Series insights de forma programática usando o [SDK do cliente JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos.

### <a name="http-headers"></a>Cabeçalhos HTTP

Cabeçalhos de solicitação necessários:

- `Authorization` para autenticação e autorização, um token de portador OAuth 2,0 válido deve ser passado no cabeçalho Authorization. O token deve ser emitido exatamente para o recurso de `https://api.timeseries.azure.com/` (também conhecido como "público" do token).

Cabeçalhos de solicitação opcionais:

- Há suporte para `application/json` somente `Content-type`.
- `x-ms-client-request-id`-uma ID de solicitação do cliente. O serviço registra esse valor. Permite que o serviço rastreie a operação entre serviços.
- `x-ms-client-session-id`-uma ID de sessão de cliente. O serviço registra esse valor. Permite que o serviço rastreie um grupo de operações relacionadas entre serviços.
- `x-ms-client-application-name`-nome do aplicativo que gerou essa solicitação. O serviço registra esse valor.

Cabeçalhos de resposta:

- Há suporte para `application/json` somente `Content-type`.
- `x-ms-request-id`-ID da solicitação gerada pelo servidor. Pode ser usado para contatar a Microsoft para investigar uma solicitação.

### <a name="http-parameters"></a>Parâmetros HTTP

Parâmetros de cadeia de caracteres de consulta de URL necessários:

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Parâmetros opcionais da cadeia de consulta de URL:

- `timeout=<timeout>` – tempo limite do lado do servidor para a execução da solicitação. Aplicável somente para as APIs [obter eventos de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e [obter agregações de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . O valor de tempo limite deve estar no formato de duração ISO 8601, por exemplo `"PT20S"` e deve estar no intervalo `1-30 s`. O valor padrão é `30 s`.

## <a name="next-steps"></a>Passos seguintes

- Para obter o código de exemplo que chama a API de Time Series Insights GA, consulte [consultar dados usando C# ](./time-series-insights-query-data-csharp.md).

- Para visualizar Time Series Insights exemplos de código de API, consulte [consultar dados C#de visualização usando ](./time-series-insights-update-query-data-csharp.md).

- Para obter informações de referência de API, consulte [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).
---
title: Autenticação e autorização de API-Azure Time Series Insights | Microsoft Docs
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
ms.date: 12/09/2019
ms.custom: seodec18
ms.openlocfilehash: b54034dc8828fb8a96f488197e517ef07ed55ab5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460418"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para Azure Time Series Insights API

Este documento descreve como registrar um aplicativo no Azure Active Directory usando a folha novo Azure Active Directory. Os aplicativos registrados no Azure Active Directory permitem que os usuários se autentiquem no e estejam autorizados a usar a API do Azure Time Series Insight associada a um ambiente de Time Series Insights.

> [!IMPORTANT]
> O Azure Time Series Insights dá suporte a ambas as bibliotecas de autenticação a seguir:
> * A biblioteca de [autenticação da Microsoft mais recente (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * A [Adal (biblioteca de autenticação de Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Principal de serviço

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
   > Para opções de acesso a dados avançadas, leia [concedendo acesso a dados](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicialização do aplicativo cliente

* Os desenvolvedores podem usar a [MSAL (biblioteca de autenticação da Microsoft)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) ou a [Adal (biblioteca de autenticação Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) ) para autenticar com Azure Time Series insights.

* Por exemplo, para autenticar usando a ADAL:

   1. Use a **ID do aplicativo** e o **segredo do cliente** (chave do aplicativo) na seção Azure Active Directory de registro do aplicativo para adquirir o token em nome do aplicativo.

   1. No C#, o código a seguir pode adquirir o token em nome do aplicativo. Para obter um exemplo completo, consulte [consultar dados C#usando ](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Em seguida, o token pode ser passado no cabeçalho `Authorization` quando o aplicativo chama a API Time Series Insights.

* Como alternativa, os desenvolvedores podem optar por autenticar usando MSAL. Leia sobre a [migração para o MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) para saber mais. 

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta seção descreve os cabeçalhos de solicitação HTTP comuns e os parâmetros usados para fazer consultas em relação às APIs de versão Time Series Insights GA e versão prévia. Os requisitos específicos da API são abordados com mais detalhes na [documentação de referência da API REST do time Series insights](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Leia a [referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure/) para saber mais sobre como consumir APIs REST, fazer solicitações HTTP e manipular respostas http.

### <a name="authentication"></a>Autenticação

Para executar consultas autenticadas em relação às [APIs REST do time Series insights](https://docs.microsoft.com/rest/api/time-series-insights/), um token de portador OAuth 2,0 válido deve ser passado no [cabeçalho Authorization](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) usando um cliente REST de sua escolha (post, JavaScript C#,). 

> [!TIP]
> Consulte a visualização de [exemplo do SDK do cliente](https://tsiclientsample.azurewebsites.net/) Azure Time Series insights hospedado para ver como autenticar com as APIs do time Series insights de forma programática usando o [SDK do cliente JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de solicitação necessários são descritos abaixo.

| Cabeçalho de solicitação necessário | Descrição |
| --- | --- |
| Autorização | Para autenticar com Time Series Insights, um token de portador OAuth 2,0 válido deve ser passado no cabeçalho **Authorization** . | 

> [!IMPORTANT]
> O token deve ser emitido exatamente para o recurso de `https://api.timeseries.azure.com/` (também conhecido como "público" do token).
> * O [](https://www.getpostman.com/) **AuthURL** do postmaster será, portanto,: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` é válido, mas `https://api.timeseries.azure.com` não é.

Os cabeçalhos de solicitação opcionais são descritos abaixo.

| Cabeçalho de pedido opcional | Descrição |
| --- | --- |
| Tipo de conteúdo | Há suporte apenas para `application/json`. |
| x-ms-client-request-id | Uma ID de solicitação do cliente. O serviço registra esse valor. Permite que o serviço rastreie a operação entre serviços. |
| x-MS-Client-session-ID | Uma ID de sessão de cliente. O serviço registra esse valor. Permite que o serviço rastreie um grupo de operações relacionadas entre serviços. |
| x-MS-Client-nome-do-aplicativo | Nome do aplicativo que gerou esta solicitação. O serviço registra esse valor. |

Os cabeçalhos de resposta opcionais, mas recomendados, são descritos abaixo.

| Cabeçalho de resposta | Descrição |
| --- | --- |
| Tipo de conteúdo | Há suporte apenas para `application/json`. |
| x-MS-Request-ID | ID da solicitação gerada pelo servidor. Pode ser usado para contatar a Microsoft para investigar uma solicitação. |
| x-MS-Property-não-encontrado-comportamento | Cabeçalho de resposta opcional da API GA. Os valores possíveis são `ThrowError` (padrão) ou `UseNull`. |

### <a name="http-parameters"></a>Parâmetros HTTP

> [!TIP]
> Encontre mais informações sobre as informações de consulta obrigatórias e opcionais na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/).

Os parâmetros de cadeia de caracteres de consulta de URL necessários dependem da versão da API.

| Lance | Valores de versão de API possíveis |
| --- |  --- |
| Disponibilidade Geral | `api-version=2016-12-12`|
| Pré-visualização | `api-version=2018-11-01-preview` |
| Pré-visualização | `api-version=2018-08-15-preview` |

Parâmetros opcionais da cadeia de consulta de URL incluem a definição de um tempo limite para tempos de execução de solicitação HTTP.

| Parâmetro de consulta opcional | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo limite do lado do servidor para a execução da solicitação HTTP. Aplicável somente para as APIs [obter eventos de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e [obter agregações de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . O valor de tempo limite deve estar no formato de duração ISO 8601, por exemplo `"PT20S"` e deve estar no intervalo `1-30 s`. O valor padrão é `30 s`. | GA (Geographic Availability, Disponibilidade Geográfica) |
| `storeType=<storeType>` | Para os ambientes de visualização com armazenamento quente habilitado, a consulta pode ser executada no `WarmStore` ou `ColdStore`. Esse parâmetro na consulta define em qual repositório a consulta deve ser executada. Se não estiver definido, a consulta será executada na loja Cold. Para consultar a loja a quente, **storetype** precisa ser definido como `WarmStore`. Se não estiver definido, a consulta será executada em relação à loja Cold. | Pré-visualização |

## <a name="next-steps"></a>Passos seguintes

- Para obter o código de exemplo que chama a API de Time Series Insights GA, consulte [consultar dados usando C# ](./time-series-insights-query-data-csharp.md).

- Para visualizar Time Series Insights exemplos de código de API, consulte [consultar dados C#de visualização usando ](./time-series-insights-update-query-data-csharp.md).

- Para obter informações de referência de API, consulte a documentação de [referência da API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) .

- Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).

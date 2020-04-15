---
title: Autenticação e autorização da API - Azure Time Series Insights / Microsoft Docs
description: Este artigo descreve como configurar a autenticação e a autorização para uma aplicação personalizada que chama a API da Série De Tempo Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: beefad41a270233336bb9134268c98341e81a7cd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380813"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este documento descreve como registar uma aplicação no Azure Ative Directory utilizando a nova lâmina de Diretório Ativo Azure. As aplicações registadas no Azure Ative Directory permitem aos utilizadores autenticar e ser autorizados a utilizar a API da Série De Tempo Azure associada a um ambiente time series Insights.

> [!IMPORTANT]
> A Azure Time Series Insights suporta ambas as seguintes bibliotecas de autenticação:
> * A mais recente Biblioteca de Autenticação da [Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * Biblioteca de [Autenticação de Diretório Ativo Azure (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Service principal (Principal de serviço)

As seguintes secções descrevem como configurar uma aplicação para aceder à API time Series Insights em nome de uma aplicação. A aplicação pode então consultar ou publicar dados de referência no ambiente Time Series Insights utilizando as suas credenciais de aplicação através do Azure Ative Directory.

## <a name="summary-and-best-practices"></a>Resumo e boas práticas

O fluxo de registo de aplicações azure Ative Directory envolve três passos principais.

1. [Registe uma candidatura](#azure-active-directory-app-registration) no Azure Ative Directory.
1. Autorize a aplicação a ter [acesso a dados ao ambiente Time Series Insights](#granting-data-access).
1. Utilize o ID de **aplicação** e `https://api.timeseries.azure.com/` o Segredo do **Cliente** para adquirir um símbolo na [sua aplicação](#client-app-initialization)de cliente. O símbolo pode então ser usado para chamar a Time Series Insights API.

Por **passo 3,** separar as credenciais da sua aplicação e do utilizador permite:

* Atribuir permissões à identidade da aplicação que são distintas das suas próprias permissões. Normalmente, estas permissões limitam-se apenas ao que a aplicação requer. Por exemplo, pode permitir que a aplicação leia dados apenas a partir de um ambiente específico da Time Series Insights.
* Isole a segurança da aplicação das credenciais de autenticação do utilizador criando utilizando um Segredo de **Cliente** ou certificado de segurança. Como resultado, as credenciais da aplicação não dependem das credenciais de um utilizador específico. Se o papel do utilizador mudar, a aplicação não requer necessariamente novas credenciais ou configuração posterior. Se o utilizador alterar a sua palavra-passe, todo o acesso à aplicação não necessita de novas credenciais ou chaves.
* Execute um script sem supervisão usando um Segredo de **Cliente** ou certificado de segurança em vez de credenciais específicas do utilizador (exigindo que estejam presentes).
* Utilize um certificado de segurança em vez de uma palavra-passe para garantir o acesso à sua API da Série De Tempo Azure.

> [!IMPORTANT]
> Siga o princípio da **Separação de Preocupações** (descrito para este cenário acima) ao configurar a sua política de segurança Azure Time Series Insights.

> [!NOTE]
> * O artigo centra-se num pedido de inquilino único onde o pedido se destina a ser executado numa única organização.
> * Você normalmente usará aplicações de inquilino único para aplicações de linha de negócio que funcionam na sua organização.

## <a name="detailed-setup"></a>Configuração detalhada

### <a name="azure-active-directory-app-registration"></a>Registo de aplicativos azure Ative Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concessão de acesso a dados

1. Para o ambiente Time Series Insights, selecione Políticas de Acesso a **Dados** e selecione **Adicionar**.

   [![Adicione nova política de acesso a dados ao ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na caixa de diálogo **Select User,** colhe o Nome da **Aplicação** ou o ID de **aplicação** da secção de registo de aplicações do Diretório Ativo Azure.

   [![Encontre uma aplicação na caixa de diálogo Select User](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione o papel. Selecione **Reader** para consultar dados ou **Colaborador** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Escolha leitor ou colaborador na caixa de diálogo Select User Role](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Guarde a apólice selecionando **OK**.

   > [!TIP]
   > Para opções avançadas de acesso a dados, leia [a concessão de acesso aos dados.](./time-series-insights-data-access.md)

### <a name="client-app-initialization"></a>Inicialização da aplicação do cliente

* Os desenvolvedores podem utilizar a [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) ou a [Azure Ative Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) para autenticar com insights da Série De Tempo Azure.

* Por exemplo, autenticar utilizando a ADAL:

   1. Utilize o ID de **aplicação** e o Segredo do **Cliente** (Chave de Aplicação) da secção de registo de aplicações azure Ative Directory para adquirir o símbolo em nome da aplicação.

   1. Em C#, o seguinte código pode adquirir o símbolo em nome do pedido. Para obter uma amostra completa, leia os dados da [Consulta utilizando C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. O símbolo pode então `Authorization` ser passado no cabeçalho quando a aplicação chama a API da Série De Tempo Insights.

* Em alternativa, os desenvolvedores podem optar por autenticar usando mSAL. Leia sobre [a migração para o MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) e veja os nossos dados de [referência Manage GA para um ambiente Azure Time Series Insights usando](time-series-insights-manage-reference-data-csharp.md) o artigo C# para saber mais. 

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta secção descreve cabeçalhos e parâmetros de pedido http comuns usados para fazer consultas contra as ApIs da Série de Tempo Insights GA e Preview. Os requisitos específicos da API são cobertos com maior detalhe na documentação de referência da [Time Series Insights REST API](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Leia a [Referência Da API Azure REST](https://docs.microsoft.com/rest/api/azure/) para saber mais sobre como consumir APIs REST, fazer pedidos HTTP e lidar com as respostas http.

### <a name="authentication"></a>Autenticação

Para realizar consultas autenticadas contra as APIs REST [DA Time Series,](https://docs.microsoft.com/rest/api/time-series-insights/)um token válido do portador oAuth 2.0 deve ser passado no [cabeçalho de Autorização](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) utilizando um cliente REST à sua escolha (Carteiro, JavaScript, C#). 

> [!TIP]
> Leia a visualização da [amostra de amostra sdk](https://tsiclientsample.azurewebsites.net/) do cliente Da Série De Tempo SDK para aprender a autenticar com as APIs da Série Time Insights, utilizando programaticamente o [SDK do Cliente JavaScript,](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos e gráficos.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de pedido necessários são descritos abaixo.

| Cabeçalho de pedido exigido | Descrição |
| --- | --- |
| Autorização | Para autenticar com Time Series Insights, um token OAuth 2.0 Bearer válido deve ser passado no cabeçalho **de Autorização.** | 

> [!IMPORTANT]
> O símbolo deve ser emitido `https://api.timeseries.azure.com/` exatamente para o recurso (também conhecido como o "público" do símbolo).
> * O seu [Carteiro](https://www.getpostman.com/) **AuthURL** será, portanto:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`é válido, mas `https://api.timeseries.azure.com` não é.

Os cabeçalhos de pedido opcionais são descritos abaixo.

| Cabeçalho de pedido opcional | Descrição |
| --- | --- |
| Tipo de conteúdo | só `application/json` é apoiado. |
| x-ms-cliente-request-id | Um cliente pede identificação. O serviço regista este valor. Permite que o serviço rastreie a operação através de serviços. |
| x-ms-cliente-sessão-id | Uma identificação da sessão de clientes. O serviço regista este valor. Permite que o serviço rastreie um grupo de operações relacionadas através de serviços. |
| x-ms-cliente-nome de aplicação | Nome da aplicação que gerou este pedido. O serviço regista este valor. |

Os cabeçalhos de resposta opcionais mas recomendados são descritos abaixo.

| Cabeçalho de resposta | Descrição |
| --- | --- |
| Tipo de conteúdo | Só `application/json` é apoiado. |
| x-ms-request-id | ID de pedido gerado pelo servidor. Pode ser usado para contactar a Microsoft para investigar um pedido. |
| x-ms-propriedade-não-encontrado-comportamento | Cabeçalho de resposta opcional GA API. Os valores possíveis são `ThrowError` (predefinidos) ou `UseNull`. |

### <a name="http-parameters"></a>Parâmetros HTTP

> [!TIP]
> Encontre mais informações sobre informações de consulta necessárias e opcionais na [documentação de referência.](https://docs.microsoft.com/rest/api/time-series-insights/)

Os parâmetros de cadeia de consulta de URL necessários dependem da versão API.

| Libertar | Possíveis valores da versão API |
| --- |  --- |
| Disponibilidade Geral | `api-version=2016-12-12`|
| Pré-visualização | `api-version=2018-11-01-preview` |
| Pré-visualização | `api-version=2018-08-15-preview` |

Os parâmetros opcionais de cadeia de consulta de URL incluem definir um tempo de execução para http solicitar tempos de execução.

| Parâmetro opcional de consulta | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo de tempo para a execução do pedido http. Aplicável apenas aos [Eventos](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) do Ambiente e [Obter Agregados Ambientais](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) APIs. O valor do tempo deve estar no formato `"PT20S"` de duração ISO 8601, por exemplo, e deve estar na gama `1-30 s`. O valor `30 s`predefinido é . | GA |
| `storeType=<storeType>` | Para ambientes de pré-visualização com loja quente ativada, `ColdStore`a consulta pode ser executada na ou `WarmStore` . Este parâmetro na consulta define em que armazena a consulta deve ser executada. Se não for definido, a consulta será executada na loja de frio. Para consultar a loja quente, o **storeType** precisa de ser definido para `WarmStore`. Se não for definido, a consulta será executada contra a loja de frio. | Pré-visualização |

## <a name="next-steps"></a>Passos seguintes

- Para obter o código da amostra que ligue para a API da Série De Tempo GA, leia [os dados da Consulta utilizando C#](./time-series-insights-query-data-csharp.md).

- Para visualizar amostras de código API da Série de Tempo, leia os dados de [pré-visualização da Consulta utilizando C#](./time-series-insights-update-query-data-csharp.md).

- Para obter informações sobre referência da API, leia a documentação de referência da [Consulta API.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Aprenda a criar um diretor de [serviço.](../active-directory/develop/howto-create-service-principal-portal.md)

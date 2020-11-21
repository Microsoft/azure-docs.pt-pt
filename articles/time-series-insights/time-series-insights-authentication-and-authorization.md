---
title: Autenticação e autorização da API - Azure Time Series Insights Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para uma aplicação personalizada que chama a API da Série de Tempos Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1bd3c5796658663b6111723829cbe620346002c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016246"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este documento descreve como registar uma aplicação no Azure Ative Directory utilizando a nova lâmina do Azure Ative Directory. As aplicações registadas no Azure Ative Directory permitem que os utilizadores autentem autenticar e ser autorizados a utilizar a Azure Time Series Insight API associada a um ambiente Azure Time Series Insights.

## <a name="service-principal"></a>Service principal (Principal de serviço)

As secções seguintes descrevem como configurar uma aplicação para aceder à Azure Time Series Insights API em nome de uma aplicação. A aplicação pode então consultar ou publicar dados de referência no ambiente Azure Time Series Insights utilizando as suas próprias credenciais de aplicação através do Azure Ative Directory.

## <a name="summary-and-best-practices"></a>Resumo e boas práticas

O fluxo de registo de aplicações Azure Ative Directory envolve três passos principais.

1. [Registe um requerimento](#azure-active-directory-app-registration) no Diretório Ativo Azure.
1. Autorizar a aplicação a ter [acesso a dados ao ambiente Azure Time Series Insights](#granting-data-access).
1. Utilize o **ID da Aplicação** e o **Cliente Secret** para adquirir um token `https://api.timeseries.azure.com/` da sua [aplicação cliente.](#client-app-initialization) O token pode então ser usado para chamar a AZure Time Series Insights API.

Por **passo 3,** separar as credenciais da sua aplicação e do seu utilizador permite-lhe:

* Atribua permissões à identidade da aplicação que são distintas das suas próprias permissões. Normalmente, estas permissões são restritas apenas ao que a app requer. Por exemplo, pode permitir que a aplicação leia dados apenas a partir de um determinado ambiente Azure Time Series Insights.
* Isolar a segurança da aplicação da criação de credenciais de autenticação do utilizador utilizando um **Segredo do Cliente** ou certificado de segurança. Como resultado, as credenciais da aplicação não dependem das credenciais de um utilizador específico. Se a função do utilizador mudar, a aplicação não requer necessariamente novas credenciais ou posterior configuração. Se o utilizador alterar a sua palavra-passe, todo o acesso à aplicação não necessita de novas credenciais ou chaves.
* Executar um script sem supervisão usando um certificado **de segurança** ou Segredo de Cliente em vez de credenciais específicas de um utilizador (exigindo que estejam presentes).
* Utilize um certificado de segurança em vez de uma palavra-passe para garantir o acesso à API da Série de Tempos Azure.

> [!IMPORTANT]
> Siga o princípio de **Separação de Preocupações** (descrito para este cenário acima) ao configurar a sua política de segurança Azure Time Series Insights.

> [!NOTE]

> * O artigo centra-se numa aplicação de um único inquilino em que o pedido se destina a funcionar numa única organização.
> * Você normalmente usará aplicações de inquilino único para aplicações de linha de negócio que funcionam na sua organização.

## <a name="detailed-setup"></a>Configuração detalhada

### <a name="azure-active-directory-app-registration"></a>Registo de aplicativos Azure Ative Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Conceder acesso aos dados

1. Para o ambiente Azure Time Series Insights, selecione **Políticas de Acesso a Dados** e selecione **Add**.

   [![Adicione nova política de acesso a dados ao ambiente Azure Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na caixa de diálogo **Select User,** cole o Nome de **Aplicação** ou o ID da **aplicação** Azure Ative.

   [![Encontre uma aplicação na caixa de diálogo do utilizador Select](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione o papel. Selecione **Reader** para consultar dados ou **Colaborador** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Escolha leitor ou contribuinte na caixa de diálogo de função do utilizador selecionado](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Guarde a política selecionando **OK**.

   > [!TIP]
   > Para opções avançadas de acesso a dados, leia [a concessão de acesso aos dados.](./concepts-access-policies.md)

### <a name="client-app-initialization"></a>Inicialização de aplicativos de cliente

* Os desenvolvedores podem usar a [Microsoft Authentication Library (MSAL) para autenticar com insights da Série De Tempo Azure.

* Para autenticar usando o MSAL:

   1. Utilize o ID de **aplicação** e o Segredo do **Cliente** (Chave de Aplicações) da secção de registo de aplicações Azure Ative Directory para adquirir o token em nome da aplicação.

   1. Em C#, o seguinte código pode adquirir o token em nome da aplicação. Para obter uma amostra completa de como consultar dados de um ambiente Gen1, leia [os dados de consulta utilizando C#](time-series-insights-query-data-csharp.md).

        Consulte o [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] repo para aceder ao código C#.

   1. O token pode então ser passado no `Authorization` cabeçalho quando a aplicação chama a AZure Time Series Insights API.

> [!IMPORTANT]
> Se estiver a utilizar [a Azure Ative Directory Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) leia sobre [a migração para o MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta secção descreve os cabeçalhos e parâmetros comuns de pedidos HTTP utilizados para fazer consultas contra as Azure Time Series Insights Gen1 e Gen2 APIs. Os requisitos específicos da API são cobertos com maior detalhe na documentação de referência da [Azure Time Series Insights REST API](/rest/api/time-series-insights/).

> [!TIP]
> Leia a [referência API AZure REST](/rest/api/azure/) para saber mais sobre como consumir APIs DE REST, fazer pedidos HTTP e lidar com respostas HTTP.

### <a name="authentication"></a>Autenticação

Para realizar consultas autenticadas contra o [Azure Time Series Insights REST APIs](/rest/api/time-series-insights/), um token portador OAuth 2.0 válido deve ser passado no [cabeçalho de autorização](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) usando um cliente REST à sua escolha (Carteiro, JavaScript, C#).

> [!TIP]
> Leia a visualização da [amostra do cliente SDK](https://tsiclientsample.azurewebsites.net/) da Série De Tempo Azure para aprender a autenticar com o Azure Time Series Insights APIs programáticamente utilizando o [JavaScript Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos e gráficos.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de pedido necessários são descritos abaixo.

| Cabeçalho de pedido exigido | Descrição |
| --- | --- |
| Autorização | Para autenticar com a Azure Time Series Insights, um token OAuth 2.0 Bearer válido deve ser passado no cabeçalho **de autorização.** |

> [!IMPORTANT]
> O símbolo deve ser emitido exatamente para o `https://api.timeseries.azure.com/` recurso (também conhecido como o "público" do token).

> * O [seu Carteiro](https://www.getpostman.com/) **AuthURL** será, portanto,: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` é válido, mas `https://api.timeseries.azure.com` não é.

Os cabeçalhos de pedido opcionais são descritos abaixo.

| Cabeçalho de pedido opcional | Descrição |
| --- | --- |
| Tipo de conteúdo | só `application/json` é suportado. |
| x-ms-cliente-pedido-id | Um pedido de identificação do cliente. O serviço regista este valor. Permite que o serviço rastreie a operação através dos serviços. |
| x-ms-cliente-sessão-id | Uma identificação de sessão de clientes. O serviço regista este valor. Permite que o serviço rastreie um grupo de operações relacionadas através dos serviços. |
| x-ms-cliente-nome de aplicação | Nome da aplicação que gerou este pedido. O serviço regista este valor. |

Os cabeçalhos de resposta opcionais mas recomendados são descritos abaixo.

| Cabeçalho de resposta | Descrição |
| --- | --- |
| Tipo de conteúdo | Só `application/json` é apoiado. |
| x-ms-request-id | ID de pedido gerado pelo servidor. Pode ser usado para contactar a Microsoft para investigar um pedido. |
| x-ms-propriedade-não-encontrado-comportamento | Cabeçalho de resposta opcional da API GA. Os valores possíveis são `ThrowError` (padrão) ou `UseNull` . |

### <a name="http-parameters"></a>Parâmetros HTTP

> [!TIP]
> Encontre mais informações sobre as informações de consulta necessárias e opcionais na [documentação de referência.](/rest/api/time-series-insights/)

Os parâmetros de cadeia de consulta de URL necessários dependem da versão API.

| Libertar | Possíveis valores da versão API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` e `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> A `api-version=2018-11-01-preview` versão será depreciada em breve. Recomendamos que os utilizadores mudem para a versão mais recente.

Os parâmetros de cadeia de consulta de URL opcionais incluem a definição de um tempo limite para os tempos de execução do pedido HTTP.

| Parâmetro de consulta opcional | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo de tempo do lado do servidor para a execução do pedido HTTP. Aplicável apenas aos [Eventos De Ambiente](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [Obter ApIs agregados ambientais.](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) O valor de tempo limite deve estar no formato de duração ISO 8601, por `"PT20S"` exemplo, e deve estar na gama `1-30 s` . O valor predefinido é `30 s`. | Gen1 |
| `storeType=<storeType>` | Para ambientes Gen2 com loja quente ativada, a consulta pode ser executada no `WarmStore` ou `ColdStore` . Este parâmetro na consulta define em que loja a consulta deve ser executada. Se não estiver definida, a consulta será executada na loja de frio. Para consultar a loja quente, **o storeType** precisa de ser configurado para `WarmStore` . Se não estiver definida, a consulta será executada contra a loja de frio. | Gen2 |

## <a name="next-steps"></a>Próximos passos

* Para o código de amostra que chama a Gen1 Azure Time Series Insights API, leia [os dados da Consulta Gen1 utilizando C#](./time-series-insights-query-data-csharp.md).

* Para obter o código de amostra que chama as amostras de código API da Série De Tempo Azure Gen2, leia [os dados da Consulta Gen2 utilizando C#](./time-series-insights-update-query-data-csharp.md).

* Para obter informações de referência da API, leia a documentação de referência da [API.](/rest/api/time-series-insights/gen1-query-api)

* Saiba como [criar um diretor de serviço.](../active-directory/develop/howto-create-service-principal-portal.md)
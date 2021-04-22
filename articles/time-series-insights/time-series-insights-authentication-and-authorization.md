---
title: Autenticação e autorização da API - Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para uma aplicação personalizada que chama a API da Série de Tempos Azure.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: 8e50b650eaffe3d0ec8d3d2cd1841bd139d33750
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867517"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Dependendo das necessidades do seu negócio, a sua solução pode incluir uma ou mais aplicações de clientes que utiliza para interagir com as [APIs](/rest/api/time-series-insights/reference-data-access-overview)do ambiente Azure Time Series Insights . Azure Time Series Insights executa a autenticação utilizando [tokens de segurança Azure AD com base em AAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Para autenticar o seu cliente, terá de obter um token ao portador com as permissões certas e passá-lo juntamente com as suas chamadas API. Este documento descreve vários métodos para obter credenciais que pode usar para obter um token ao portador e autenticar, incluindo a utilização de identidade gerida e o registo de aplicações do Azure Ative Directory.

## <a name="managed-identities"></a>Identidades geridas

As seguintes secções descrevem como usar uma identidade gerida a partir do Azure Ative Directory (Azure AD) para aceder à API da Série de Tempos Azure. No Azure, identidades geridas eliminam a necessidade de os desenvolvedores terem de gerir credenciais fornecendo uma identidade para o recurso Azure em Azure AD e usando-o para obter fichas do Azure Ative Directory (Azure AD). Aqui estão alguns dos benefícios da utilização de identidades geridas:

- Não precisas de gerir credenciais. As credenciais nem sequer são acessíveis a si.
- Pode utilizar identidades geridas para autenticar qualquer serviço Azure que suporte a autenticação AD Azure, incluindo o Cofre da Chave Azure.
- As identidades geridas podem ser utilizadas sem qualquer custo adicional.

Para obter mais informações sobre os dois tipos de identidades geridas leia [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Pode utilizar identidades geridas a partir do seu:

- VMs do Azure
- Serviços de Aplicações do Azure
- Funções do Azure
- Instâncias do contentor Azure
- e mais...

Consulte [os serviços Azure que suportam identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) para a lista completa.

## <a name="azure-active-directory-app-registration"></a>Registo de aplicativos Azure Ative Directory

Recomendamos a utilização de identidades geridas sempre que possível para que não seja necessário gerir as credenciais. Se o seu pedido de cliente não estiver hospedado num serviço Azure que suporte identidades geridas, pode registar a sua candidatura junto de um inquilino Azure AD. Quando regista a sua candidatura com Azure AD, está a criar uma configuração de identidade para a sua aplicação que lhe permite integrar-se com a Azure AD. Quando regista uma aplicação no [portal Azure,](https://portal.azure.com/)você escolhe se é um único inquilino (apenas acessível no seu inquilino) ou multi-inquilino (acessível em outros inquilinos) e pode configurar opcionalmente um URI redirecionador (para onde o token de acesso é enviado).

Quando tiver concluído o registo da aplicação, tem uma instância globalmente única da app (o objeto da aplicação) que vive dentro do seu inquilino ou diretório. Você também tem um ID globalmente único para a sua aplicação (a app ou iD do cliente). No portal, pode então adicionar segredos ou certificados e âmbitos para fazer a sua app funcionar, personalizar a marca da sua app no diálogo de inscrição, e muito mais.

Se registar uma aplicação no portal, um objeto de aplicação e um objeto principal de serviço são automaticamente criados no seu inquilino de casa. Se registar/criar uma aplicação utilizando as APIs do Gráfico microsoft, criar o objeto principal de serviço é um passo separado. Um objeto principal de serviço é necessário para solicitar fichas.

Certifique-se de rever a lista de verificação de [segurança](../active-directory/develop/identity-platform-integration-checklist.md#security) para a sua aplicação. Como uma boa prática, deve usar [credenciais de certificado,](../active-directory/develop/active-directory-certificate-credentials.md)não credenciais de senha (segredos de cliente).

Consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure](../active-directory/develop/app-objects-and-service-principals.md) para obter mais detalhes.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Passo 1: Crie a sua identidade gerida ou registo de aplicações

Uma vez identificado se vai usar uma identidade gerida ou um registo de aplicações, o seu próximo passo é providenciar um.

### <a name="managed-identity"></a>Identidade gerida

Os passos que utilizará para criar uma identidade gerida variarão consoante o local onde o seu código está localizado e se está ou não a criar um sistema atribuído ou identidade atribuída pelo utilizador. Leia [tipos de identidade geridos](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para entender a diferença. Uma vez selecionado o seu tipo de identidade, localize e siga o tutorial correto na [documentação](../active-directory/managed-identities-azure-resources/index.yml)de identidades geridas pela Azure AD . Lá encontrará instruções para configurar identidades geridas para:

- [VMs do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [Serviço de Aplicações e Funções Azure](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- e mais...

### <a name="application-registration"></a>Registo da aplicação

Siga os passos listados no [Registo de uma candidatura.](../active-directory/develop/quickstart-register-app.md#register-an-application)

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Passo 2: Conceder Acesso

Quando o seu ambiente Azure Time Series Insights recebe um pedido, primeiro o sinal do portador do chamador é validado. Se a validação passar, o chamador foi autenticado e, em seguida, é feita outra verificação para garantir que o chamador está autorizado a executar a ação solicitada. Para autorizar qualquer utilizador ou principal de serviço, deve primeiro conceder-lhes acesso ao ambiente, atribuindo-lhes a função Reader ou Contribuinte.

- Para conceder acesso através do [portal Azure](https://portal.azure.com/) UI, siga as instruções enumeradas no [acesso de dados grant a um](concepts-access-policies.md) artigo ambiental. Ao selecionar o utilizador, pode procurar o registo de identidade ou aplicação gerido pelo seu nome ou por ID.

- Para conceder acesso através do Azure CLI, executar o seguinte comando. Reveja [aqui](/cli/azure/tsi/access-policy) a documentação para obter a lista completa de comandos disponíveis para gerir o acesso.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> A extensão de timeeriesinsights para Azure CLI requer a versão 2.11.0 ou superior. A extensão instalará automaticamente a primeira vez que executar um comando de política de acesso az tsi. [Saiba mais](/cli/azure/azure-cli-extensions-overview) sobre extensões.

## <a name="step-3-requesting-tokens"></a>Passo 3: Solicitação de Tokens

Uma vez que o seu registo de identidade ou aplicação gerido tenha sido aprovisionado e atribuído uma função, está pronto para começar a usá-lo para solicitar fichas portadoras OAuth 2.0. O método que utiliza para obter um token diferirá consoante o local onde o seu código está hospedado e a sua língua de eleição. Ao especificar o recurso (também conhecido como "público" do token), pode identificar Azure Time Series Insights pelo seu URL ou GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Se utilizar o URL como ID de recurso, o token deve ser emitido exatamente para `https://api.timeseries.azure.com/` . O corte de fuga é necessário.

> * Se utilizar [o Carteiro](https://www.getpostman.com/) o seu **AuthURL** será: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` é válido, mas `https://api.timeseries.azure.com` não é.

### <a name="managed-identities"></a>Identidades geridas

Ao aceder a partir do Azure App Service ou Funções siga a orientação nos [tokens Obter recursos Azure](../app-service/overview-managed-identity.md).

Para aplicações e funções .NET, a forma mais simples de trabalhar com uma identidade gerida é através da biblioteca cliente [de Identidade Azure](/dotnet/api/overview/azure/identity-readme) para .NET. Esta biblioteca de clientes é popular devido à sua simplicidade e benefícios de segurança. Os desenvolvedores podem escrever código uma vez e permitir que a biblioteca do cliente determine como autenticar com base no ambiente de aplicação - seja numa estação de trabalho de desenvolvedor usando a conta de um desenvolvedor ou implantada em Azure usando uma identidade de serviço gerida. Para orientação de migração da biblioteca antecessora AppAuthentication leia [AppAuthentication to Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration).

Solicite um token para Azure Time Series Insights usando C# e a biblioteca de clientes de identidade Azure para .NET:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Registo de aplicações

* Os desenvolvedores podem usar a [Biblioteca de Autenticação da Microsoft](../active-directory/develop/msal-overview.md) (MSAL) para obter fichas para registos de aplicações.

A MSAL pode ser usada em muitos cenários de aplicação, incluindo, mas não se limitando a:

* [Aplicações de página única (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Assinatura de aplicação web em um utilizador e chamando uma API web em nome do utilizador](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Web API chamando outra API web a jusante em nome do utilizador inscrito](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Aplicação de ambiente de trabalho chamando uma API web em nome do utilizador inscrito](../active-directory/develop/scenario-desktop-overview.md)
* [Aplicação móvel chamando uma API web em nome do utilizador que se inscreveu interativamente](../active-directory/develop/scenario-mobile-overview.md).
* [Aplicação de ambiente de trabalho/serviço da daemon chamando a API web em nome de si mesma](../active-directory/develop/scenario-daemon-overview.md)

Para obter o código C# da amostra que mostra como adquirir um token como registo de aplicações e dados de consulta de um ambiente Gen2, consulte a aplicação da amostra no [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Se estiver a utilizar [a Azure Ative Directory Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) leia sobre [a migração para o MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta secção descreve os cabeçalhos e parâmetros comuns de pedidos HTTP utilizados para fazer consultas contra as Azure Time Series Insights Gen1 e Gen2 APIs. Os requisitos específicos da API são cobertos com maior detalhe na documentação de referência da [Azure Time Series Insights REST API](/rest/api/time-series-insights/).

> [!TIP]
> Leia a [referência API AZure REST](/rest/api/azure/) para saber mais sobre como consumir APIs DE REST, fazer pedidos HTTP e lidar com respostas HTTP.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de pedido necessários são descritos abaixo.

| Cabeçalho de pedido exigido | Description |
| --- | --- |
| Autorização | Para autenticar com a Azure Time Series Insights, um símbolo de portador OAuth 2.0 válido deve ser passado no [cabeçalho de autorização](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Leia a visualização da [amostra do cliente SDK](https://tsiclientsample.azurewebsites.net/) da Série De Tempo Azure para aprender a autenticar com o Azure Time Series Insights APIs programáticamente utilizando o [JavaScript Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos e gráficos.

Os cabeçalhos de pedido opcionais são descritos abaixo.

| Cabeçalho de pedido opcional | Description |
| --- | --- |
| Tipo de conteúdo | só `application/json` é suportado. |
| x-ms-cliente-pedido-id | Um pedido de identificação do cliente. O serviço regista este valor. Permite que o serviço rastreie a operação através dos serviços. |
| x-ms-cliente-sessão-id | Uma identificação de sessão de clientes. O serviço regista este valor. Permite que o serviço rastreie um grupo de operações relacionadas através dos serviços. |
| x-ms-cliente-nome de aplicação | Nome da aplicação que gerou este pedido. O serviço regista este valor. |

Os cabeçalhos de resposta opcionais mas recomendados são descritos abaixo.

| Cabeçalho de resposta | Description |
| --- | --- |
| Tipo de conteúdo | Só `application/json` é apoiado. |
| x-ms-request-id | ID de pedido gerado pelo servidor. Pode ser usado para contactar a Microsoft para investigar um pedido. |
| x-ms-propriedade-não-encontrado-comportamento | Cabeçalho de resposta opcional da API GA. Os valores possíveis são `ThrowError` (padrão) ou `UseNull` . |

### <a name="http-parameters"></a>Parâmetros HTTP

> [!TIP]
> Encontre mais informações sobre as informações de consulta necessárias e opcionais na [documentação de referência.](/rest/api/time-series-insights/)

Os parâmetros de cadeia de consulta de URL necessários dependem da versão API.

| Libertar | Valores da versão API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Os parâmetros de cadeia de consulta de URL opcionais incluem a definição de um tempo limite para os tempos de execução do pedido HTTP.

| Parâmetro de consulta opcional | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo de tempo do lado do servidor para a execução do pedido HTTP. Aplicável apenas aos [Eventos De Ambiente](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [Obter ApIs agregados ambientais.](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) O valor de tempo limite deve estar no formato de duração ISO 8601, por `"PT20S"` exemplo, e deve estar na gama `1-30 s` . O valor predefinido é `30 s`. | Gen1 |
| `storeType=<storeType>` | Para ambientes Gen2 com loja quente ativada, a consulta pode ser executada no `WarmStore` ou `ColdStore` . Este parâmetro na consulta define em que loja a consulta deve ser executada. Se não estiver definida, a consulta será executada na loja de frio. Para consultar a loja quente, **o storeType** precisa de ser configurado para `WarmStore` . Se não estiver definida, a consulta será executada contra a loja de frio. | Gen2 |

## <a name="next-steps"></a>Passos seguintes

* Para o código de amostra que chama a Gen1 Azure Time Series Insights API, leia [os dados da Consulta Gen1 utilizando C#](./time-series-insights-query-data-csharp.md).

* Para obter o código de amostra que chama as amostras de código API da Série De Tempo Azure Gen2, leia [os dados da Consulta Gen2 utilizando C#](./time-series-insights-update-query-data-csharp.md).

* Para obter informações de referência da API, leia a documentação de referência da [API.](/rest/api/time-series-insights/reference-query-apis)

---
title: Colocação em cache personalizada da Gestão de API do Azure
description: Saiba como cache itens por chave na Azure API Management. Pode modificar a tecla utilizando cabeçalhos de pedido.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: cf9901b4e49460dd2fb91dceaf239571058c5284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213326"
---
# <a name="custom-caching-in-azure-api-management"></a>Colocação em cache personalizada da Gestão de API do Azure
O serviço de gestão API da Azure tem suporte integrado para [o caching de resposta HTTP](api-management-howto-cache.md) utilizando o URL de recurso como chave. A chave pode ser modificada através de cabeçalhos de pedido utilizando as `vary-by` propriedades. Isto é útil para cache respostas HTTP inteiras (também conhecidas como representações), mas às vezes é útil apenas cache uma parte de uma representação. As novas políticas [de valor de cache-lookup](./api-management-caching-policies.md#GetFromCacheByKey) e [cache-store-value](./api-management-caching-policies.md#StoreToCacheByKey) proporcionam a capacidade de armazenar e recuperar pedaços arbitrários de dados dentro das definições políticas. Esta capacidade também acrescenta valor à política [de pedido de envio](./api-management-advanced-policies.md#SendRequest) anteriormente introduzida, uma vez que agora pode cache respostas de serviços externos.

## <a name="architecture"></a>Arquitetura
O serviço de Gestão API utiliza uma cache de dados partilhada por inquilino para que, à medida que escala até várias unidades, ainda tenha acesso aos mesmos dados em cache. No entanto, quando se trabalha com uma implantação multi-região, existem caches independentes em cada uma das regiões. É importante não tratar a cache como uma loja de dados, onde é a única fonte de alguma informação. Se o fez, e mais tarde decidiu aproveitar a implantação multi-região, então os clientes com utilizadores que viajam podem perder acesso a esses dados em cache.

## <a name="fragment-caching"></a>Caching fragmento
Existem certos casos em que as respostas devolvidas contêm uma parte dos dados que é dispendioso de determinar e ainda permanece fresco por um período de tempo razoável. Como exemplo, considere um serviço construído por uma companhia aérea que forneça informações relativas a reservas de voo, estado de voo, etc. Se o utilizador for membro do programa de pontos das companhias aéreas, também terá informações relativas ao seu estado atual e quilometragem acumulada. Estas informações relacionadas com o utilizador podem ser armazenadas num sistema diferente, mas pode ser desejável incluí-la em respostas devolvidas sobre o estado do voo e reservas. Isto pode ser feito usando um processo chamado caching fragmento. A representação primária pode ser devolvida do servidor de origem usando algum tipo de token para indicar onde as informações relacionadas com o utilizador devem ser inseridas. 

Considere a seguinte resposta JSON de uma API de backend.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

E recurso secundário `/userprofile/{userid}` que parece,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Para determinar as informações adequadas do utilizador a incluir, a API Management precisa de identificar quem é o utilizador final. Este mecanismo é dependente da implementação. Como exemplo, estou a usar a `Subject` reivindicação de um `JWT` símbolo. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

A API Management armazena o `enduserid` valor numa variável de contexto para posterior utilização. O próximo passo é determinar se um pedido anterior já recuperou a informação do utilizador e a armazenou na cache. Para isso, a API Management utiliza a `cache-lookup-value` política.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Se não houver entrada na cache que corresponda ao valor chave, então não `userprofile` é criada nenhuma variável de contexto. A API Management verifica o sucesso da procura utilizando a política de `choose` fluxo de controlo.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Se a variável de `userprofile` contexto não existir, então a API Management terá de fazer um pedido HTTP para a recuperar.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

A API Management utiliza o `enduserid` para construir o URL para o recurso de perfil do utilizador. Uma vez que a API Management tem a resposta, retira o texto do corpo da resposta e armazena-o de volta numa variável de contexto.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Para evitar que a API Management volte a fazer este pedido HTTP, quando o mesmo utilizador fizer outro pedido, pode especificar para armazenar o perfil do utilizador na cache.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

A API Management armazena o valor na cache utilizando a mesma chave com que a API Management tentou originalmente recuperá-la. A duração que a API Management opta por armazenar o valor deve basear-se na frequência com que a informação muda e na forma como os utilizadores tolerantes são para informações desatualizadas. 

É importante perceber que a recuperação da cache ainda é um pedido fora de processo, de rede e potencialmente ainda pode adicionar dezenas de milissegundos ao pedido. Os benefícios surgem ao determinar que as informações do perfil do utilizador demoram mais tempo do que isso devido à necessidade de fazer consultas de base de dados ou de informações agregadas a partir de várias back-ends.

O passo final no processo é atualizar a resposta devolvida com a informação do perfil do utilizador.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Pode optar por incluir as aspas como parte do token para que, mesmo quando a substituição não ocorra, a resposta ainda é um JSON válido.  

Uma vez combinados todos estes passos, o resultado final é uma política que se parece com a seguinte.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Esta abordagem de caching é usada principalmente em sites web onde HTML é composto no lado do servidor para que possa ser renderizado como uma única página. Também pode ser útil em APIs onde os clientes não podem fazer o caching HTTP do lado do cliente ou é desejável não colocar essa responsabilidade no cliente.

Este mesmo tipo de cache de fragmento também pode ser feito nos servidores web backend usando um servidor de cache Redis, no entanto, usar o serviço de Gestão API para executar este trabalho é útil quando os fragmentos em cache vêm de diferentes back-ends do que as respostas primárias.

## <a name="transparent-versioning"></a>Versão transparente
É prática comum que várias versões de implementação diferentes de uma API sejam suportadas a qualquer momento. Por exemplo, apoiar diferentes ambientes (dev, teste, produção, etc.) ou apoiar versões mais antigas da API para dar tempo aos consumidores de API migrarem para versões mais recentes. 

Uma abordagem para lidar com esta situação, em vez de exigir que os desenvolvedores de clientes mudem os URLs de `/v1/customers` para fora é armazenar nos `/v2/customers` dados de perfil do consumidor que versão da API que atualmente desejam usar e chamar o URL de backend apropriado. Para determinar o URL de backend correto para chamar um cliente em particular, é necessário consultar alguns dados de configuração. Ao caching estes dados de configuração, a API Management pode minimizar a penalidade de desempenho de fazer este lookup.

O primeiro passo é determinar o identificador utilizado para configurar a versão desejada. Neste exemplo, optei por associar a versão à chave de subscrição do produto. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

A API Management faz então uma procura de cache para ver se já recuperou a versão do cliente desejada.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Em seguida, a API Management verifica se não a encontrou na cache.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Se a API Management não a encontrou, a API Management recupera-a.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Extrair o texto do corpo de resposta da resposta.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Guarde-o de volta na cache para utilização futura.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

E, finalmente, atualize o URL de back-end para selecionar a versão do serviço desejado pelo cliente.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

A política completa é a seguinte:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Permitir aos consumidores da API controlar de forma transparente a versão backend que está a ser acedida pelos clientes sem ter de atualizar e redistribuir clientes é uma solução elegante que aborda muitas preocupações de versões API.

## <a name="tenant-isolation"></a>Isolamento do Inquilino
Em implantações maiores e multi-inquilinos algumas empresas criam grupos separados de inquilinos em implementações distintas de hardware backend. Isto minimiza o número de clientes que são impactados por um problema de hardware no backend. Também permite que novas versões de software sejam lançadas por etapas. Idealmente, esta arquitetura backend deve ser transparente para os consumidores de API. Isto pode ser conseguido de forma semelhante à versão transparente porque é baseado na mesma técnica de manipular o URL backend usando o estado de configuração por chave API.  

Em vez de devolver uma versão preferida da API para cada chave de subscrição, você devolveria um identificador que relaciona um inquilino com o grupo de hardware atribuído. Este identificador pode ser usado para construir o URL de backend apropriado.

## <a name="summary"></a>Resumo
A liberdade de utilizar a cache de gestão da Azure API para armazenar qualquer tipo de dados permite um acesso eficiente a dados de configuração que podem afetar a forma como um pedido de entrada é processado. Também pode ser usado para armazenar fragmentos de dados que podem aumentar as respostas, devolvidas de uma API de backend.

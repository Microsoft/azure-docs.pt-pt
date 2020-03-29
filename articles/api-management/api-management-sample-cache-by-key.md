---
title: Colocação em cache personalizada da Gestão de API do Azure
description: Saiba como cache itens por chave na Gestão da API Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60780826"
---
# <a name="custom-caching-in-azure-api-management"></a>Colocação em cache personalizada da Gestão de API do Azure
O serviço de gestão azure API tem suporte incorporado para o [cache de resposta HTTP](api-management-howto-cache.md) usando o URL de recursos como chave. A chave pode ser modificada por `vary-by` cabeçalhos de pedido utilizando as propriedades. Isto é útil para caching respostas http inteiras (aka representações), mas às vezes é útil apenas cache uma parte de uma representação. As novas políticas de [valor de cache-lookup](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) e [cache-store-value](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) fornecem a capacidade de armazenar e recuperar pedaços arbitrários de dados a partir de definições de políticas. Esta capacidade também acrescenta valor à política de [pedido de envio](/azure/api-management/api-management-advanced-policies#SendRequest) previamente introduzida, pois agora pode cache respostas de serviços externos.

## <a name="architecture"></a>Arquitetura
O serviço de Gestão API utiliza uma cache de dados partilhada por inquilino para que, à medida que se escala para várias unidades, ainda tenha acesso aos mesmos dados em cache. No entanto, quando se trabalha com uma implantação multi-região, existem caches independentes em cada uma das regiões. É importante não tratar a cache como uma loja de dados, onde é a única fonte de alguma informação. Se o fez, e mais tarde decidiu aproveitar a implementação em várias regiões, então os clientes com utilizadores que viajam podem perder acesso a esses dados em cache.

## <a name="fragment-caching"></a>Fragmento de cache
Há certos casos em que as respostas que são devolvidas contêm alguma parte dos dados que são dispendiosos de determinar e ainda permanecem frescos por um período razoável de tempo. Como exemplo, considere um serviço construído por uma companhia aérea que forneça informações relativas às reservas de voo, estado do voo, etc. Se o utilizador for membro do programa de pontos das companhias aéreas, também disporão de informações relacionadas com o seu estado atual e quilometragem acumulada. Estas informações relacionadas com o utilizador podem ser armazenadas num sistema diferente, mas pode ser desejável incluí-la em respostas devolvidas sobre o estado do voo e reservas. Isto pode ser feito usando um processo chamado de cache de fragmento. A representação primária pode ser devolvida do servidor de origem usando algum tipo de ficha para indicar onde as informações relacionadas com o utilizador devem ser inseridas. 

Considere a seguinte resposta da JSON de uma API de backend.

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

Para determinar as informações apropriadas do utilizador a incluir, a API Management precisa de identificar quem é o utilizador final. Este mecanismo é dependente da implementação. Como exemplo, estou a `Subject` usar `JWT` a reivindicação de um símbolo. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

A API `enduserid` Management armazena o valor numa variável de contexto para posterior utilização. O próximo passo é determinar se um pedido anterior já recuperou as informações do utilizador e armazenou-as na cache. Para isso, a API Management utiliza a `cache-lookup-value` política.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Se não houver entrada na cache que corresponda ao `userprofile` valor-chave, então não é criada nenhuma variável de contexto. A API Management verifica o sucesso `choose` da procura utilizando a política de fluxo de controlo.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Se `userprofile` a variável de contexto não existir, então a API Management terá de fazer um pedido http para recuperá-la.

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

A API `enduserid` Management utiliza o URL para construir o URL para o recurso de perfil do utilizador. Uma vez que a API Management tem a resposta, ele retira o texto do corpo da resposta e armazena-o de volta para uma variável de contexto.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Para evitar que a API Management volte a fazer este pedido http, quando o mesmo utilizador efaz outro pedido, pode especificar para armazenar o perfil do utilizador na cache.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

A API Management armazena o valor na cache utilizando exatamente a mesma chave com que a API Management tentou originalmente recuperá-lo. A duração que a API Management opta por armazenar o valor deve basear-se na frequência com que a informação muda e na forma como os utilizadores tolerantes são para informações desatualizadas. 

É importante perceber que a recuperação da cache ainda é um pedido fora de processo, de rede e potencialmente ainda pode adicionar dezenas de milissegundos ao pedido. Os benefícios vêm ao determinar que as informações sobre o perfil do utilizador demoram mais tempo do que isso devido à necessidade de fazer consultas de base de dados ou agregar informações de várias extremidades traseiras.

O passo final no processo é atualizar a resposta devolvida com as informações do perfil do utilizador.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Pode optar por incluir as aspas como parte do símbolo para que, mesmo quando a substituição não ocorra, a resposta ainda seja um JSON válido.  

Uma vez que se combinam todos estes passos, o resultado final é uma política que se parece com a seguinte.

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

Esta abordagem de cache é usada principalmente em web sites onde HTML é composto no lado do servidor para que possa ser renderizado como uma única página. Também pode ser útil em APIs onde os clientes não podem fazer o cache do lado do cliente EM HTTP ou é desejável não colocar essa responsabilidade no cliente.

Este mesmo tipo de fragmento de cache também pode ser feito nos servidores web backend usando um servidor redis caching, no entanto, usar o serviço de Gestão API para realizar este trabalho é útil quando os fragmentos em cache são provenientes de diferentes back-ends do que os primários respostas.

## <a name="transparent-versioning"></a>Versão transparente
É prática comum que várias versões de implementação diferentes de uma API sejam suportadas a qualquer momento. Por exemplo, apoiar diferentes ambientes (dev, teste, produção, etc.) ou apoiar versões mais antigas da API para dar tempo aos consumidores de API migrarem para versões mais recentes. 

Uma abordagem para lidar com isto, em vez de `/v1/customers` `/v2/customers` exigir que os desenvolvedores de clientes alterem os URLs de para é armazenar nos dados de perfil do consumidor qual versão da API que desejam atualmente utilizar e chamar o URL de backend apropriado. Para determinar o URL de backend correto para chamar um determinado cliente, é necessário consultar alguns dados de configuração. Ao cortar estes dados de configuração, a API Management pode minimizar a penalização de desempenho de fazer esta procura.

O primeiro passo é determinar o identificador utilizado para configurar a versão desejada. Neste exemplo, optei por associar a versão à chave de subscrição do produto. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

A API Management faz então uma procura em cache para ver se já recuperou a versão decliente desejada.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Em seguida, a API Management verifica se não a encontrou no cache.

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

Guarde-o de volta na cache para uso futuro.

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

Permitir aos consumidores da API controlar de forma transparente a versão backend que está a ser acedida pelos clientes sem ter de atualizar e redistribuir clientes é uma solução elegante que aborda muitas preocupações de versão API.

## <a name="tenant-isolation"></a>Isolamento de inquilino
Em maiores implantações multi-inquilinos algumas empresas criam grupos separados de inquilinos em implementações distintas de hardware backend. Isto minimiza o número de clientes que são afetados por um problema de hardware no backend. Também permite que novas versões de software sejam lançadas por etapas. Idealmente, esta arquitetura de backend deve ser transparente para os consumidores de API. Isto pode ser conseguido de forma semelhante à versão transparente porque se baseia na mesma técnica de manipulação do URL de backend utilizando o estado de configuração por chave API.  

Em vez de devolver uma versão preferida da API para cada chave de subscrição, devolveria um identificador que relacionasse um inquilino com o grupo de hardware atribuído. Este identificador pode ser usado para construir o URL traseiro apropriado.

## <a name="summary"></a>Resumo
A liberdade de utilização do cache de gestão da API Azure para armazenar qualquer tipo de dados permite um acesso eficiente a dados de configuração que possam afetar a forma como um pedido de entrada é processado. Também pode ser usado para armazenar fragmentos de dados que podem aumentar as respostas, devolvidos de uma API de backend.

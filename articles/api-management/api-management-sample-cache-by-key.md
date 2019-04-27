---
title: A colocação em cache personalizada na gestão de API do Azure
description: Saiba como colocar em cache itens por chave na gestão de API do Azure
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780826"
---
# <a name="custom-caching-in-azure-api-management"></a>A colocação em cache personalizada na gestão de API do Azure
O serviço de gestão de API do Azure tem suporte incorporado para [colocação em cache de resposta HTTP](api-management-howto-cache.md) usando o URL de recurso como chave. A chave pode ser modificada por cabeçalhos de solicitação usando o `vary-by` propriedades. Isto é útil para a colocação em cache as respostas HTTP inteiras (também conhecido como representações), mas às vezes é útil para a cache de apenas uma parte de uma representação. A nova [valor de pesquisa de cache](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) e [valor do arquivo do cache](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) políticas oferecem a capacidade de armazenar e obter partes arbitrárias de dados a partir de definições de política. Esta capacidade também adiciona valor ao anteriormente introduzido [pedido de envio](/azure/api-management/api-management-advanced-policies#SendRequest) política porque agora pode colocar em cache as respostas dos serviços externos.

## <a name="architecture"></a>Arquitetura
Dados em cache usos de serviço de gestão de API um dados por inquilino partilhado em cache para que, como aumente verticalmente para várias unidades ainda obter acesso às mesmas. No entanto, ao trabalhar com uma implementação em várias regiões existem caches independentes dentro de cada uma das regiões. É importante não tratar o cache como um arquivo de dados, em que é a única fonte de alguma parte das informações. Se fez e decidir posteriormente tirar partido da implementação de várias regiões, em seguida, os clientes com os utilizadores que viajam podem perder o acesso a esses dados em cache.

## <a name="fragment-caching"></a>Cache fragmentado
Há determinados casos em que as respostas serem devolvidas contenham uma parte dos dados que é Caro para determinar e ainda permanecem atualizados durante um período de tempo razoável. Por exemplo, considere um serviço criado por uma companhia aérea que fornece informações relacionadas com reservas de voo, estado de voos, etc. Se o utilizador é membro do programa de pontos de companhias aéreas, eles também teria de obter informações relacionadas com o respetivo estado atual e acumulados quantidade. Estas informações relacionadas ao usuário podem ser armazenadas num sistema diferente, mas, talvez seja desejável para incluí-la na resposta retornada sobre reservas e estado de voo. Isso pode ser feito através de um processo chamado de cache fragmentado. A representação principal pode ser devolvida a partir do servidor de origem usando algum tipo de token para indicar onde as informações relacionadas com o utilizador estão a ser inserido. 

Considere a seguinte resposta JSON de uma API de back-end.

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

E de recursos secundários em `/userprofile/{userid}` que se parece com,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Para determinar as informações de utilizador adequada para incluir, gestão de API tem de identificar quem é o utilizador final. Esse mecanismo é dependente de implementação. Por exemplo, estou usando o `Subject` de afirmação de um `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Arquivos de gestão de API a `enduserid` valor numa variável de contexto para utilização posterior. A próxima etapa é determinar se um pedido anterior já tem obter as informações de utilizador e guardá-la no cache. Para isso, a gestão de API utiliza o `cache-lookup-value` política.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Se não houver nenhuma entrada na cache que corresponde ao valor da chave, em seguida, não `userprofile` variável de contexto é criada. Gestão de API verifica o êxito de utilizar a pesquisa a `choose` diretiva de fluxo de controle.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Se o `userprofile` variável de contexto não existe, em seguida, a gestão de API vai fazer uma solicitação HTTP para recuperá-la.

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

Gestão de API utiliza o `enduserid` para construir o URL para o recurso de perfil do usuário. Assim que a gestão de API tem a resposta, ele extrai o texto de corpo de resposta e armazena-a novamente para uma variável de contexto.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Para evitar a gestão de API de efetuar este pedido HTTP mais uma vez, quando o mesmo utilizador fizer outro pedido, pode especificar para armazenar o perfil de utilizador na cache.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Gestão de API armazena o valor no cache usando a mesma chave exata que a gestão de API tentou originalmente recuperá-la com. O período em que a gestão de API escolhe para armazenar o valor deve basear-se sobre como muitas vezes, as alterações de informações e os utilizadores de como a tolerância a falhas são informações de Desatualizadas. 

É importante perceber que a obtenção da cache ainda é uma fora do processo, o pedido de rede e potencialmente pode ainda adicionar dezenas de milissegundos para o pedido. Os benefícios são fornecidos ao determinar que as informações de perfil do usuário demora mais tempo do que isso devido a necessidade de bases de dados consultas ou agregar informações de vários back-ends.

A etapa final do processo é atualizar a resposta retornada com as informações de perfil do usuário.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Pode optar por incluir as aspas como parte do token, para que, mesmo quando a substituição não ocorre, a resposta ainda é um JSON válido.  

Depois de combinar todas essas etapas juntos, o resultado final é uma política que se parece com o seguinte.

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

Essa abordagem de colocação em cache é usada principalmente em web sites em que o HTML é composto no lado do servidor para que ele pode ser composto como uma única página. Também pode ser útil em APIs em que os clientes não é possível fazer a cache do lado do cliente HTTP ou é desejável não colocar essa responsabilidade no cliente.

Esse tipo de cache fragmentado também pode ser feito nos servidores de web de back-end com um servidor de colocação em cache de Redis, no entanto, utilizar o serviço de gestão de API para realizar esse trabalho é útil, quando os fragmentos em cache são provenientes de back-ends diferentes do que o primário respostas.

## <a name="transparent-versioning"></a>Controlo de versões transparente
É prática comum para várias versões de implementação diferente de uma API de suporte ao mesmo tempo. Por exemplo, para suportar ambientes diferentes (desenvolvimento, teste, produção, etc.) ou para suporte às versões anteriores da API para dar tempo para os consumidores de APIS migrar para as versões mais recentes. 

Uma abordagem para lidar com isso, em vez de exigir que os desenvolvedores de cliente alterar os URLs de `/v1/customers` para `/v2/customers` é armazenar dados de perfil do consumidor, qual é a versão da API atualmente que desejam usar e chamar o URL de back-end adequado. Para determinar o URL de back-end corretos para chamar para um cliente específico, é necessário consultar alguns dados de configuração. Ao colocar em cache estes dados de configuração, gestão de API pode minimizar a penalidade de desempenho de fazer esta pesquisa.

A primeira etapa é determinar o identificador utilizado para configurar a versão pretendida. Neste exemplo, optei por associar a versão para a chave de subscrição do produto. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Gestão de API, em seguida, realiza uma pesquisa de cache para ver se já obter a versão de cliente pretendido.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Em seguida, a gestão de API verifica para ver se ele não encontrou-lo na cache.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Se não encontrar a gestão de API, gestão de API obtém os mesmos.

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

Extraia o texto de corpo de resposta da resposta.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Store-lo em cache para utilização futura.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

E, por fim, atualize o URL de back-end para selecionar a versão do serviço pretendido pelo cliente.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

A política de conclua é o seguinte:

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

Permitindo que os consumidores de APIS de forma transparente controlar qual versão de back-end está sendo acessado por clientes, sem ter de atualizar e Reimplementar os clientes é uma solução elegante que aborda muitas questões de controle de versão de API.

## <a name="tenant-isolation"></a>Isolamento de inquilino
Em Implantações maiores e multi-inquilinos algumas empresas, crie grupos separados de inquilinos em diferentes implementações de hardware de back-end. Isso minimiza o número de clientes que são afetadas por um problema de hardware no back-end. Também permite que novas versões de software ser implementada em fases. O ideal é que esta arquitetura de back-end deve ser transparente para os consumidores de APIS. Isso pode ser obtido de forma semelhante ao controle de versão transparente porque baseia-se a mesma técnica de manipular o URL de back-end com estado de configuração por chave de API.  

Em vez de retornar uma versão preferencial da API para cada chave de assinatura, retornará um identificador que está relacionado com um inquilino para o grupo de hardware atribuídos. Esse identificador pode ser utilizado para construir o URL de back-end adequado.

## <a name="summary"></a>Resumo
A liberdade de utilizar a cache da gestão de API do Azure para armazenar qualquer tipo de dados permite acesso eficiente aos dados de configuração que podem afetar a forma como uma solicitação de entrada é processada. Também pode ser utilizado para armazenar fragmentos de dados que podem aumentar as respostas, retornadas de um API de back-end.

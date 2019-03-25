---
title: Políticas avançadas de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre as políticas avançadas disponíveis para utilização na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 9d3bc50e1578704de029d53c0b1eaa21e74182cf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401924"
---
# <a name="api-management-advanced-policies"></a>Políticas avançadas de gestão de API

Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para informações sobre como adicionar e configurar as políticas, consulte [políticas de gestão de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a> Políticas Avançadas

-   [Controlar o fluxo](api-management-advanced-policies.md#choose) - condicionalmente aplica-se as declarações de política com base nos resultados da avaliação da booleano [expressões](api-management-policy-expressions.md).
-   [Reencaminhar pedido](#ForwardRequest) -reencaminha o pedido para o serviço de back-end.
-   [Limitar a simultaneidade](#LimitConcurrency) -impede entre as políticas de execução em mais do que o número especificado de pedidos de cada vez.
-   [Registo para o Hub de eventos](#log-to-eventhub) -envia mensagens no formato especificado para um Hub de eventos definidos por uma entidade de registo.
-   [Simular a resposta](#mock-response) -anulações de execução do pipeline e devolver uma resposta simulada diretamente para o chamador.
-   [Repita](#Retry) -repete a execução das declarações a política circunscritas, se e até que a condição é cumprida. A execução será repetida em intervalos de tempo especificados e até especificado o número de tentativas.
-   [Devolver a resposta](#ReturnResponse) -anulações de execução do pipeline e devolve a resposta especificada diretamente para o chamador.
-   [Enviar pedido unidirecional](#SendOneWayRequest) -envia um pedido para o URL especificado sem aguardar uma resposta.
-   [Enviar pedido](#SendRequest) -envia um pedido para o URL especificado.
-   [Definir o HTTP proxy](#SetHttpProxy) -permite-lhe a pedidos de rota reencaminhado através de um proxy HTTP.
-   [Definir o método de pedido](#SetRequestMethod) -permite-lhe alterar o método HTTP para um pedido.
-   [Definir o código de estado](#SetStatus) -altera o código de estado HTTP para o valor especificado.
-   [Definir variável](api-management-advanced-policies.md#set-variable) -persistir um valor numa determinada [contexto](api-management-policy-expressions.md#ContextVariables) variável para acesso posterior.
-   [Rastreio](#Trace) -adiciona uma cadeia de caracteres para o [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) saída.
-   [Aguarde](#Wait) -tem de aguardar para incluído [pedido de envio](api-management-advanced-policies.md#SendRequest), [obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey), ou [controlar o fluxo de](api-management-advanced-policies.md#choose) políticas para concluir antes de continuar.

## <a name="choose"></a> Fluxo de controlo

O `choose` política se aplica a política circunscritas instruções com base no resultado final da avaliação de expressões booleanas, semelhantes a um if-then-else ou de um comutador de construir numa linguagem de programação.

### <a name="ChoosePolicyStatement"></a> Declaração de política

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

A política de controlo de fluxo tem de conter, pelo menos, um `<when/>` elemento. O `<otherwise/>` elemento é opcional. Condições na `<when/>` elementos são avaliados por ordem de sua aparência na política. Declarações de política, incluídas entre a primeira `<when/>` elemento com o atributo de condição é igual a `true` serão aplicadas. As políticas, incluída entre o `<otherwise/>` elemento, se estiverem presentes, será aplicado se todos os do `<when/>` são atributos do elemento condição `false`.

### <a name="examples"></a>Exemplos

#### <a name="ChooseExample"></a> Exemplo

O exemplo seguinte demonstra um [definir variável](api-management-advanced-policies.md#set-variable) política e duas políticas de fluxo de controle.

Definir política de variável é na secção de entrada e cria um `isMobile` booleano [contexto](api-management-policy-expressions.md#ContextVariables) variável que está definido como VERDADEIRO se o `User-Agent` cabeçalho contém o texto de pedido `iPad` ou `iPhone`.

A primeira diretiva de fluxo de controle é também na secção de entrada e, condicionalmente aplica-se uma de duas [defina o parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) políticas consoante o valor da `isMobile` variável de contexto.

A segunda política de controlo de fluxo é na secção de saída e condicionalmente aplica-se a [converter XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) política quando `isMobile` está definido como `true`.

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Exemplo

Este exemplo mostra como realizar a filtragem de conteúdo, removendo elementos de dados da resposta recebida do serviço de back-end ao utilizar o `Starter` produto. Para uma demonstração de configurar e utilizar esta política, consulte [177 do Cloud Cover episódio: Mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço rápido para 34:30. Iniciar o 31:50 ver uma descrição geral dos [a escuro céu previsão API](https://developer.forecast.io/) utilizado para esta demonstração.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>Elementos

| Elemento   | Descrição                                                                                                                                                                                                                                                               | Necessário |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Escolha    | Elemento raiz.                                                                                                                                                                                                                                                             | Sim      |
| quando      | A condição a utilizar para o `if` ou `ifelse` partes do `choose` política. Se o `choose` política tem vários `when` seções, eles são avaliados sequencialmente. Uma vez a `condition` de um quando o elemento é avaliada como `true`, mais nenhuma `when` condições são avaliadas. | Sim      |
| otherwise | Contém o fragmento de política para ser utilizado se nenhuma da `when` condições forem avaliadas como `true`.                                                                                                                                                                               | Não       |

### <a name="attributes"></a>Atributos

| Atributo                                              | Descrição                                                                                               | Necessário |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condição = "expressão booleana &#124; booleano constante" | A expressão booleana ou constante para avaliada quando o que contém `when` declaração de política é avaliada. | Sim      |

### <a name="ChooseUsage"></a> Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="ForwardRequest"></a> Reencaminhar pedido

O `forward-request` política encaminha a solicitação de entrada para o serviço de back-end especificado no pedido [contexto](api-management-policy-expressions.md#ContextVariables). O URL do serviço de back-end é especificado na API [configurações](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) e pode ser alterada usando o [definir o serviço de back-end](api-management-transformation-policies.md) política.

> [!NOTE]
> Remover este resultados da política no pedido não sejam encaminhado para o back-end serviço e as políticas na secção de saída são avaliadas imediatamente após a conclusão com êxito das políticas na secção de entrada.

### <a name="policy-statement"></a>Declaração de política

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false" buffer-request-body="true | false" />
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

A política de nível de API seguinte reencaminha todos os pedidos de API para o serviço de back-end com um intervalo de tempo limite de 60 segundos.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Exemplo

Esta política de nível de operação utiliza o `base` elemento para herdar a política de back-end do âmbito de nível de API principal.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Exemplo

Esta política de nível de operação explicitamente encaminha todos os pedidos para o serviço de back-end com um tempo limite de 120 e não herda o política de back-end ao nível da API do pai.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120"/>
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Exemplo

Esta política de nível de operação não encaminha pedidos para o serviço de back-end.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Elementos

| Elemento         | Descrição   | Necessário |
| --------------- | ------------- | -------- |
| pedido de reencaminhamento | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo                               | Descrição                                                                                                      | Necessário | Predefinição     |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| timeout="integer"                       | O intervalo de tempo limite em segundos antes da chamada para o serviço de back-end falhará. Valor mínimo é de 0 segundos. Valores máximos é 240 segundos.| Não       | 240 segundos |
| follow-redirects="true &#124; false"    | Especifica se os redirecionamentos do serviço de back-end são seguidos pelo gateway ou retornados ao chamador.      | Não       | false       |
| buffer-request-body="true &#124; false" | Quando definido como "true" pedido é armazenado em buffer e irá ser reutilizado [repita](api-management-advanced-policies.md#Retry). | Não       | false       |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** back-end
-   **Âmbitos de política:** todos os âmbitos

## <a name="LimitConcurrency"></a> Limite de simultaneidade

O `limit-concurrency` política impede que as políticas de circunscritas em mais do que o número especificado de pedidos em execução em qualquer altura. Após a exceder esse número, novos pedidos falhará imediatamente com o código de estado do 429 demasiados pedidos.

### <a name="LimitConcurrencyStatement"></a> Declaração de política

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo seguinte demonstra como limitar o número de pedidos reencaminhados para um back-end com base no valor de uma variável de contexto.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento           | Descrição   | Necessário |
| ----------------- | ------------- | -------- |
| limite de simultaneidade | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                        | Necessário | Predefinição |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Uma cadeia de caracteres. Expressão permitido. Especifica o âmbito de simultaneidade. Podem ser partilhadas por várias políticas. | Sim      | N/A     |
| max-count | Um número inteiro. Especifica um número máximo de pedidos que estão autorizados a introduzir a política.           | Sim      | N/A     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="log-to-eventhub"></a> Registo para o Hub de eventos

O `log-to-eventhub` política envia mensagens no formato especificado para um Hub de eventos definidos por uma entidade de registo. Como o nome indica, a política é utilizada para guardar as informações selecionadas de contexto de solicitação ou resposta para análise online ou offline.

> [!NOTE]
> Para obter um guia passo a passo sobre como configurar um hub de eventos e eventos de registo, consulte [como registar eventos de gestão de API com os Hubs de eventos do Azure](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Declaração de política

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exemplo

Qualquer cadeia de caracteres pode ser utilizada como o valor que será registado nos Hubs de eventos. Neste exemplo, a data e hora, nome do serviço de implementação, id do pedido, endereço ip e nome da operação para todas as chamadas de entrada são registadas para o hub de eventos Logger registado com o `contoso-logger` id.

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento         | Descrição                                                                     | Necessário |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | Elemento raiz. O valor deste elemento é a cadeia de caracteres para iniciar sessão para o event hub. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                               | Necessário                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | O id de agente registado com o seu serviço de gestão de API.         | Sim                                                                  |
| id de partição  | Especifica o índice da partição onde as mensagens são enviadas.             | Opcional. Este atributo não pode ser utilizado se `partition-key` é utilizado. |
| partition-key | Especifica o valor utilizado para a atribuição de partições quando as mensagens são enviadas. | Opcional. Este atributo não pode ser utilizado se `partition-id` é utilizado.  |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="mock-response"></a> Resposta de simulação

O `mock-response`, como o nome implica, é usado para simular APIs e operações. Ele anulará a execução de normal pipeline e devolve uma resposta simulada para o chamador. Sempre tenta a política devolver respostas de fidelidade mais elevada. Ele prefere exemplos conteúdo da resposta, sempre que disponível. Gera as respostas do exemplo de esquemas, quando são fornecidos esquemas e exemplos não são. Se forem encontrados nem exemplos ou esquemas, as respostas com nenhum conteúdo são devolvidas.

### <a name="policy-statement"></a>Declaração de política

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Exemplos

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Elementos

| Elemento       | Descrição   | Necessário |
| ------------- | ------------- | -------- |
| resposta de simulação | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo    | Descrição                                                                                           | Necessário | Predefinição |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status-code  | Especifica o código de estado de resposta e é utilizado para selecionar exemplo correspondente ou esquema.                 | Não       | 200     |
| content-type | Especifica `Content-Type` valor do cabeçalho de resposta e é utilizado para selecionar exemplo correspondente ou esquema. | Não       | Nenhuma    |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="Retry"></a> Tente novamente

O `retry` política executa suas diretivas de filho uma vez e, em seguida, tentar sua execução até que a repetição `condition` torna-se `false` ou repita `count` está esgotado.

### <a name="policy-statement"></a>Declaração de política

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Exemplo

No exemplo a seguir, o reencaminhamento do pedido é repetido até dez vezes usando um algoritmo de repetição exponencial. Uma vez que `first-fast-retry` é definido como FALSO, todas as tentativas de repetição estão sujeitos ao algoritmo de repetição exponencial.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Elementos

| Elemento | Descrição                                                         | Necessário |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Elemento raiz. Pode conter quaisquer outras diretivas como elementos filho. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                                                                                                                                           | Necessário | Predefinição |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition        | Um literal booleano ou [expressão](api-management-policy-expressions.md) especificação se as repetições devem ser paradas (`false`) ou continuação (`true`).      | Sim      | N/A     |
| count            | Um número positivo que especifica o número máximo de tentativas para tentar.                                                                                | Sim      | N/A     |
| intervalo         | Um número positivo em segundos, especificando o intervalo de espera entre a repetição de tentativas.                                                                 | Sim      | N/A     |
| max-interval     | Um número positivo em segundos, especificando o número máximo de espera intervalo entre tentativas de repetição. Ele é usado para implementar um algoritmo de repetição exponencial. | Não       | N/A     |
| delta            | Um número positivo em segundos, especificando o incremento de intervalo de espera. É utilizado para implementar os algoritmos de repetição linear e exponencial.             | Não       | N/A     |
| primeiro-fast-repetição | Se definido como `true` , a primeira tentativa de repetição é executada imediatamente.                                                                                  | Não       | `false` |

> [!NOTE]
> Quando apenas os `interval` for especificado, **fixo** repetições de intervalo são executadas.
> Quando apenas os `interval` e `delta` forem especificados, uma **linear** algoritmo do intervalo entre tentativas é utilizado, em que o tempo de espera entre tentativas é calculado de acordo com a seguinte fórmula - `interval + (count - 1)*delta`.
> Quando o `interval`, `max-interval` e `delta` forem especificados, **exponencial** algoritmo do intervalo entre tentativas é aplicado, onde o tempo de espera entre as repetições está a crescer exponencialmente do valor da `interval` para o valor `max-interval` , de acordo com a seguinte fórmula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Tenha em atenção que as restrições de utilização de políticas de subordinados serão herdadas por esta política.

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="ReturnResponse"></a> Devolver a resposta

O `return-response` política interrompe a execução de pipeline e devolve uma predefinição ou uma resposta personalizada para o chamador. Resposta predefinida é `200 OK` com nenhum corpo. Resposta personalizada pode ser especificada por meio de instruções de política ou variável de contexto. Quando ambos são fornecidas, a resposta contida a variável de contexto é modificada pelas declarações de política antes de ser devolvido ao chamador.

### <a name="policy-statement"></a>Declaração de política

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Exemplo

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elementos

| Elemento         | Descrição                                                                               | Necessário |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| resposta de retorno | Elemento raiz.                                                                             | Sim      |
| set-header      | R [definição de cabeçalho](api-management-transformation-policies.md#SetHTTPheader) declaração de política. | Não       |
| Definir corpo        | R [set-corpo](api-management-transformation-policies.md#SetBody) declaração de política.         | Não       |
| set-status      | R [set-status](api-management-advanced-policies.md#SetStatus) declaração de política.           | Não       |

### <a name="attributes"></a>Atributos

| Atributo              | Descrição                                                                                                                                                                          | Necessário  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| response-variable-name | O nome da variável de contexto referenciado a partir de, por exemplo, a montante [pedido de envio](api-management-advanced-policies.md#SendRequest) política e que contém um `Response` objeto | Opcional. |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SendOneWayRequest"></a> Enviar pedido unidirecional

O `send-one-way-request` política envia o pedido fornecido para o URL especificado sem aguardar uma resposta.

### <a name="policy-statement"></a>Declaração de política

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Exemplo

Esta política de exemplo mostra um exemplo de como utilizar o `send-one-way-request` política para enviar uma mensagem para uma sala de bate-papo Slack se o código de resposta HTTP é maior que ou igual a 500. Para obter mais informações sobre este exemplo, consulte [utilizar serviços externos do serviço de gestão de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementos

| Elemento                    | Descrição                                                                                                 | Necessário                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| forma-pedido de envio-um       | Elemento raiz.                                                                                               | Sim                             |
| url                        | O URL do pedido.                                                                                     | Não se modo = cópia; caso contrário, Sim. |
| método                     | O método HTTP para o pedido.                                                                            | Não se modo = cópia; caso contrário, Sim. |
| cabeçalho                     | Cabeçalho do pedido. Utilize vários elementos de cabeçalho para vários cabeçalhos de pedido.                                  | Não                              |
| corpo                       | Corpo da solicitação.                                                                                           | Não                              |
| authentication-certificate | [Certificado a utilizar para autenticação de cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Determina se se trata de um novo pedido ou uma cópia da solicitação atual. No modo de saída, modo = cópia não inicializar o corpo do pedido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| nome          | Especifica o nome do cabeçalho de ser definido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/A      |
| ação existe | Especifica a ação a tomar quando o cabeçalho já está especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do cabeçalho existente.<br />-skip - não substitui o valor de cabeçalho existente.<br />-Acrescentar - acrescenta o valor para o valor de cabeçalho existente.<br />-delete - remove o cabeçalho do pedido.<br /><br /> Quando definido como `override` Transaction múltiplas entradas com o mesmo nome resulta no cabeçalho que está a ser definido de acordo com todas as entradas (que serão listadas várias vezes); apenas valores listados serão definidos no resultado. | Não       | substituir |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SendRequest"></a> Enviar pedido

O `send-request` política envia o pedido de fornecido para o URL especificado, a aguardar já não é do que o valor de tempo limite do conjunto.

### <a name="policy-statement"></a>Declaração de política

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Exemplo

Este exemplo mostra uma forma de verificar a referência de uma token com um servidor de autorização. Para obter mais informações sobre este exemplo, consulte [utilizar serviços externos do serviço de gestão de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Elementos

| Elemento                    | Descrição                                                                                                 | Necessário                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| pedido de envio               | Elemento raiz.                                                                                               | Sim                             |
| url                        | O URL do pedido.                                                                                     | Não se modo = cópia; caso contrário, Sim. |
| método                     | O método HTTP para o pedido.                                                                            | Não se modo = cópia; caso contrário, Sim. |
| cabeçalho                     | Cabeçalho do pedido. Utilize vários elementos de cabeçalho para vários cabeçalhos de pedido.                                  | Não                              |
| corpo                       | Corpo da solicitação.                                                                                           | Não                              |
| authentication-certificate | [Certificado a utilizar para autenticação de cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo                       | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Determina se se trata de um novo pedido ou uma cópia da solicitação atual. No modo de saída, modo = cópia não inicializar o corpo do pedido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| response-variable-name="string" | O nome da variável de contexto que irá receber um objeto de resposta. Se a variável não existir, será criada após a execução bem-sucedida da política e irá tornar-se acessível via [ `context.Variable` ](api-management-policy-expressions.md#ContextVariables) coleção.                                                                                                                                                                                                                                                                                                                          | Sim      | N/A      |
| timeout="integer"               | O intervalo de tempo limite em segundos antes da chamada para o URL de falha.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Não       | 60       |
| ignore-error                    | Se true e os resultados de pedido num erro:<br /><br /> – Se o nome da variável de resposta foi especificado esta irá conter um valor nulo.<br />– Se não foi especificado o nome da variável de resposta, contexto. Não será possível atualizar o pedido.                                                                                                                                                                                                                                                                                                                                                                                   | Não       | false    |
| nome                            | Especifica o nome do cabeçalho de ser definido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/A      |
| ação existe                   | Especifica a ação a tomar quando o cabeçalho já está especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do cabeçalho existente.<br />-skip - não substitui o valor de cabeçalho existente.<br />-Acrescentar - acrescenta o valor para o valor de cabeçalho existente.<br />-delete - remove o cabeçalho do pedido.<br /><br /> Quando definido como `override` Transaction múltiplas entradas com o mesmo nome resulta no cabeçalho que está a ser definido de acordo com todas as entradas (que serão listadas várias vezes); apenas valores listados serão definidos no resultado. | Não       | substituir |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SetHttpProxy"></a> Proxy HTTP de conjunto

O `proxy` política permite-lhe para encaminhar os pedidos reencaminhados para o back-ends através de um proxy HTTP. Apenas HTTP (não HTTPS) é suportada entre o gateway e o proxy. Básico e apenas autenticação NTLM.

### <a name="policy-statement"></a>Declaração de política

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Exemplo

Observe o uso de [propriedades](api-management-howto-properties.md) como valores de nome de utilizador e palavra-passe para evitar armazenar informações confidenciais no documento da política.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementos

| Elemento | Descrição  | Necessário |
| ------- | ------------ | -------- |
| Proxy   | Elemento de raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo         | Descrição                                            | Necessário | Predefinição |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | URL de proxy na forma de http://host:port.             | Sim      | N/A     |
| username="string" | Nome de utilizador a ser utilizado para autenticação com o proxy. | Não       | N/A     |
| password="string" | Palavra-passe a ser utilizado para autenticação com o proxy. | Não       | N/A     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada

-   **Âmbitos de política:** todos os âmbitos

## <a name="SetRequestMethod"></a> Método de pedido de conjunto

O `set-method` política permite-lhe alterar o método de pedido HTTP para um pedido.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exemplo

Esta política de exemplo que utiliza o `set-method` política mostra um exemplo de enviar uma mensagem para uma sala de bate-papo Slack se o código de resposta HTTP é maior que ou igual a 500. Para obter mais informações sobre este exemplo, consulte [utilizar serviços externos do serviço de gestão de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementos

| Elemento    | Descrição                                                       | Necessário |
| ---------- | ----------------------------------------------------------------- | -------- |
| método Set | Elemento raiz. O valor do elemento Especifica o método HTTP. | Sim      |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SetStatus"></a> Código de estado do conjunto

O `set-status` política define o código de estado HTTP para o valor especificado.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Exemplo

Este exemplo mostra como devolver uma resposta 401, se o token de autorização é inválido. Para obter mais informações, consulte [utilizar serviços externos do serviço de gestão de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Elementos

| Elemento    | Descrição   | Necessário |
| ---------- | ------------- | -------- |
| set-status | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo       | Descrição                                                | Necessário | Predefinição |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | O código de estado HTTP a devolver.                            | Sim      | N/A     |
| reason="string" | Uma descrição do motivo para devolver o código de estado. | Sim      | N/A     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** saída, back-end, no caso de erro
-   **Âmbitos de política:** todos os âmbitos

## <a name="set-variable"></a> Definir variável

O `set-variable` política declara um [contexto](api-management-policy-expressions.md#ContextVariables) variável e o atribui um valor especificado por meio de um [expressão](api-management-policy-expressions.md) ou um literal de cadeia. Se a expressão contém um literal, ela seria convertida numa cadeia de caracteres e o tipo do valor será `System.String`.

### <a name="set-variablePolicyStatement"></a> Declaração de política

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a> Exemplo

O exemplo seguinte demonstra uma política de variável de conjunto na secção de entrada. Esta política de variável de conjunto cria um `isMobile` booleano [contexto](api-management-policy-expressions.md#ContextVariables) variável que está definido como VERDADEIRO se o `User-Agent` cabeçalho contém o texto de pedido `iPad` ou `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementos

| Elemento      | Descrição   | Necessário |
| ------------ | ------------- | -------- |
| set-variable | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                              | Necessário |
| --------- | ------------------------------------------------------------------------ | -------- |
| nome      | O nome da variável.                                                | Sim      |
| valor     | O valor da variável. Isso pode ser uma expressão ou um valor literal. | Sim      |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro
-   **Âmbitos de política:** todos os âmbitos

### <a name="set-variableAllowedTypes"></a> Tipos permitidos

As expressões usadas no `set-variable` política tem de devolver um dos seguintes tipos básicos.

-   System.Boolean
-   System.SByte
-   System.Byte
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   System.Decimal
-   System.Single
-   System.Double
-   System.Guid
-   System.String
-   System.Char
-   System.DateTime
-   System.TimeSpan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System.Single?
-   System.Double?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

## <a name="Trace"></a> Trace

O `trace` política adiciona uma cadeia de caracteres para o [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) saída. A política será executado apenas quando o rastreamento é acionado, ou seja, `Ocp-Apim-Trace` cabeçalho do pedido está presente e definida para `true` e `Ocp-Apim-Subscription-Key` cabeçalho do pedido está presente e contém uma chave válida associada com a conta de administrador.

### <a name="policy-statement"></a>Declaração de política

```xml

<trace source="arbitrary string literal">
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elementos

| Elemento | Descrição   | Necessário |
| ------- | ------------- | -------- |
| Rastreio   | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                             | Necessário | Predefinição |
| --------- | --------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Literal de cadeia de caracteres é significativo para o Visualizador de rastreio e especificar a origem da mensagem. | Sim      | N/A     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="Wait"></a> Wait

O `wait` política executa suas diretivas de filho imediatos em paralelo e aguarda todos ou uma das suas políticas de filho imediatos para concluir antes de concluir. A política de espera pode ter como as políticas de filho imediatos [pedido de envio](api-management-advanced-policies.md#SendRequest), [obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey), e [controlar o fluxo](api-management-advanced-policies.md#choose) políticas.

### <a name="policy-statement"></a>Declaração de política

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exemplo

No exemplo a seguir há dois `choose` políticas como políticas de filho imediatos do `wait` política. Cada um destes `choose` políticas é executado em paralelo. Cada `choose` política tenta recuperar um valor em cache. Se houver uma falha de acerto na cache, um serviço de back-end é chamado para fornecer o valor. Neste exemplo o `wait` política não é concluída até que todas as suas políticas de filho imediatos concluir, uma vez que o `for` atributo está definido como `all`. Neste exemplo as variáveis de contexto (`execute-branch-one`, `value-one`, `execute-branch-two`, e `value-two`) são declarados fora do escopo desta política de exemplo.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Elementos

| Elemento | Descrição                                                                                                   | Necessário |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| Aguarde    | Elemento raiz. Pode conter como elementos filho apenas `send-request`, `cache-lookup-value`, e `choose` políticas. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                                                                                                                                                                                                                                                                                                                                            | Necessário | Predefinição |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| durante       | Determina se o `wait` política aguarda para todas as políticas de filho imediatos como concluído ou apenas um. Valores permitidos são:<br /><br /> - `all` -aguardar que todas as políticas de filho imediatos concluir<br />-qualquer - a aguardar por qualquer política filho imediatos para concluir. Depois de concluída a primeira diretiva filho imediatos, o `wait` política for concluída e execução de outras políticas do filho imediatos é terminada. | Não       | all     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end
-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações para trabalhar com políticas, consulte:

-   [Políticas de gestão de API](api-management-howto-policies.md)
-   [Expressões de política](api-management-policy-expressions.md)
-   [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
-   [Exemplos de política](policy-samples.md)

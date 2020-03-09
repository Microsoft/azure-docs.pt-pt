---
title: Políticas avançadas da Azure API Management Microsoft Docs
description: Conheça as políticas avançadas disponíveis para utilização na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: c8ef481fe277d6451923da828f0e7473354c24cf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374266"
---
# <a name="api-management-advanced-policies"></a>API Gestão políticas avançadas

Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a>Políticas avançadas

-   [Fluxo de controlo](api-management-advanced-policies.md#choose) - Aplica condicionalmente declarações políticas baseadas nos resultados da avaliação das [expressões](api-management-policy-expressions.md)booleanas .
-   [Pedido para a frente](#ForwardRequest) - Reencaminha o pedido para o serviço backend.
-   [Limite a conmoeda](#LimitConcurrency) - Impede que as políticas fechadas executem por mais do que o número especificado de pedidos de cada vez.
-   [Iniciar sessão no Event Hub](#log-to-eventhub) - Envia mensagens no formato especificado para um Hub de Eventos definido por uma entidade Logger.
-   [Resposta falsa](#mock-response) - Aborta a execução do gasoduto e devolve uma resposta ridicularizada diretamente ao chamador.
-   [Retry](#Retry) - Retry execução das declarações políticas fechadas, se e até que a condição seja cumprida. A execução repetir-se-á nos intervalos de tempo especificados e até à contagem de repetição especificada.
-   [Resposta de devolução](#ReturnResponse) - Aborta a execução do gasoduto e devolve a resposta especificada diretamente ao chamador.
-   [Enviar um pedido](#SendOneWayRequest) de ida - Envia um pedido para o URL especificado sem esperar por uma resposta.
-   [Enviar pedido](#SendRequest) - Envia um pedido para o URL especificado.
-   [Definir proxy HTTP](#SetHttpProxy) - Permite-lhe encaminhar pedidos encaminhadas através de um proxy HTTP.
-   [Definir método](#SetRequestMethod) de pedido - Permite-lhe alterar o método HTTP para um pedido.
-   [Definir código](#SetStatus) de estado - Altera o código de estado HTTP para o valor especificado.
-   [Variável definida](api-management-advanced-policies.md#set-variable) - Persiste um valor numa variável de [contexto](api-management-policy-expressions.md#ContextVariables) nomeado para acesso posterior.
-   [Trace](#Trace) - Adiciona vestígios personalizados na saída do [Inspetor API,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) telemetria de Insights de Aplicação e Registos de Diagnóstico.
-   [Aguardar](#Wait) - Aguarda por pedido de [envio](api-management-advanced-policies.md#SendRequest)em anexo , [Obter valor da cache](api-management-caching-policies.md#GetFromCacheByKey), ou controlar as políticas de [fluxo](api-management-advanced-policies.md#choose) para completar antes de prosseguir.

## <a name="choose"></a>Fluxo de controlo

A política `choose` aplica declarações políticas em anexo baseadas no resultado da avaliação das expressões booleanas, semelhantes a uma construção de se-então ou de uma mudança numa linguagem de programação.

### <a name="ChoosePolicyStatement"></a>Declaração política

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

A política de fluxo de controlo deve conter pelo menos um elemento `<when/>`. O elemento `<otherwise/>` é opcional. As condições em `<when/>` elementos são avaliadas por ordem da sua aparição dentro da política. As declarações políticas em anexo no primeiro elemento `<when/>` com atributo de condição igual a `true` serão aplicadas. As políticas enquadradas no elemento `<otherwise/>`, se forem presentes, serão aplicadas se todos os atributos da condição do elemento `<when/>` forem `false`.

### <a name="examples"></a>Exemplos

#### <a name="ChooseExample"></a>Exemplo

O exemplo que se segue demonstra uma política [de definição variável](api-management-advanced-policies.md#set-variable) e duas políticas de fluxo de controlo.

A política variável definida está na secção de entrada e cria uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) `isMobile` Boolean que é definida como verdadeira se o cabeçalho de pedido de `User-Agent` contiver o texto `iPad` ou `iPhone`.

A primeira política de fluxo de controlo também está na secção de entrada, e aplica condicionalmente uma de duas políticas de [parâmetros](api-management-transformation-policies.md#SetQueryStringParameter) de corda de consulta definida, dependendo do valor da variável de contexto `isMobile`.

A segunda política de fluxo de controlo está na secção de saída e aplica condicionalmente a política [ConvertE XML à JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) quando `isMobile` está definida para `true`.

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

Este exemplo mostra como executar a filtragem de conteúdo removendo elementos de dados da resposta recebida do serviço backend ao utilizar o produto `Starter`. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover Episódio 177: Mais Funcionalidades de Gestão aPI com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para as 34:30. Comece às 31:50 para ver uma visão geral da [API](https://developer.forecast.io/) de Previsão do Céu Escuro usada para esta demonstração.

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
| escolher    | Elemento de raiz.                                                                                                                                                                                                                                                             | Sim      |
| quando      | A condição de utilização para as partes `if` ou `ifelse` da política `choose`. Se a política `choose` tiver várias secções `when`, são avaliadas sequencialmente. Uma vez que o `condition` de um elemento avalia para `true`, não são avaliadas mais condições `when`. | Sim      |
| caso contrário | Contém o corte de política a utilizar se nenhuma das condições de `when` avaliar para `true`.                                                                                                                                                                               | Não       |

### <a name="attributes"></a>Atributos

| Atributo                                              | Descrição                                                                                               | Necessário |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condição="Boolean &#124; expressão Boolean constant" | A expressão booleana ou constante a avaliar quando a declaração política de `when` contendo é avaliada. | Sim      |

### <a name="ChooseUsage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="ForwardRequest"></a>Pedido para a frente

A política `forward-request` remepara o pedido de entrada para o serviço de backend especificado no [contexto](api-management-policy-expressions.md#ContextVariables)do pedido. O URL do serviço backend é especificado nas [definições](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) da API e pode ser alterado usando a política de serviço de [backend definida.](api-management-transformation-policies.md)

> [!NOTE]
> A eliminação desta política resulta no facto de o pedido não ser encaminhado para o serviço de backend e as políticas da secção de saída são avaliadas imediatamente após a conclusão bem sucedida das políticas na secção de entrada.

### <a name="policy-statement"></a>Declaração política

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

A seguinte política de nível DeAPi encaminha todos os pedidos da API para o serviço backend com um intervalo de tempo de 60 segundos.

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

Esta política de nível de operação utiliza o elemento `base` para herdar a política de backend do âmbito de nível de API-parental.

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

Esta política de nível de operação encaminha explicitamente todos os pedidos para o serviço de backend com um prazo de 120 e não herda a política de backend nível de api do nível de parentalidade. Se o serviço backend responder com um código de estado de erro de 400 a 599 incluído, a secção [de erro](api-management-error-handling-policies.md) será ativada.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Exemplo

Esta política de nível de operação não reencaminha os pedidos para o serviço de backend.

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
| pedido para a frente | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo                                     | Descrição                                                                                                                                                                                                                                                                                                    | Necessário | Predefinição |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="integer"                             | O tempo em segundos para esperar que os cabeçalhos de resposta HTTP sejam devolvidos pelo serviço de backend antes de um erro de tempo ser levantado. O valor mínimo é de 0 segundos. Valores superiores a 240 segundos podem não ser honrados, uma vez que a infraestrutura de rede subjacente pode deixar cair ligações inativas após este tempo. | Não       | Nenhum    |
| follow-redirects="false &#124; true"          | Especifica se os redirecionamentos do serviço de backend são seguidos pelo gateway ou devolvidos ao chamador.                                                                                                                                                                                                    | Não       | false   |
| tampão-request-body="falso &#124; verdadeiro"       | Quando definido para o pedido "verdadeiro" é tamponado e será reutilizado em [retry](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Não       | false   |
| falha-no-erro-status-code="falso &#124; verdadeiro" | Quando definido para verdadeiramente acionar a secção [de erros para](api-management-error-handling-policies.md) códigos de resposta na gama de 400 a 599 inclusive.                                                                                                                                                                      | Não       | false   |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** backend
-   **Âmbitos de política:** todos os âmbitos

## <a name="LimitConcurrency"></a>Limite conmoeda

A política `limit-concurrency` impede que as políticas em anexo sejam executadas por mais do que o número especificado de pedidos a qualquer momento. Ao exceder esse número, novos pedidos falharão imediatamente com o código de estado de 429 Pedidos de Muitos Pedidos.

### <a name="LimitConcurrencyStatement"></a>Declaração política

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo que se segue demonstra como limitar o número de pedidos reencaminhados para um backend com base no valor de uma variável de contexto.

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
| limite-conmoeda | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                        | Necessário | Predefinição |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Uma corda. Expressão permitida. Especifica o âmbito da moeda. Pode ser partilhado por múltiplas políticas. | Sim      | N/D     |
| contagem máxima | Um inteiro. Especifica um número máximo de pedidos que podem entrar na apólice.           | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="log-to-eventhub"></a>Iniciar sessão no Centro de Eventos

A política `log-to-eventhub` envia mensagens no formato especificado para um Hub de Eventodefinido por uma entidade Logger. Como o seu nome indica, a política é usada para guardar informações de contexto de pedido ou resposta selecionadas para análise online ou offline.

> [!NOTE]
> Para um guia passo a passo sobre a configuração de um centro de eventos e eventos de registo, consulte como registar eventos de [Gestão API com Hubs de Eventos Azure](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Declaração política

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exemplo

Qualquer corda pode ser usada como o valor a ser registado em Centros de Eventos. Neste exemplo, a data e hora, o nome do serviço de implementação, o id de pedido, o endereço IP e o nome de operação para todas as chamadas de entrada são registados no centro de eventos Logger registado com o id `contoso-logger`

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
| log-to-eventhub | Elemento de raiz. O valor deste elemento é a corda para entrar no seu centro de eventos. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                               | Necessário                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | A identificação do Logger registada no seu serviço de Gestão API.         | Sim                                                                  |
| partição-id  | Especifica o índice da partição onde as mensagens são enviadas.             | Opcional. Este atributo não pode ser utilizado se for utilizado `partition-key`. |
| chave de partição | Especifica o valor utilizado para a atribuição de divisórias quando as mensagens são enviadas. | Opcional. Este atributo não pode ser utilizado se for utilizado `partition-id`.  |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="mock-response"></a>Resposta falsa

O `mock-response`, como o nome indica, é usado para ridicularizar APIs e operações. Aborta a execução normal do gasoduto e devolve uma resposta ridícula ao ouvinte. A política tenta sempre devolver respostas de maior fidelidade. Prefere exemplos de conteúdo de resposta, sempre que disponível. Gera respostas de amostra schemas, quando são fornecidos esquemmas e exemplos não. Se não forem encontrados exemplos ou esquemas, as respostas sem conteúdo são devolvidas.

### <a name="policy-statement"></a>Declaração política

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
| mock-resposta | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo    | Descrição                                                                                           | Necessário | Predefinição |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| código de estado  | Especifica o código de estado da resposta e é utilizado para selecionar o exemplo ou o esquema correspondente.                 | Não       | 200     |
| tipo de conteúdo | Especifica `Content-Type` valor do cabeçalho de resposta e é utilizado para selecionar o exemplo ou o esquema correspondente. | Não       | Nenhum    |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="Retry"></a>Retry

A política `retry` executa as suas políticas infantis uma vez e, em seguida, retenta a sua execução até que o `condition` de novo se torne `false` ou se esgote `count`.

### <a name="policy-statement"></a>Declaração política

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

No exemplo seguinte, o reencaminhamento de pedidos é novamente experimentado até dez vezes usando um algoritmo de retry exponencial. Uma vez que `first-fast-retry` está definido para falso, todas as tentativas de retenção estão sujeitas ao algoritmo de retry exponencial.

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
| retry   | Elemento de raiz. Pode conter quaisquer outras políticas como elementos infantis. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                                                                                                                                           | Necessário | Predefinição |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition        | Um literal ou [expressão](api-management-policy-expressions.md) booleano especificando se as tentativas devem ser interrompidas (`false`) ou continuadas (`true`).      | Sim      | N/D     |
| count            | Um número positivo que especifica o número máximo de tentativas de tentativa.                                                                                | Sim      | N/D     |
| intervalo         | Um número positivo em segundos especificando o intervalo de espera entre as tentativas de repetição.                                                                 | Sim      | N/D     |
| intervalo máximo     | Um número positivo em segundos especificando o intervalo máximo de espera entre as tentativas de repetição. É usado para implementar um algoritmo de retry exponencial. | Não       | N/D     |
| delta            | Um número positivo em segundos especificando o incremento do intervalo de espera. É usado para implementar os algoritmos lineares e exponenciais de retry.             | Não       | N/D     |
| primeira-rápida-retry | Se for programado para `true`, a primeira tentativa de retry é realizada imediatamente.                                                                                  | Não       | `false` |

> [!NOTE]
> Quando apenas o `interval` é especificado, são efetuadas repetições de **intervalos fixos.**
> Quando apenas os `interval` e `delta` são especificados, **é** utilizado um algoritmo linear de repetição de intervalos, onde o tempo de espera entre as tentativas é calculado de acordo com a seguinte fórmula - `interval + (count - 1)*delta`.
> Quando o `interval`, `max-interval` e `delta` são especificados, o algoritmo de repetição de intervalos **exponenciais** é aplicado, onde o tempo de espera entre as repetições está a crescer exponencialmente do valor da `interval` ao valor `max-interval` de acordo com a fórmula seguinte - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de política. Note-se que as restrições à utilização da política infantil serão herdadas por esta política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="ReturnResponse"></a>Resposta de devolução

A política `return-response` aborta a execução do gasoduto e devolve uma resposta padrão ou personalizada ao chamador. A resposta padrão é `200 OK` sem corpo. A resposta personalizada pode ser especificada através de uma variável de contexto ou declarações políticas. Quando ambos são fornecidos, a resposta contida na variável de contexto é modificada pelas declarações políticas antes de ser devolvida ao chamador.

### <a name="policy-statement"></a>Declaração política

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
| resposta de retorno | Elemento de raiz.                                                                             | Sim      |
| set-header      | Uma declaração política [de cabeçalho.](api-management-transformation-policies.md#SetHTTPheader) | Não       |
| set-body        | Uma declaração [política de corpo de conjunto.](api-management-transformation-policies.md#SetBody)         | Não       |
| set-status      | Uma declaração de política [de estado de fixação.](api-management-advanced-policies.md#SetStatus)           | Não       |

### <a name="attributes"></a>Atributos

| Atributo              | Descrição                                                                                                                                                                          | Necessário  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| response-variable-name | O nome da variável de contexto referenciada, por exemplo, de uma política [de pedido de envio](api-management-advanced-policies.md#SendRequest) a montante e contendo um objeto `Response` | Opcional. |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SendOneWayRequest"></a>Enviar um pedido de ida

A política `send-one-way-request` envia o pedido fornecido para o URL especificado sem esperar por uma resposta.

### <a name="policy-statement"></a>Declaração política

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

Esta política de amostras mostra um exemplo de utilização da política de `send-one-way-request` para enviar uma mensagem para uma sala de chat Slack se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre esta amostra, consulte [A utilização de serviços externos do serviço de Gestão API Azure.](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
| enviar-um-pedido de caminho       | Elemento de raiz.                                                                                               | Sim                             |
| url                        | A URL do pedido.                                                                                     | Não se mode=cópia; caso contrário, sim. |
| método                     | O método HTTP para o pedido.                                                                            | Não se mode=cópia; caso contrário, sim. |
| cabeçalho                     | Peça cabeçalho. Utilize vários elementos de cabeçalho para vários cabeçalhos de pedido.                                  | Não                              |
| corpo                       | O corpo de pedido.                                                                                           | Não                              |
| autenticação-certificado | [Certificado a utilizar para autenticação do cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Determina se se trata de um novo pedido ou de uma cópia do pedido atual. No modo de saída, o modo=cópia não inicia o corpo de pedido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| nome          | Especifica o nome do cabeçalho a definir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/D      |
| existe-ação | Especifica que medidas tomar quando o cabeçalho já estiver especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - substituição - substitui o valor do cabeçalho existente.<br />- skip - não substitui o valor do cabeçalho existente.<br />- apêndice - anexa o valor ao valor do cabeçalho existente.<br />- apagar - retira o cabeçalho do pedido.<br /><br /> Quando definido para `override` alistando várias entradas com o mesmo nome resulta no conjunto do cabeçalho de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado. | Não       | substituição |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SendRequest"></a>Enviar pedido

A política `send-request` envia o pedido fornecido para o URL especificado, esperando não mais do que o valor estabelecido.

### <a name="policy-statement"></a>Declaração política

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

Este exemplo mostra uma maneira de verificar um símbolo de referência com um servidor de autorização. Para obter mais informações sobre esta amostra, consulte [A utilização de serviços externos do serviço de Gestão API Azure.](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
| envio-pedido               | Elemento de raiz.                                                                                               | Sim                             |
| url                        | A URL do pedido.                                                                                     | Não se mode=cópia; caso contrário, sim. |
| método                     | O método HTTP para o pedido.                                                                            | Não se mode=cópia; caso contrário, sim. |
| cabeçalho                     | Peça cabeçalho. Utilize vários elementos de cabeçalho para vários cabeçalhos de pedido.                                  | Não                              |
| corpo                       | O corpo de pedido.                                                                                           | Não                              |
| autenticação-certificado | [Certificado a utilizar para autenticação do cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo                       | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Determina se se trata de um novo pedido ou de uma cópia do pedido atual. No modo de saída, o modo=cópia não inicia o corpo de pedido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| response-variable-name="string" | O nome da variável de contexto que receberá um objeto de resposta. Se a variável não existir, será criada após a execução bem sucedida da apólice e tornar-se-á acessível através [`context.Variable`](api-management-policy-expressions.md#ContextVariables) coleção.                                                                                                                                                                                                                                                                                                                          | Sim      | N/D      |
| timeout="integer"               | O intervalo de tempo de tempo em segundos antes da chamada para o URL falhar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Não       | 60       |
| ignore-error                    | Se for verdade e o pedido resultar num erro:<br /><br /> - Se o nome variável de resposta tiver sido especificado, conterá um valor nulo.<br />- Se não foi especificado o nome variável de resposta, contexto. O pedido não será atualizado.                                                                                                                                                                                                                                                                                                                                                                                   | Não       | false    |
| nome                            | Especifica o nome do cabeçalho a definir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/D      |
| existe-ação                   | Especifica que medidas tomar quando o cabeçalho já estiver especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - substituição - substitui o valor do cabeçalho existente.<br />- skip - não substitui o valor do cabeçalho existente.<br />- apêndice - anexa o valor ao valor do cabeçalho existente.<br />- apagar - retira o cabeçalho do pedido.<br /><br /> Quando definido para `override` alistando várias entradas com o mesmo nome resulta no conjunto do cabeçalho de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado. | Não       | substituição |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SetHttpProxy"></a>Definir proxy HTTP

A política `proxy` permite-lhe encaminhar pedidos encaminhados para backends através de um proxy HTTP. Apenas http (não HTTPS) é suportado entre o gateway e o proxy. Autenticação básica e NTLM apenas.

### <a name="policy-statement"></a>Declaração política

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Exemplo

Note a utilização de [propriedades](api-management-howto-properties.md) como valores do nome de utilizador e palavra-passe para evitar armazenar informações sensíveis no documento de política.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementos

| Elemento | Descrição  | Necessário |
| ------- | ------------ | -------- |
| Proxy   | Elemento raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo         | Descrição                                            | Necessário | Predefinição |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | URL proxy na forma de http://host:port.             | Sim      | N/D     |
| username="string" | Nome de utilizador a utilizar para autenticação com o representante. | Não       | N/D     |
| password="string" | Palavra-passe a utilizar para autenticação com o representante. | Não       | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

## <a name="SetRequestMethod"></a>Definir método de pedido

A política `set-method` permite alterar o método de pedido http para um pedido.

### <a name="policy-statement"></a>Declaração política

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exemplo

Esta política de amostras que utiliza a política `set-method` mostra um exemplo de envio de uma mensagem para uma sala de chat Slack se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre esta amostra, consulte [A utilização de serviços externos do serviço de Gestão API Azure.](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
| método de conjunto | Elemento de raiz. O valor do elemento especifica o método HTTP. | Sim      |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="SetStatus"></a>Definir código de estado

A política `set-status` define o código de estado HTTP para o valor especificado.

### <a name="policy-statement"></a>Declaração política

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Exemplo

Este exemplo mostra como devolver uma resposta 401 se o sinal de autorização for inválido. Para mais informações, consulte [Utilização de serviços externos do serviço de Gestão API Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
| set-status | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo       | Descrição                                                | Necessário | Predefinição |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | O código de estado HTTP para devolver.                            | Sim      | N/D     |
| reason="string" | Uma descrição da razão para devolver o código de estado. | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** saída, backend, erro
-   **Âmbitos de política:** todos os âmbitos

## <a name="set-variable"></a>Definir variável

A política `set-variable` declara uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) e atribui-lhe um valor especificado através de uma [expressão](api-management-policy-expressions.md) ou de uma corda literal. se a expressão contiver um literal, será convertido numa corda e o tipo do valor será `System.String`.

### <a name="set-variablePolicyStatement"></a>Declaração política

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a>Exemplo

O exemplo que se segue demonstra uma política variável definida na secção de entrada. Esta política variável definida cria uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) `isMobile` Boolean que é definida como verdadeira se o cabeçalho de pedido de `User-Agent` contiver o texto `iPad` ou `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementos

| Elemento      | Descrição   | Necessário |
| ------------ | ------------- | -------- |
| set-variable | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                              | Necessário |
| --------- | ------------------------------------------------------------------------ | -------- |
| nome      | O nome da variável.                                                | Sim      |
| valor     | O valor da variável. Isto pode ser uma expressão ou um valor literal. | Sim      |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro
-   **Âmbitos de política:** todos os âmbitos

### <a name="set-variableAllowedTypes"></a>Tipos permitidos

As expressões utilizadas na política `set-variable` devem devolver um dos seguintes tipos básicos.

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
-   Sistema.Duplo?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

## <a name="Trace"></a>Vestígios

A política `trace` adiciona um traço personalizado na saída do Inspetor API, telemetria de Insights de Aplicação e/ou Registos de Diagnóstico.

-   A apólice adiciona um traço personalizado à saída do [Inspetor API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) quando o rastreio é acionado, ou seja, `Ocp-Apim-Trace` cabeçalho de pedido está presente e definido para o verdadeiro e `Ocp-Apim-Subscription-Key` cabeçalho de pedido está presente e detém uma chave válida que permite o rastreio.
-   A política cria uma telemetria [trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) em Insights de aplicação, quando a integração de Insights de [Aplicação](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) está ativada e o nível de `severity` especificado na política é de um nível `verbosity` especificado na definição de diagnóstico.
-   A política adiciona uma propriedade na entrada de registo quando os [Registos de Diagnóstico](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) estão ativados e o nível de gravidade especificado na política é a um nível de verbosidade especificado na definição de diagnóstico.

### <a name="policy-statement"></a>Declaração política

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="traceExample"></a>Exemplo

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elementos

| Elemento  | Descrição                                                                                                                                          | Necessário |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Vestígios    | Elemento de raiz.                                                                                                                                        | Sim      |
| message  | Uma corda ou expressão a ser registada.                                                                                                                 | Sim      |
| do IdP | Adiciona uma propriedade personalizada à telemetria De [Rastreio](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) de Insights de Aplicação. | Não       |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                                               | Necessário | Predefinição |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | String literal significativo para o rastreador espectador e especificando a origem da mensagem.                                   | Sim      | N/D     |
| gravidade  | Especifica o nível de gravidade do vestígio. Os valores permitidos são `verbose`, `information`, `error` (do mais baixo para o mais alto). | Não       | Verboso |
| nome      | Nome da propriedade.                                                                                                     | Sim      | N/D     |
| valor     | Valor da propriedade.                                                                                                    | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="Wait"></a>Espera, espera.

A política `wait` executa as suas políticas infantis imediatas em paralelo, e aguarda que todas ou uma das suas políticas imediatas para crianças completem antes de estar concluída. A política de espera pode ter como as suas políticas imediatas para crianças [Enviar por email](api-management-advanced-policies.md#SendRequest)o artigo [Cache](api-management-caching-policies.md#GetFromCacheByKey)e Controle as políticas de [fluxo.](api-management-advanced-policies.md#choose)

### <a name="policy-statement"></a>Declaração política

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exemplo

No exemplo seguinte, há duas políticas `choose` como políticas imediatas para crianças da política `wait`. Cada uma destas políticas `choose` executa em paralelo. Cada `choose` política tenta obter um valor em cache. Se houver uma falha de cache, um serviço de backend é chamado para fornecer o valor. Neste exemplo, a política `wait` só termina quando todas as suas políticas imediatas para crianças estiverem concluídas, uma vez que o atributo `for` está previsto para `all`. Neste exemplo, as variáveis de contexto (`execute-branch-one`, `value-one`, `execute-branch-two`, e `value-two`- são declaradas fora do âmbito desta política de exemplo.

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
| esperar    | Elemento de raiz. Pode conter como elementos infantis apenas `send-request`, `cache-lookup-value`e políticas `choose`. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                                                                                                                                                                                                                                                                                                                                            | Necessário | Predefinição |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| para       | Determina se a política de `wait` espera que todas as políticas imediatas para crianças sejam concluídas ou apenas uma. Valores permitidos são:<br /><br /> - `all` - espere que todas as políticas imediatas para crianças completem<br />- qualquer - espere que qualquer política imediata de crianças esteja concluída. Uma vez concluída a primeira política imediata para crianças, a política `wait` termina e a execução de quaisquer outras políticas imediatas para crianças é encerrada. | Não       | all     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend
-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para mais informações que trabalhem com políticas, consulte:

-   [Políticas em Gestão aPi](api-management-howto-policies.md)
-   [Expressões de política](api-management-policy-expressions.md)
-   [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
-   [Amostras políticas](policy-samples.md)

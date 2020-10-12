---
title: Azure API Management avançou políticas Microsoft Docs
description: Conheça as políticas avançadas disponíveis para utilização na Azure API Management. Veja exemplos e veja recursos adicionais disponíveis.
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
ms.openlocfilehash: 6ac3457a22128f313084ab070a5a61c2d26d4b85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87851686"
---
# <a name="api-management-advanced-policies"></a>Políticas avançadas de Gestão de API

Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](https://go.microsoft.com/fwlink/?LinkID=398186)

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a> Políticas avançadas

-   [Fluxo de](api-management-advanced-policies.md#choose) controlo - Aplica, condicionalmente, declarações políticas baseadas nos resultados da avaliação das [expressões booleanas](api-management-policy-expressions.md).
-   [Pedido de encaminhar](#ForwardRequest) - Remete o pedido para o serviço de backend.
-   [Limita a concordância](#LimitConcurrency) - Impede que as políticas em anexo executem mais do que o número especificado de pedidos de cada vez.
-   [Log to Event Hub](#log-to-eventhub) - Envia mensagens no formato especificado para um Centro de Eventos definido por uma entidade Logger.
-   [Resposta falsa](#mock-response) - Aborta a execução do gasoduto e devolve uma resposta ridícula diretamente ao autor da chamada.
-   [Retry](#Retry) - Recauchutagem execução das declarações políticas em anexo, se e até que a condição seja satisfeita. A execução repetir-se-á nos intervalos de tempo especificados e até à contagem de repetição especificada.
-   [Resposta de retorno](#ReturnResponse) - Aborta a execução do gasoduto e devolve a resposta especificada diretamente ao autor da chamada.
-   [Enviar um pedido de ida](#SendOneWayRequest) - Envia um pedido para o URL especificado sem esperar por uma resposta.
-   [Envio de pedido](#SendRequest) - Envia um pedido para a URL especificada.
-   [Definir http proxy](#SetHttpProxy) - Permite-lhe encaminhar os pedidos reencaminhados através de um representante HTTP.
-   [Definir método de pedido](#SetRequestMethod) - Permite-lhe alterar o método HTTP para um pedido.
-   [Definir código de estado](#SetStatus) - Altera o código de estado HTTP para o valor especificado.
-   [Variação variável](api-management-advanced-policies.md#set-variable) - Persiste um valor numa variável de [contexto](api-management-policy-expressions.md#ContextVariables) nomeado para acesso posterior.
-   [Trace](#Trace) - Adiciona vestígios personalizados na saída do [Inspetor API,](./api-management-howto-api-inspector.md) telemetria de Insights de Aplicação e Registos de Recursos.
-   [Aguarde](#Wait) - Espera por pedido de [envio](api-management-advanced-policies.md#SendRequest)anexo, [Obtenha valor a partir de cache,](api-management-caching-policies.md#GetFromCacheByKey)ou políticas de [fluxo de controlo](api-management-advanced-policies.md#choose) para concluir antes de prosseguir.

## <a name="control-flow"></a><a name="choose"></a> Fluxo de controlo

A `choose` política aplica declarações políticas fechadas com base no resultado da avaliação das expressões booleanas, semelhantes a um caso de então ou a uma construção de comutação numa linguagem de programação.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a> Declaração política

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

A política de fluxo de controlo deve conter pelo menos um `<when/>` elemento. O `<otherwise/>` elemento é opcional. As condições dos `<when/>` elementos são avaliadas por ordem da sua aparência dentro da política. Serão aplicadas declarações de política incluídas no primeiro `<when/>` elemento com atributos de condição `true` iguais. As políticas incluídas no `<otherwise/>` elemento, se presentes, serão aplicadas se todos os atributos da condição do `<when/>` elemento forem `false` .

### <a name="examples"></a>Exemplos

#### <a name="example"></a><a name="ChooseExample"></a> Exemplo

O exemplo a seguir demonstra uma política [de variável definida](api-management-advanced-policies.md#set-variable) e duas políticas de fluxo de controlo.

A política variável definida está na secção de entrada e cria uma `isMobile` variável de [contexto](api-management-policy-expressions.md#ContextVariables) Boolean que é definida como verdadeira se o cabeçalho de `User-Agent` pedido contiver o texto `iPad` ou `iPhone` .

A primeira política de fluxo de controlo também está na secção de entrada, e aplica condicionalmente uma de duas políticas de parâmetros de linha de [consulta de conjunto,](api-management-transformation-policies.md#SetQueryStringParameter) dependendo do valor da variável de `isMobile` contexto.

A segunda política de fluxo de controlo encontra-se na secção de saída e aplica condicionalmente a política [de Conversão XML a JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) quando `isMobile` está definida para `true` .

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

Este exemplo mostra como executar a filtragem de conteúdos removendo elementos de dados da resposta recebida do serviço de backend ao utilizar o `Starter` produto. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward to 34:30. Comece às 31:50 para ver uma visão geral da [API](https://developer.forecast.io/) de Previsão do Céu Escuro usada para esta demonstração.

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

| Elemento   | Descrição                                                                                                                                                                                                                                                               | Obrigatório |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| escolher    | Elemento de raiz.                                                                                                                                                                                                                                                             | Sim      |
| quando      | A condição a usar para a `if` ou `ifelse` partes da `choose` apólice. Se a `choose` apólice tiver `when` várias secções, são avaliadas sequencialmente. Uma vez que o `condition` elemento de um quando o elemento `true` avalia, não são avaliadas mais `when` condições. | Sim      |
| caso contrário | Contém o corte de política a utilizar se nenhuma das `when` condições avaliar `true` .                                                                                                                                                                               | Não       |

### <a name="attributes"></a>Atributos

| Atributo                                              | Descrição                                                                                               | Obrigatório |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condition="Expressão booleana &#124; constante booleana" | A expressão booleana ou constante para ser avaliada quando a declaração de política contendo `when` é avaliada. | Sim      |

### <a name="usage"></a><a name="ChooseUsage"></a> Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="forward-request"></a><a name="ForwardRequest"></a> Pedido de encaminho

A `forward-request` apólice remete o pedido de entrada para o serviço de backend especificado no [contexto](api-management-policy-expressions.md#ContextVariables)do pedido . O URL de serviço de backend é especificado nas [definições](./import-and-publish.md) de API e pode ser alterado usando a política [de serviço de backend set.](api-management-transformation-policies.md)

> [!NOTE]
> A eliminação desta política resulta na não reencaminhamento do pedido para o serviço de backend e as políticas na secção de saída são avaliadas imediatamente após a conclusão bem sucedida das políticas na secção de entrada.

### <a name="policy-statement"></a>Declaração política

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

A seguinte política de nível API remete todos os pedidos da API para o serviço de backend com um intervalo de tempo de 60 segundos.

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

Esta política de nível de funcionamento utiliza o `base` elemento para herdar a política de backend do âmbito de nível de API dos pais.

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

Esta política de nível de operação remete explicitamente todos os pedidos para o serviço de backend com um intervalo de tempo de 120 e não herda a política de backend de nível de API dos pais. Se o serviço de backend responder com um código de estado de erro de 400 a 599 inclusive, será ativada a secção [de erro.](api-management-error-handling-policies.md)

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

Esta política de nível de funcionamento não encaminha pedidos para o serviço de backend.

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

| Elemento         | Descrição   | Obrigatório |
| --------------- | ------------- | -------- |
| pedido para o futuro | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo                                     | Descrição                                                                                                                                                                                                                                                                                                    | Obrigatório | Predefinição |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="inteiro"                             | A quantidade de tempo em segundos para aguardar que os cabeçalhos de resposta HTTP sejam devolvidos pelo serviço de backend antes de um erro de tempo for aumentado. O valor mínimo é de 0 segundos. Valores superiores a 240 segundos não podem ser honrados, uma vez que a infraestrutura de rede subjacente pode deixar cair as ligações inativas após este tempo. | Não       | Nenhum    |
| follow-redirects="falso &#124; verdadeiro"          | Especifica se os redirecionamentos do serviço de backend são seguidos pelo gateway ou devolvidos ao chamador.                                                                                                                                                                                                    | Não       | false   |
| buffer-request-body="falso &#124; verdadeiro"       | Quando definido como pedido "verdadeiro" é tamponado e será reutilizado em [re-try](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Não       | false   |
| fail-on-error-status-code="falso &#124; verdadeiro" | Quando definido para a secção [de erro](api-management-error-handling-policies.md) verdadeiro, os códigos de resposta entre 400 e 599 inclusive.                                                                                                                                                                      | Não       | false   |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** backend
-   **Âmbitos de política:** todos os âmbitos

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a> Limitação da concordância

A `limit-concurrency` política impede que as políticas em anexo executem mais do que o número especificado de pedidos a qualquer momento. Ao ultrapassar esse número, os novos pedidos falharão imediatamente com o código de estado de pedidos de muitos.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a> Declaração política

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra como limitar o número de pedidos reencaminhados para um backend com base no valor de uma variável de contexto.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    </limit-concurrency>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento           | Descrição   | Obrigatório |
| ----------------- | ------------- | -------- |
| limitação da concordância | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                        | Obrigatório | Predefinição |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Uma corda. Expressão permitida. Especifica o âmbito de concordância. Pode ser partilhado por várias políticas. | Sim      | N/D     |
| contagem máxima | Um número inteiro. Especifica um número máximo de pedidos que são autorizados a entrar na apólice.           | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a> Faça login no Centro de Eventos

A `log-to-eventhub` política envia mensagens no formato especificado para um Centro de Eventos definido por uma entidade Logger. Como o seu nome indica, a política é utilizada para guardar informações de contexto de pedido ou resposta selecionadas para análise online ou offline.

> [!NOTE]
> Para um guia passo a passo sobre a configuração de um centro de eventos e eventos de registo, consulte [Como registar eventos de Gestão de API com Azure Event Hubs](./api-management-howto-log-event-hubs.md).

### <a name="policy-statement"></a>Declaração política

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exemplo

Qualquer string pode ser usado como o valor a ser registado em Centros de Eventos. Neste exemplo, a data e hora, nome do serviço de implantação, ID de pedido, endereço IP e nome de operação para todas as chamadas de entrada são registados no centro de eventos Logger registado com o `contoso-logger` ID

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

| Elemento         | Descrição                                                                     | Obrigatório |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | Elemento de raiz. O valor deste elemento é a cadeia para iniciar sessão no seu centro de eventos. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                               | Obrigatório                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | A identificação do Madeireiro registou-se no seu serviço de Gestão API.         | Sim                                                                  |
| partição id  | Especifica o índice da partição onde as mensagens são enviadas.             | Opcional. Este atributo não pode ser utilizado se `partition-key` for utilizado. |
| chave de partição | Especifica o valor utilizado para a atribuição de divisórias quando as mensagens são enviadas. | Opcional. Este atributo não pode ser utilizado se `partition-id` for utilizado.  |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="mock-response"></a><a name="mock-response"></a> Resposta falsa

O `mock-response` , como o nome indica, é usado para ridicularizar APIs e operações. Aborta a execução normal do gasoduto e devolve uma resposta ridícula ao chamador. A política tenta sempre retribuir respostas de maior fidelidade. Prefere exemplos de conteúdo de resposta, sempre que disponível. Gera respostas de amostra a partir de esquemas, quando os esquemas são fornecidos e os exemplos não são. Se nenhum dos exemplos ou esquemas for encontrado, as respostas sem conteúdo são devolvidas.

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

| Elemento       | Descrição   | Obrigatório |
| ------------- | ------------- | -------- |
| mock-response | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo    | Descrição                                                                                           | Obrigatório | Predefinição |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| código de estado  | Especifica o código de estado de resposta e é utilizado para selecionar o exemplo ou esquema correspondente.                 | Não       | 200     |
| tipo de conteúdo | Especifica o `Content-Type` valor do cabeçalho de resposta e é utilizado para selecionar o exemplo ou esquema correspondente. | Não       | Nenhum    |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="retry"></a><a name="Retry"></a> Retry

A `retry` política executa as suas políticas infantis uma vez e, em seguida, retrifica a sua execução até que a repetição se torne ou `condition` `false` redaça. `count`

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

No exemplo seguinte, o reencaminhamento de pedidos é novamente experimentado até dez vezes usando um algoritmo de retíria exponencial. Uma vez `first-fast-retry` que está definido como falso, todas as tentativas de rettentámos estão sujeitas ao algoritmo exponencial de retry.

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

| Elemento | Descrição                                                         | Obrigatório |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Elemento de raiz. Pode conter quaisquer outras políticas como elementos infantis. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                                                                                                                                           | Obrigatório | Predefinição |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condição        | Um literal ou [expressão](api-management-policy-expressions.md) booleano especificando se as retrósias devem ser paradas `false` () ou continuadas ( `true` ).      | Sim      | N/D     |
| count            | Um número positivo que especifica o número máximo de tentativas.                                                                                | Sim      | N/D     |
| interval         | Um número positivo em segundos especificando o intervalo de espera entre as tentativas de repetição.                                                                 | Sim      | N/D     |
| intervalo máximo     | Um número positivo em segundos especificando o intervalo máximo de espera entre as tentativas de repetição. É usado para implementar um algoritmo exponencial de retíria. | Não       | N/D     |
| delta            | Um número positivo em segundos especificando o incremento do intervalo de espera. É usado para implementar os algoritmos lineares e exponenciais de retíria.             | Não       | N/D     |
| primeira-rápida retry | Se estiver `true` definido, a primeira tentativa de repetição é executada imediatamente.                                                                                  | Não       | `false` |

> [!NOTE]
> Quando apenas as `interval` especificadas são especificadas, são executadas recaídas de intervalo **fixa.**
> Quando apenas o `interval` e `delta` especificado, é utilizado um algoritmo de relagem de intervalo **linear,** onde o tempo de espera entre as retrógios é calculado de acordo com a seguinte fórmula - `interval + (count - 1)*delta` .
> Quando o `interval` algoritmo de `max-interval` `delta` relemque de intervalo **exponencial** é aplicado, onde o tempo de espera entre as retrações está a crescer exponencialmente do valor `interval` para o valor de acordo com a seguinte fórmula - `max-interval` . `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes) Note que as restrições à política infantil serão herdadas por esta política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="return-response"></a><a name="ReturnResponse"></a> Resposta de retorno

A `return-response` apólice aborta a execução do gasoduto e devolve uma resposta padrão ou personalizada ao chamador. A resposta predefinida `200 OK` é sem corpo. A resposta personalizada pode ser especificada através de uma variável de contexto ou declarações políticas. Quando ambos são fornecidos, a resposta contida na variável de contexto é modificada pelas declarações de política antes de ser devolvida ao chamador.

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

| Elemento         | Descrição                                                                               | Obrigatório |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| resposta de retorno | Elemento de raiz.                                                                             | Sim      |
| set-header      | Uma declaração [política de cabeçalho.](api-management-transformation-policies.md#SetHTTPheader) | Não       |
| conjunto de corpo        | Uma [declaração política de corpo definido.](api-management-transformation-policies.md#SetBody)         | Não       |
| estado de conjunto      | Uma declaração de política [de definição.](api-management-advanced-policies.md#SetStatus)           | Não       |

### <a name="attributes"></a>Atributos

| Atributo              | Descrição                                                                                                                                                                          | Obrigatório  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| resposta-nome variável | O nome da variável de contexto referenciada, por exemplo, de uma política [de pedido de envio](api-management-advanced-policies.md#SendRequest) a montante e contendo um `Response` objeto | Opcional. |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a> Enviar um pedido de ida

A `send-one-way-request` apólice envia o pedido fornecido para o URL especificado sem esperar por uma resposta.

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

Esta política de amostra mostra um exemplo de utilização da `send-one-way-request` política para enviar uma mensagem para uma sala de chat Slack se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre esta amostra, consulte [utilização de serviços externos do serviço de Gestão API da Azure.](./api-management-sample-send-request.md)

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

| Elemento                    | Descrição                                                                                                 | Obrigatório                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| enviar-um-pedido de ida       | Elemento de raiz.                                                                                               | Sim                             |
| url                        | A URL do pedido.                                                                                     | Não se mode=copy; caso contrário, sim. |
| método                     | O método HTTP para o pedido.                                                                            | Não se mode=copy; caso contrário, sim. |
| cabeçalho                     | Pedido de cabeçalho. Utilize vários elementos do cabeçalho para vários cabeçalhos de pedido.                                  | Não                              |
| body                       | O corpo de pedido.                                                                                           | Não                              |
| certificado de autenticação | [Certificado para utilização para autenticação do cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Determina se se trata de um novo pedido ou de uma cópia do pedido atual. No modo de saída, mode=copy não inicializa o corpo de pedido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| name          | Especifica o nome do cabeçalho a definir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/D      |
| existe-ação | Especifica que medidas tomar quando o cabeçalho já está especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - sobreposição - substitui o valor do cabeçalho existente.<br />- saltar - não substitui o valor do cabeçalho existente.<br />- apêndice - anexa o valor ao valor do cabeçalho existente.<br />- excluir - retire o cabeçalho do pedido.<br /><br /> Quando definido para `override` a inscrição de várias entradas com o mesmo nome resulta na configuração do cabeçalho de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado. | Não       | sobreposição |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="send-request"></a><a name="SendRequest"></a> Enviar pedido

A `send-request` apólice envia o pedido fornecido para o URL especificado, esperando não mais do que o valor de tempo limite definido.

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

Este exemplo mostra uma maneira de verificar um token de referência com um servidor de autorização. Para obter mais informações sobre esta amostra, consulte [utilização de serviços externos do serviço de Gestão API da Azure.](./api-management-sample-send-request.md)

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

| Elemento                    | Descrição                                                                                                 | Obrigatório                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| envio-pedido               | Elemento de raiz.                                                                                               | Sim                             |
| url                        | A URL do pedido.                                                                                     | Não se mode=copy; caso contrário, sim. |
| método                     | O método HTTP para o pedido.                                                                            | Não se mode=copy; caso contrário, sim. |
| cabeçalho                     | Pedido de cabeçalho. Utilize vários elementos do cabeçalho para vários cabeçalhos de pedido.                                  | Não                              |
| body                       | O corpo de pedido.                                                                                           | Não                              |
| certificado de autenticação | [Certificado para utilização para autenticação do cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo                       | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Determina se se trata de um novo pedido ou de uma cópia do pedido atual. No modo de saída, mode=copy não inicializa o corpo de pedido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| resposta-variável-nome="string" | O nome da variável de contexto que receberá um objeto de resposta. Se a variável não existir, será criada após a execução bem sucedida da apólice e tornar-se-á acessível através da [`context.Variable`](api-management-policy-expressions.md#ContextVariables) recolha.                                                                                                                                                                                                                                                                                                                          | Sim      | N/D      |
| timeout="inteiro"               | O intervalo de tempo em segundos antes da chamada para o URL falhar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Não       | 60       |
| ignorar erro                    | Se for verdade e o pedido resultar em erro:<br /><br /> - Se o nome variável de resposta for especificado, conterá um valor nulo.<br />- Se não for especificado o nome variável de resposta, o contexto. O pedido não será atualizado.                                                                                                                                                                                                                                                                                                                                                                                   | Não       | false    |
| name                            | Especifica o nome do cabeçalho a definir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/D      |
| existe-ação                   | Especifica que medidas tomar quando o cabeçalho já está especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - sobreposição - substitui o valor do cabeçalho existente.<br />- saltar - não substitui o valor do cabeçalho existente.<br />- apêndice - anexa o valor ao valor do cabeçalho existente.<br />- excluir - retire o cabeçalho do pedido.<br /><br /> Quando definido para `override` a inscrição de várias entradas com o mesmo nome resulta na configuração do cabeçalho de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado. | Não       | sobreposição |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a> Definir http proxy

A `proxy` política permite-lhe encaminhar os pedidos reencaminhados para backends através de um representante HTTP. Apenas HTTP (não HTTPS) é suportado entre o gateway e o proxy. Autenticação básica e ntlm.

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

| Elemento | Descrição  | Obrigatório |
| ------- | ------------ | -------- |
| proxy   | Elemento de raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo         | Descrição                                            | Obrigatório | Predefinição |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | URL de procuração na forma de http://host:port .             | Sim      | N/D     |
| username="string" | Nome de utilizador para ser utilizado para autenticação com o representante. | Não       | N/D     |
| palavra-passe="cadeia" | Palavra-passe para ser usada para autenticação com o representante. | Não       | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

## <a name="set-request-method"></a><a name="SetRequestMethod"></a> Definir método de pedido

A `set-method` apólice permite-lhe alterar o método de pedido HTTP para um pedido.

### <a name="policy-statement"></a>Declaração política

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exemplo

Esta política de amostras que utiliza a `set-method` política mostra um exemplo de envio de uma mensagem para uma sala de chat Slack se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre esta amostra, consulte [utilização de serviços externos do serviço de Gestão API da Azure.](./api-management-sample-send-request.md)

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

| Elemento    | Descrição                                                       | Obrigatório |
| ---------- | ----------------------------------------------------------------- | -------- |
| método de conjunto | Elemento de raiz. O valor do elemento especifica o método HTTP. | Sim      |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="set-status-code"></a><a name="SetStatus"></a> Definir código de estado

A `set-status` apólice define o código de estado HTTP para o valor especificado.

### <a name="policy-statement"></a>Declaração política

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Exemplo

Este exemplo mostra como devolver uma resposta 401 se o sinal de autorização for inválido. Para mais informações, consulte [utilização de serviços externos do serviço de Gestão API da Azure](./api-management-sample-send-request.md)

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

| Elemento    | Descrição   | Obrigatório |
| ---------- | ------------- | -------- |
| estado de conjunto | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo       | Descrição                                                | Obrigatório | Predefinição |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| código="inteiro"  | O código de estado HTTP para devolver.                            | Sim      | N/D     |
| reason="string" | Uma descrição do motivo para devolver o código de estado. | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** saída, backend, on-error
-   **Âmbitos de política:** todos os âmbitos

## <a name="set-variable"></a><a name="set-variable"></a> Conjunto variável

A `set-variable` política declara uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) e atribui-lhe um valor especificado através de uma [expressão](api-management-policy-expressions.md) ou de uma corda literal. se a expressão contiver um literal, será convertido para uma corda e o tipo de valor será `System.String` .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a> Declaração política

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a> Exemplo

O exemplo a seguir demonstra uma política variável definida na secção de entrada. Esta política variável definida cria uma `isMobile` variável de [contexto](api-management-policy-expressions.md#ContextVariables) Boolean que é definida como verdadeira se o cabeçalho de `User-Agent` pedido contiver o texto `iPad` ou `iPhone` .

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementos

| Elemento      | Descrição   | Obrigatório |
| ------------ | ------------- | -------- |
| conjunto-variável | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                              | Obrigatório |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | O nome da variável.                                                | Sim      |
| valor     | O valor da variável. Isto pode ser uma expressão ou um valor literal. | Sim      |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro
-   **Âmbitos de política:** todos os âmbitos

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a> Tipos permitidos

As expressões utilizadas na `set-variable` apólice devem devolver um dos seguintes tipos básicos.

-   System.Boolean
-   System.SByte
-   Sistema.Byte
-   Sistema.UInt16
-   Sistema.UInt32
-   Sistema.UInt64
-   Sistema.Int16
-   Sistema.Int32
-   Sistema.Int64
-   Sistema.Decimal
-   Sistema.Único
-   Sistema.Duplo
-   Sistema.Guid
-   Sistema.String
-   Sistema.Char
-   Sistema.DataTime
-   System.TimeSpan
-   Sistema.Byte?
-   Sistema.UInt16?
-   Sistema.UInt32?
-   Sistema.UInt64?
-   Sistema.Int16?
-   Sistema.Int32?
-   Sistema.Int64?
-   Sistema.Decimal?
-   Sistema.Single?
-   Sistema.Duplo?
-   Sistema.Guid?
-   Sistema.String?
-   Sistema.Char?
-   Sistema.DateTime?

## <a name="trace"></a><a name="Trace"></a> Vestígios

A `trace` política adiciona um traço personalizado na saída do Inspetor API, telemetria de Insights de Aplicação e/ou Registos de Recursos.

-   A apólice adiciona um traço personalizado à saída do Inspetor da [API](./api-management-howto-api-inspector.md) quando o rastreio é desencadeado, ou seja, `Ocp-Apim-Trace` o cabeçalho de pedido está presente e definido para o cabeçalho verdadeiro e `Ocp-Apim-Subscription-Key` o cabeçalho de pedido está presente e contém uma chave válida que permite o rastreio.
-   A política cria uma telemetria [Trace](../azure-monitor/app/data-model-trace-telemetry.md) in Application Insights, quando a [integração de Insights de Aplicação](./api-management-howto-app-insights.md) está ativada e o `severity` nível especificado na política é igual ou superior ao `verbosity` nível especificado na definição de diagnóstico.
-   A política adiciona uma propriedade na entrada de registo quando os [Registos de Recursos](./api-management-howto-use-azure-monitor.md#activity-logs) estão ativados e o nível de gravidade especificado na política é superior ou superior ao nível de verbosidade especificado na definição de diagnóstico.

### <a name="policy-statement"></a>Declaração política

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a> Exemplo

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elementos

| Elemento  | Descrição                                                                                                                                          | Obrigatório |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| traço    | Elemento de raiz.                                                                                                                                        | Sim      |
| message  | Uma corda ou expressão a ser registada.                                                                                                                 | Sim      |
| do IdP | Adiciona uma propriedade personalizada à telemetria Application Insights [Trace.](../azure-monitor/app/data-model-trace-telemetry.md) | Não       |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                                               | Obrigatório | Predefinição |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | String literal significativo para o observador de vestígios e especificando a origem da mensagem.                                   | Sim      | N/D     |
| gravidade  | Especifica o nível de gravidade do vestígio. Os valores permitidos `verbose` `information` são, `error` (do mais baixo ao mais alto). | Não       | Verboso |
| name      | Nome da propriedade.                                                                                                     | Sim      | N/D     |
| valor     | Valor do imóvel.                                                                                                    | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="wait"></a><a name="Wait"></a> Espera, espera.

A `wait` política executa as suas políticas imediatas para crianças em paralelo, e aguarda que todas ou uma das suas políticas imediatas para a criança terminem antes de ser concluída. A política de espera pode ter como políticas imediatas para [crianças Enviar pedido,](api-management-advanced-policies.md#SendRequest) [obter valor a partir de cache](api-management-caching-policies.md#GetFromCacheByKey)e políticas de fluxo de [controlo.](api-management-advanced-policies.md#choose)

### <a name="policy-statement"></a>Declaração política

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exemplo

No exemplo seguinte, existem duas `choose` políticas como políticas imediatas para as crianças da `wait` política. Cada uma `choose` destas políticas executa em paralelo. Cada `choose` política tenta recuperar um valor em cache. Se houver uma falha de cache, um serviço de backend é chamado para fornecer o valor. Neste exemplo, a `wait` política não se completa até que todas as suas políticas imediatas para crianças completem, porque o `for` atributo está definido para `all` . Neste exemplo, as variáveis de contexto `execute-branch-one` `value-one` (, `execute-branch-two` , e - `value-two` são declaradas fora do âmbito desta política de exemplo.

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

| Elemento | Descrição                                                                                                   | Obrigatório |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| esperar    | Elemento de raiz. Pode conter apenas como elementos infantis `send-request` `cache-lookup-value` , e `choose` políticas. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                                                                                                                                                                                                                                                                                                                                            | Obrigatório | Predefinição |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| para       | Determina se a política aguarda que `wait` todas as políticas imediatas para crianças sejam concluídas ou apenas uma. Os valores permitidos são:<br /><br /> - `all` - aguardar que todas as políticas imediatas para a criança terminem<br />- qualquer - aguarde que qualquer política imediata de crianças seja completada. Uma vez concluída a primeira política imediata da criança, a `wait` política completa e a execução de quaisquer outras políticas imediatas para crianças é encerrada. | Não       | all     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend
-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas, consulte:

-   [Políticas em Gestão de API](api-management-howto-policies.md)
-   [Expressões de política](api-management-policy-expressions.md)
-   [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
-   [Amostras de política](policy-samples.md)

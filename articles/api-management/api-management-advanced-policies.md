---
title: Políticas avançadas de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas avançadas disponíveis para uso no gerenciamento de API do Azure.
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
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903013"
---
# <a name="api-management-advanced-policies"></a>Políticas avançadas de gerenciamento de API

Este tópico fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter informações sobre como adicionar e configurar políticas, consulte [políticas no gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a>Políticas avançadas

-   [Fluxo de controle](api-management-advanced-policies.md#choose) – aplica-se condicionalmente a instruções de política com base nos resultados da avaliação de [expressões](api-management-policy-expressions.md)booleanas.
-   [Encaminhar solicitação](#ForwardRequest) – encaminha a solicitação para o serviço de back-end.
-   [Simultaneidade de limite](#LimitConcurrency) – impede que políticas incluídas sejam executadas por mais do que o número especificado de solicitações por vez.
-   [Registrar no Hub de eventos](#log-to-eventhub) – envia mensagens no formato especificado para um hub de eventos definido por uma entidade de agente.
-   [Resposta de simulação](#mock-response) – anula a execução do pipeline e retorna uma resposta fictícia diretamente para o chamador.
-   [Retry](#Retry) -repete a execução das instruções de política incluídas, se e até que a condição seja atendida. A execução será repetida nos intervalos de tempo especificados e até a contagem de repetições especificada.
-   [Retornar resposta](#ReturnResponse) – anula a execução do pipeline e retorna a resposta especificada diretamente para o chamador.
-   [Enviar solicitação unidirecional](#SendOneWayRequest) – envia uma solicitação para a URL especificada sem aguardar uma resposta.
-   [Enviar solicitação](#SendRequest) – envia uma solicitação para a URL especificada.
-   [Definir proxy http](#SetHttpProxy) – permite rotear solicitações encaminhadas por meio de um proxy http.
-   [Definir método de solicitação](#SetRequestMethod) – permite que você altere o método http para uma solicitação.
-   [Definir código de status](#SetStatus) -altera o código de status HTTP para o valor especificado.
-   [Definir variável](api-management-advanced-policies.md#set-variable) – persiste um valor em uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) nomeada para acesso posterior.
-   [Trace](#Trace) – adiciona rastreamentos personalizados na saída do [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) , Application insights telemetrias e logs de diagnóstico.
-   [Wait](#Wait) -aguarda a conclusão das políticas de [envio por solicitação](api-management-advanced-policies.md#SendRequest), [obtenção de valor do cache](api-management-caching-policies.md#GetFromCacheByKey)ou [fluxo de controle](api-management-advanced-policies.md#choose) antes de continuar.

## <a name="choose"></a>Fluxo de controle

A política de `choose` aplica instruções de política embutidas com base no resultado da avaliação de expressões booleanas, semelhante a um if-then-else ou uma construção de opção em uma linguagem de programação.

### <a name="ChoosePolicyStatement"></a>Declaração de política

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

A política de fluxo de controle deve conter pelo menos um elemento `<when/>`. O elemento `<otherwise/>` é opcional. As condições nos elementos `<when/>` são avaliadas na ordem de sua aparência na política. As declarações de política incluídas no primeiro elemento `<when/>` com atributo Condition é igual a `true` serão aplicadas. As políticas delimitadas no elemento `<otherwise/>`, se presente, serão aplicadas se todos os atributos de condição do elemento `<when/>` forem `false`.

### <a name="examples"></a>Exemplos

#### <a name="ChooseExample"></a>Exemplo

O exemplo a seguir demonstra uma diretiva [Set-Variable](api-management-advanced-policies.md#set-variable) e duas políticas de fluxo de controle.

A política definir variável está na seção de entrada e cria uma `isMobile` variável de [contexto](api-management-policy-expressions.md#ContextVariables) booliana definida como true se o cabeçalho `User-Agent` solicitação contiver o texto `iPad` ou `iPhone`.

A primeira política de fluxo de controle também está na seção de entrada e aplica condicionalmente uma das duas políticas de [parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) , dependendo do valor da variável de contexto de `isMobile`.

A segunda política de fluxo de controle está na seção de saída e aplica condicionalmente a política [converter XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) quando `isMobile` é definida como `true`.

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

Este exemplo mostra como executar a filtragem de conteúdo removendo elementos de dados da resposta recebida do serviço de back-end ao usar o produto `Starter`. Para ver uma demonstração de como configurar e usar essa política, confira o [episódio 177 do Cloud Cover: mais recursos de gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avança para 34:30. Inicie em 31:50 para ver uma visão geral da [API de previsão do céu escuro](https://developer.forecast.io/) usada para esta demonstração.

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
| selecione    | Elemento raiz.                                                                                                                                                                                                                                                             | Sim      |
| ao      | A condição a ser usada para o `if` ou `ifelse` partes da política de `choose`. Se a política de `choose` tiver várias seções `when`, elas serão avaliadas sequencialmente. Depois que a `condition` de um elemento When é avaliada como `true`, nenhuma outra condição de `when` é avaliada. | Sim      |
| , | Contém o trecho de política a ser usado se nenhuma das condições de `when` forem avaliadas como `true`.                                                                                                                                                                               | Não       |

### <a name="attributes"></a>Atributos

| Atributo                                              | Descrição                                                                                               | Obrigatório |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| Condition = "constante booliana da expressão &#124; booliana" | A expressão booliana ou constante a ser avaliada quando a instrução de política de `when` que a contém é avaliada. | Sim      |

### <a name="ChooseUsage"></a>Usos

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="ForwardRequest"></a>Encaminhar solicitação

A política de `forward-request` encaminha a solicitação de entrada para o serviço de back-end especificado no [contexto](api-management-policy-expressions.md#ContextVariables)da solicitação. A URL do serviço de back-end é especificada nas [configurações](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) da API e pode ser alterada usando a política [definir serviço de back-end](api-management-transformation-policies.md) .

> [!NOTE]
> A remoção dessa política resulta na não encaminhamento da solicitação para o serviço de back-end e as políticas na seção de saída são avaliadas imediatamente após a conclusão bem-sucedida das políticas na seção de entrada.

### <a name="policy-statement"></a>Declaração de política

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

A política de nível de API a seguir encaminha todas as solicitações de API para o serviço de back-end com um intervalo de tempo limite de 60 segundos.

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

Essa política de nível de operação usa o elemento `base` para herdar a política de back-end do escopo do nível da API pai.

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

Essa política de nível de operação encaminha explicitamente todas as solicitações para o serviço de back-end com um tempo limite de 120 e não herda a política de back-end do nível da API pai. Se o serviço de back-end responder com um código de status de erro de 400 para 599, inclusive, a seção [On-Error](api-management-error-handling-policies.md) será disparada.

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

Essa política de nível de operação não encaminha solicitações para o serviço de back-end.

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
| reencaminhar-pedido | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo                                     | Descrição                                                                                                                                                                                                                                                                                                    | Obrigatório | Predefinição |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="integer"                             | A quantidade de tempo em segundos a aguardar que os cabeçalhos de resposta HTTP sejam retornados pelo serviço de back-end antes que um erro de tempo limite seja gerado. O valor mínimo é 0 segundos. Valores maiores que 240 segundos podem não ser respeitados, pois a infraestrutura de rede subjacente pode descartar conexões ociosas após esse tempo. | Não       | Nenhuma    |
| Siga-redirecionamentos = "falso &#124; verdadeiro"          | Especifica se os redirecionamentos do serviço de back-end são seguidos pelo gateway ou retornados para o chamador.                                                                                                                                                                                                    | Não       | false   |
| buffer-Request-Body = "false &#124; true"       | Quando definido como "true", a solicitação é armazenada em buffer e será reutilizada na [nova tentativa](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Não       | false   |
| falha-em-erro-status-código = "falso &#124; verdadeiro" | Quando definido como true triggers [On-Error](api-management-error-handling-policies.md) seção para códigos de resposta no intervalo de 400 a 599, inclusive.                                                                                                                                                                      | Não       | false   |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** back-end
-   **Escopos de política:** todos os escopos

## <a name="LimitConcurrency"></a>Limitar a simultaneidade

A política de `limit-concurrency` impede que políticas incluídas sejam executadas por mais do que o número especificado de solicitações a qualquer momento. Ao exceder esse número, novas solicitações falharão imediatamente com o código de status 429 número excessivo de solicitações.

### <a name="LimitConcurrencyStatement"></a>Declaração de política

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra como limitar o número de solicitações encaminhadas a um back-end com base no valor de uma variável de contexto.

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

| Elemento           | Descrição   | Obrigatório |
| ----------------- | ------------- | -------- |
| limite-simultaneidade | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                        | Obrigatório | Predefinição |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Uma cadeia de caracteres. Expressão permitida. Especifica o escopo de simultaneidade. Pode ser compartilhado por várias políticas. | Sim      | N/A     |
| contagem máxima | Um número inteiro. Especifica um número máximo de solicitações que têm permissão para entrar na política.           | Sim      | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="log-to-eventhub"></a>Registrar no Hub de eventos

A política de `log-to-eventhub` envia mensagens no formato especificado para um hub de eventos definido por uma entidade de agente. Como o nome indica, a política é usada para salvar informações de contexto de solicitação ou de resposta selecionadas para análise online ou offline.

> [!NOTE]
> Para obter um guia passo a passo sobre como configurar um hub de eventos e eventos de log, consulte [como registrar eventos de gerenciamento de API com os hubs de eventos do Azure](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Declaração de política

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exemplo

Qualquer cadeia de caracteres pode ser usada como o valor a ser registrado nos hubs de eventos. Neste exemplo, a data e hora, o nome do serviço de implantação, a ID da solicitação, o endereço IP e o nome da operação para todas as chamadas de entrada são registrados no agente do hub de eventos registrado com a ID de `contoso-logger`

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
| log-to-eventhub | Elemento raiz. O valor desse elemento é a cadeia de caracteres a ser registrada em seu hub de eventos. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                               | Obrigatório                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | A ID do agente de log registrado com o serviço de gerenciamento de API.         | Sim                                                                  |
| ID da partição  | Especifica o índice da partição para a qual as mensagens são enviadas.             | Opcional. Esse atributo não poderá ser usado se `partition-key` for usado. |
| chave de partição | Especifica o valor usado para atribuição de partição quando as mensagens são enviadas. | Opcional. Esse atributo não poderá ser usado se `partition-id` for usado.  |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="mock-response"></a>Resposta fictícia

O `mock-response`, como o nome indica, é usado para simular APIs e operações. Ele anula a execução normal do pipeline e retorna uma resposta fictícia ao chamador. A política sempre tenta retornar respostas de fidelidade mais alta. Ele prefere exemplos de conteúdo de resposta, sempre que disponível. Ele gera respostas de exemplo de esquemas, quando esquemas são fornecidos e exemplos não são. Se nenhum dos exemplos ou esquemas for encontrado, as respostas sem conteúdo serão retornadas.

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

| Elemento       | Descrição   | Obrigatório |
| ------------- | ------------- | -------- |
| mock-response | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo    | Descrição                                                                                           | Obrigatório | Predefinição |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status-código  | Especifica o código de status de resposta e é usado para selecionar o exemplo ou o esquema correspondente.                 | Não       | 200     |
| Tipo de conteúdo | Especifica `Content-Type` valor do cabeçalho de resposta e é usado para selecionar o exemplo ou o esquema correspondente. | Não       | Nenhuma    |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="Retry"></a>Repita

A política de `retry` executa suas políticas filho uma vez e, em seguida, tenta novamente a execução até que o `condition` de repetição se torne `false` ou tentar novamente `count` seja esgotada.

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

No exemplo a seguir, o encaminhamento de solicitação é tentado novamente até dez vezes usando um algoritmo de repetição exponencial. Como `first-fast-retry` está definido como false, todas as tentativas de repetição estão sujeitas ao algoritmo de repetição exponencial.

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
| retry   | Elemento raiz. Pode conter quaisquer outras políticas como seus elementos filho. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                                                                                                                                           | Obrigatório | Predefinição |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition        | Um literal booliano ou [expressão](api-management-policy-expressions.md) que especifica se as repetições devem ser interrompidas (`false`) ou continuadas (`true`).      | Sim      | N/A     |
| count            | Um número positivo que especifica o número máximo de repetições a tentar.                                                                                | Sim      | N/A     |
| intervalo         | Um número positivo, em segundos, que especifica o intervalo de espera entre as tentativas de repetição.                                                                 | Sim      | N/A     |
| intervalo máx.     | Um número positivo, em segundos, que especifica o intervalo de espera máximo entre as tentativas de repetição. Ele é usado para implementar um algoritmo de repetição exponencial. | Não       | N/A     |
| delta            | Um número positivo em segundos que especifica o incremento de intervalo de espera. Ele é usado para implementar os algoritmos de repetição linear e exponencial.             | Não       | N/A     |
| primeira-rápida-repetição | Se definido como `true`, a primeira tentativa de repetição é executada imediatamente.                                                                                  | Não       | `false` |

> [!NOTE]
> Quando apenas o `interval` é especificado, as tentativas de intervalo **fixo** são executadas.
> Quando apenas `interval` e `delta` são especificados, um algoritmo de repetição de intervalo **linear** é usado, em que o tempo de espera entre as repetições é calculado de acordo com a fórmula a seguir-`interval + (count - 1)*delta`.
> Quando o `interval`, `max-interval` e `delta` são especificados, o algoritmo de repetição de intervalo **exponencial** é aplicado, em que o tempo de espera entre as repetições aumenta exponencialmente do valor de `interval` para o valor `max-interval` de acordo com a fórmula a seguir-`min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de política a seguir. Observe que as restrições de uso de política filho serão herdadas por essa política.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="ReturnResponse"></a>Resposta de retorno

A política de `return-response` anula a execução do pipeline e retorna uma resposta padrão ou personalizada para o chamador. A resposta padrão é `200 OK` sem corpo. A resposta personalizada pode ser especificada por meio de uma variável de contexto ou instruções de política. Quando ambos são fornecidos, a resposta contida na variável de contexto é modificada pelas instruções de política antes de ser retornada ao chamador.

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

| Elemento         | Descrição                                                                               | Obrigatório |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| retorno de resposta | Elemento raiz.                                                                             | Sim      |
| set-header      | Uma declaração [de política Set-header](api-management-transformation-policies.md#SetHTTPheader) . | Não       |
| definir corpo        | Uma declaração [de política Set-Body](api-management-transformation-policies.md#SetBody) .         | Não       |
| set-status      | Uma declaração [de política Set-status](api-management-advanced-policies.md#SetStatus) .           | Não       |

### <a name="attributes"></a>Atributos

| Atributo              | Descrição                                                                                                                                                                          | Obrigatório  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| response-variable-name | O nome da variável de contexto referenciada de, por exemplo, uma política de [envio e solicitação](api-management-advanced-policies.md#SendRequest) de upstream e que contém um objeto `Response` | Opcional. |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="SendOneWayRequest"></a>Enviar solicitação unidirecional

A política de `send-one-way-request` envia a solicitação fornecida para a URL especificada sem aguardar uma resposta.

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

Esta política de exemplo mostra um exemplo de como usar a política de `send-one-way-request` para enviar uma mensagem para uma sala de chat de atraso se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre este exemplo, consulte [usando serviços externos do serviço de gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| solicitação Send-unidirecional       | Elemento raiz.                                                                                               | Sim                             |
| url                        | A URL da solicitação.                                                                                     | Não se Mode = Copy; caso contrário, sim. |
| método                     | O método HTTP para a solicitação.                                                                            | Não se Mode = Copy; caso contrário, sim. |
| cabeçalho                     | Cabeçalho da solicitação. Use vários elementos de cabeçalho para vários cabeçalhos de solicitação.                                  | Não                              |
| body                       | O corpo da solicitação.                                                                                           | Não                              |
| autenticação-certificado | [Certificado a ser usado para autenticação de cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo     | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Determina se esta é uma nova solicitação ou uma cópia da solicitação atual. No modo de saída, Mode = Copy não inicializa o corpo da solicitação.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| nome          | Especifica o nome do cabeçalho a definir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/A      |
| Exists-ação | Especifica a ação a ser tomada quando o cabeçalho já estiver especificado. Esse atributo deve ter um dos valores a seguir.<br /><br /> -Override – substitui o valor do cabeçalho existente.<br />-Skip-não substitui o valor do cabeçalho existente.<br />-Append – acrescenta o valor ao valor de cabeçalho existente.<br />-Delete – remove o cabeçalho da solicitação.<br /><br /> Quando definido como `override` a listar várias entradas com os mesmos resultados de nome no cabeçalho que está sendo definido de acordo com todas as entradas (que serão listadas várias vezes); somente os valores listados serão definidos no resultado. | Não       | substituição |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="SendRequest"></a>Enviar solicitação

A política de `send-request` envia a solicitação fornecida para a URL especificada, aguardando não mais do que o valor de tempo limite definido.

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

Este exemplo mostra uma maneira de verificar um token de referência com um servidor de autorização. Para obter mais informações sobre este exemplo, consulte [usando serviços externos do serviço de gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| Enviar solicitação               | Elemento raiz.                                                                                               | Sim                             |
| url                        | A URL da solicitação.                                                                                     | Não se Mode = Copy; caso contrário, sim. |
| método                     | O método HTTP para a solicitação.                                                                            | Não se Mode = Copy; caso contrário, sim. |
| cabeçalho                     | Cabeçalho da solicitação. Use vários elementos de cabeçalho para vários cabeçalhos de solicitação.                                  | Não                              |
| body                       | O corpo da solicitação.                                                                                           | Não                              |
| autenticação-certificado | [Certificado a ser usado para autenticação de cliente](api-management-authentication-policies.md#ClientCertificate) | Não                              |

### <a name="attributes"></a>Atributos

| Atributo                       | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Obrigatório | Predefinição  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Determina se esta é uma nova solicitação ou uma cópia da solicitação atual. No modo de saída, Mode = Copy não inicializa o corpo da solicitação.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Não       | Novo      |
| response-variable-name="string" | O nome da variável de contexto que receberá um objeto de resposta. Se a variável não existir, ela será criada após a execução bem-sucedida da política e ficará acessível por meio de [`context.Variable`](api-management-policy-expressions.md#ContextVariables) coleção.                                                                                                                                                                                                                                                                                                                          | Sim      | N/A      |
| timeout="integer"               | O intervalo de tempo limite em segundos antes da chamada para a URL falhar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Não       | 60       |
| ignore-error                    | Se for true e a solicitação resultar em um erro:<br /><br /> -Se o nome da variável de resposta tiver sido especificado, ele conterá um valor nulo.<br />-Se Response-variável-name não tiver sido especificado, Context. A solicitação não será atualizada.                                                                                                                                                                                                                                                                                                                                                                                   | Não       | false    |
| nome                            | Especifica o nome do cabeçalho a definir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Sim      | N/A      |
| Exists-ação                   | Especifica a ação a ser tomada quando o cabeçalho já estiver especificado. Esse atributo deve ter um dos valores a seguir.<br /><br /> -Override – substitui o valor do cabeçalho existente.<br />-Skip-não substitui o valor do cabeçalho existente.<br />-Append – acrescenta o valor ao valor de cabeçalho existente.<br />-Delete – remove o cabeçalho da solicitação.<br /><br /> Quando definido como `override` a listar várias entradas com os mesmos resultados de nome no cabeçalho que está sendo definido de acordo com todas as entradas (que serão listadas várias vezes); somente os valores listados serão definidos no resultado. | Não       | substituição |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="SetHttpProxy"></a>Definir proxy HTTP

A política de `proxy` permite rotear solicitações encaminhadas para back-ends por meio de um proxy HTTP. Há suporte apenas para HTTP (não HTTPS) entre o gateway e o proxy. Somente autenticação básica e NTLM.

### <a name="policy-statement"></a>Declaração de política

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Exemplo

Observe o uso de [Propriedades](api-management-howto-properties.md) como valores de nome de usuário e senha para evitar armazenar informações confidenciais no documento de política.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementos

| Elemento | Descrição  | Obrigatório |
| ------- | ------------ | -------- |
| Proxy   | Elemento raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo         | Descrição                                            | Obrigatório | Predefinição |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | URL do proxy na forma de http://host:port.             | Sim      | N/A     |
| username="string" | Nome de usuário a ser usado para autenticação com o proxy. | Não       | N/A     |
| password="string" | Senha a ser usada para autenticação com o proxy. | Não       | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada

-   **Escopos de política:** todos os escopos

## <a name="SetRequestMethod"></a>Definir método de solicitação

A política de `set-method` permite que você altere o método de solicitação HTTP para uma solicitação.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exemplo

Esta política de exemplo que usa a política de `set-method` mostra um exemplo de envio de uma mensagem para uma sala de chat de atraso se o código de resposta HTTP for maior ou igual a 500. Para obter mais informações sobre este exemplo, consulte [usando serviços externos do serviço de gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| Set-Method | Elemento raiz. O valor do elemento Especifica o método HTTP. | Sim      |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="SetStatus"></a>Definir código de status

A política de `set-status` define o código de status HTTP para o valor especificado.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Exemplo

Este exemplo mostra como retornar uma resposta 401 se o token de autorização for inválido. Para obter mais informações, consulte [usando serviços externos do serviço de gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
| set-status | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo       | Descrição                                                | Obrigatório | Predefinição |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | O código de status HTTP a ser retornado.                            | Sim      | N/A     |
| reason="string" | Uma descrição do motivo para retornar o código de status. | Sim      | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** saída, back-end, em caso de erro
-   **Escopos de política:** todos os escopos

## <a name="set-variable"></a>Definir variável

A política de `set-variable` declara uma variável de [contexto](api-management-policy-expressions.md#ContextVariables) e atribui a ela um valor especificado por meio de uma [expressão](api-management-policy-expressions.md) ou um literal de cadeia de caracteres. se a expressão contiver um literal, ela será convertida em uma cadeia de caracteres e o tipo do valor será `System.String`.

### <a name="set-variablePolicyStatement"></a>Declaração de política

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a>Exemplo

O exemplo a seguir demonstra uma política de definir variável na seção de entrada. Essa política de variável de conjunto cria um `isMobile` variável de [contexto](api-management-policy-expressions.md#ContextVariables) booliano definida como true se o cabeçalho de solicitação `User-Agent` contiver o texto `iPad` ou `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementos

| Elemento      | Descrição   | Obrigatório |
| ------------ | ------------- | -------- |
| set-variable | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                              | Obrigatório |
| --------- | ------------------------------------------------------------------------ | -------- |
| nome      | O nome da variável.                                                | Sim      |
| valor     | O valor da variável. Pode ser uma expressão ou um valor literal. | Sim      |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro
-   **Escopos de política:** todos os escopos

### <a name="set-variableAllowedTypes"></a>Tipos permitidos

As expressões usadas na política de `set-variable` devem retornar um dos tipos básicos a seguir.

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
-   System. byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System.Single?
-   System. Double?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

## <a name="Trace"></a>Rastreou

A política de `trace` adiciona um rastreamento personalizado à saída do Inspetor de API, Application Insights telemetrias e/ou logs de diagnóstico.

-   A política adiciona um rastreamento personalizado à saída do [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) quando o rastreamento é disparado, ou seja, `Ocp-Apim-Trace` cabeçalho de solicitação está presente e definido como true e `Ocp-Apim-Subscription-Key` cabeçalho de solicitação está presente e contém uma chave válida que permite o rastreamento.
-   A política cria uma telemetria de [rastreamento](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) no Application insights, quando [Application insights integração](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) está habilitada e o nível de `severity` especificado na política é ou superior ao nível de `verbosity` especificado na configuração de diagnóstico.
-   A política adiciona uma propriedade na entrada de log quando [os logs de diagnóstico](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) estão habilitados e o nível de severidade especificado na política é ou superior ao nível de detalhes especificado na configuração de diagnóstico.

### <a name="policy-statement"></a>Declaração de política

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

| Elemento  | Descrição                                                                                                                                          | Obrigatório |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rastreou    | Elemento raiz.                                                                                                                                        | Sim      |
| message  | Uma cadeia de caracteres ou expressão a ser registrada.                                                                                                                 | Sim      |
| do IdP | Adiciona uma propriedade personalizada à telemetria de [rastreamento](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) de Application insights. | Não       |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                                               | Obrigatório | Predefinição |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Literal de cadeia de caracteres significativo para o Visualizador de rastreamento e especificando a origem da mensagem.                                   | Sim      | N/A     |
| gravidade  | Especifica o nível de severidade do rastreamento. Os valores permitidos são `verbose`, `information`, `error` (do mais baixo ao mais alto). | Não       | Verboso |
| nome      | Nome da propriedade.                                                                                                     | Sim      | N/A     |
| valor     | Valor da propriedade.                                                                                                    | Sim      | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

## <a name="Wait"></a>Esperado

A política de `wait` executa suas políticas filho imediatas em paralelo e aguarda que todas ou uma de suas políticas filho imediatas sejam concluídas antes de ser concluída. A política de espera pode ter como suas políticas filho imediatas [enviam solicitações de solicitação](api-management-advanced-policies.md#SendRequest), [obtenção de valor do cache](api-management-caching-policies.md#GetFromCacheByKey)e [fluxo de controle](api-management-advanced-policies.md#choose) .

### <a name="policy-statement"></a>Declaração de política

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exemplo

No exemplo a seguir, há duas políticas de `choose` como políticas filho imediatas da política de `wait`. Cada uma dessas políticas de `choose` é executada em paralelo. Cada política de `choose` tenta recuperar um valor em cache. Se houver um erro de cache, um serviço de back-end será chamado para fornecer o valor. Neste exemplo, a política de `wait` não é concluída até que todas as suas políticas filho imediatas sejam concluídas, porque o atributo `for` está definido como `all`. Neste exemplo, as variáveis de contexto (`execute-branch-one`, `value-one`, `execute-branch-two`e `value-two`) são declaradas fora do escopo desta política de exemplo.

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
| esperado    | Elemento raiz. Pode conter como elementos filho somente `send-request`as políticas, `cache-lookup-value`e `choose`. | Sim      |

### <a name="attributes"></a>Atributos

| Atributo | Descrição                                                                                                                                                                                                                                                                                                                                                                                                            | Obrigatório | Predefinição |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| 작성하시겠어요?       | Determina se a política de `wait` aguarda que todas as políticas filho imediatas sejam concluídas ou apenas uma. Valores permitidos são:<br /><br /> - `all`-aguardar a conclusão de todas as políticas filho imediatas<br />-Any-aguardar a conclusão de qualquer política filho imediata. Depois que a primeira política filho imediata for concluída, a política de `wait` será concluída e a execução de quaisquer outras políticas filho imediatas é encerrada. | Não       | all     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end
-   **Escopos de política:** todos os escopos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

-   [Políticas no gerenciamento de API](api-management-howto-policies.md)
-   [Expressões de política](api-management-policy-expressions.md)
-   [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
-   [Exemplos de política](policy-samples.md)

---
title: Corrente de funções em funções duráveis - Azure
description: Aprenda a executar uma amostra de Funções Duráveis que execute uma sequência de funções.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77562071"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Corrente de funções em funções duráveis - Amostra de sequência olá

A corrente de funções refere-se ao padrão de execução de uma sequência de funções numa determinada ordem. Muitas vezes, a saída de uma função tem de ser aplicada à entrada de outra função. Este artigo descreve a sequência de corrente que cria quando completa as Funções Duráveis ([C#](durable-functions-create-first-csharp.md) ou [JavaScript).](quickstart-js-vscode.md) Para obter mais informações sobre funções duráveis, consulte a [visão geral das Funções Duráveis](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E1_HelloSequence`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que liga `E1_SayHello` várias vezes numa sequência. Armazena as saídas das `E1_SayHello` chamadas e regista os resultados.
* `E1_SayHello`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que prepara uma corda com "Olá".
* `HttpStart`: Uma função ativada http que inicia uma instância do orquestrador.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence função orquestradora

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Todas as funções de orquestração C# `DurableOrchestrationContext`devem ter `Microsoft.Azure.WebJobs.Extensions.DurableTask` um parâmetro de tipo, que exista na montagem. Este objeto de contexto *activity* permite-lhe ligar para outras `CallActivityAsync` funções de atividade e passar parâmetros de entrada utilizando o seu método.

O código `E1_SayHello` liga três vezes em sequência com diferentes valores de parâmetros. O valor de retorno de `outputs` cada chamada é adicionado à lista, que é devolvida no final da função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> As Funções Duráveis JavaScript estão disponíveis apenas para as funções 2.0 tempo de funcionamento.

#### <a name="functionjson"></a>function.json

Se utilizar o Visual Studio Code ou o portal Azure para desenvolvimento, aqui está o conteúdo do ficheiro *function.json* para a função de orquestrador. A maioria dos *ficheiros de orquestração.json* são quase exatamente assim.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

O importante é `orchestrationTrigger` o tipo de ligação. Todas as funções de orquestrador devem usar este tipo de gatilho.

> [!WARNING]
> Para respeitar a regra "não Em I/O" das funções orquestradoras, não utilize `orchestrationTrigger` nenhuma encadernação de entrada ou saída ao utilizar a ligação do gatilho.  Se forem necessárias outras encadernações de entrada `activityTrigger` ou de saída, devem, em vez disso, ser utilizadas no contexto das funções, que são chamadas pelo orquestrador. Para mais informações, consulte o artigo de restrições de código de [função orquestrador.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js

Aqui está a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração JavaScript devem incluir o [ `durable-functions` módulo](https://www.npmjs.com/package/durable-functions). É uma biblioteca que lhe permite escrever Funções Duráveis no JavaScript. Existem três diferenças significativas entre uma função de orquestração e outras funções JavaScript:

1. A função é uma [função geradora. .](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. A função está embrulhada `durable-functions` numa chamada `orchestrator` ao `df`método do módulo (aqui).
3. A função deve ser sincronizada. Uma vez que o método "orquestrador" trata de chamar "context.done", a função deve simplesmente "devolver".

O `context` objeto `df` contém um objeto de contexto de orquestração durável que permite `callActivity` ligar para outras funções de *atividade* e passar parâmetros de entrada usando o seu método. O código `E1_SayHello` liga três vezes em sequência `yield` com diferentes valores de parâmetros, usando para indicar que a execução deve esperar que as chamadas de função de atividade assinária sejam devolvidas. O valor de retorno de `outputs` cada chamada é adicionado à matriz, que é devolvida no final da função.

---

### <a name="e1_sayhello-activity-function"></a>função de atividade E1_SayHello

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

As atividades usam o `ActivityTrigger` atributo. Utilize o `IDurableActivityContext` fornecido para realizar ações relacionadas com a `GetInput<T>`atividade, tais como aceder ao valor de entrada utilizando .

A implementação é `E1_SayHello` uma operação de formatação de cordas relativamente trivial.

Em vez de `IDurableActivityContext`se ligar a um, pode ligar-se diretamente ao tipo que é passado para a função de atividade. Por exemplo:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/função.json

O ficheiro *função.json* `E1_SayHello` para a função de atividade é semelhante ao da função de exceção de `E1_HelloSequence` que utiliza um `activityTrigger` tipo de ligação em vez de um `orchestrationTrigger` tipo de ligação.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Qualquer função chamada por uma `activityTrigger` função de orquestração deve utilizar a encadernação.

A implementação é `E1_SayHello` uma operação de formatação de cordas relativamente trivial.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário de uma função de orquestração JavaScript, uma função de atividade não precisa de configuração especial. A entrada que lhe foi passada pela função orquestradora está localizada no `context.bindings` objeto sob o nome da `activityTrigger` encadernação - neste caso, `context.bindings.name`. O nome de ligação pode ser definido como parâmetro da função exportada e acedido diretamente, que é o que o código da amostra faz.

---

### <a name="httpstart-client-function"></a>Função do cliente httpStart

Pode iniciar uma instância de função de orquestrador utilizando uma função do cliente. Utilizará a `HttpStart` função ativada http `E1_HelloSequence`para iniciar casos de .

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Para interagir com os orquestradores, `DurableClient` a função deve incluir uma ligação de entrada. Usa o cliente para iniciar uma orquestração. Também pode ajudá-lo a devolver uma resposta HTTP contendo URLs para verificar o estado da nova orquestração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Para interagir com os orquestradores, `durableClient` a função deve incluir uma ligação de entrada.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Utilize `df.getClient` para `DurableOrchestrationClient` obter um objeto. Usa o cliente para iniciar uma orquestração. Também pode ajudá-lo a devolver uma resposta HTTP contendo URLs para verificar o estado da nova orquestração.

---

## <a name="run-the-sample"></a>Executar o exemplo

Para executar `E1_HelloSequence` a orquestração, envie o `HttpStart` seguinte pedido HTTP POST para a função.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> O snippet HTTP anterior pressupõe `host.json` que existe uma `api/` entrada no ficheiro que remove o prefixo predefinido de todas as funções de gatilho HTTP URLs. Pode encontrar a marcação para `host.json` esta configuração no ficheiro nas amostras.

Por exemplo, se estiver a executar a amostra numa aplicação de função chamada "myfunctionapp", substitua "{host}" por "myfunctionapp.azurewebsites.net".

O resultado é uma resposta HTTP 202, como esta (aparada para brevidade):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste ponto, a orquestração está em fila e começa a funcionar imediatamente. O URL `Location` no cabeçalho pode ser utilizado para verificar o estado da execução.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

O resultado é o estatuto da orquestração. Funciona e completa rapidamente, por isso vê-lo no estado *completo* com uma resposta que se parece com esta (aparada para brevidade):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como pode ver, `runtimeStatus` a instância é *concluída* e contém `output` o resultado em série da json da execução da função orquestradora.

> [!NOTE]
> Pode implementar uma lógica de arranque `queueTrigger`semelhante `eventHubTrigger`para `timerTrigger`outros tipos de gatilho, como, ou .

Olhe para os registos de execução da função. A `E1_HelloSequence` função começou e completou várias vezes devido ao comportamento de repetição descrito no tópico de fiabilidade da [orquestração.](durable-functions-orchestrations.md#reliability) Por outro lado, houve apenas `E1_SayHello` três execuções, uma vez que essas execuções de funções não são repetidas.

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou uma simples orquestração de corrente de função. A amostra seguinte mostra como implementar o padrão de ventilação/fã-in.

> [!div class="nextstepaction"]
> [Executar a amostra fan-out/fan-in](durable-functions-cloud-backup.md)

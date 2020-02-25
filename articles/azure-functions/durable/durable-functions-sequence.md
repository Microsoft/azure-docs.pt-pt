---
title: Corrente de funções em funções duráveis - Azure
description: Aprenda a executar uma amostra de Funções Duráveis que execute uma sequência de funções.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562071"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Corrente de funções em funções duráveis - Amostra de sequência olá

A corrente de funções refere-se ao padrão de execução de uma sequência de funções numa determinada ordem. Muitas vezes, a saída de uma função tem de ser aplicada à entrada de outra função. Este artigo descreve a sequência de corrente que cria quando[C#](durable-functions-create-first-csharp.md) completa as Funções Duráveis de arranque rápido (ou [JavaScript).](quickstart-js-vscode.md) Para obter mais informações sobre funções duráveis, consulte a [visão geral das Funções Duráveis](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E1_HelloSequence`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que chama `E1_SayHello` várias vezes numa sequência. Armazena as saídas das chamadas `E1_SayHello` e regista os resultados.
* `E1_SayHello`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que prepara uma corda com "Olá".
* `HttpStart`: Uma função ativada em HTTP que inicia uma instância do orquestrador.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence função orquestradora

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Todas C# as funções de orquestração devem ter um parâmetro de tipo `DurableOrchestrationContext`, que exista na montagem `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Este objeto de contexto permite-lhe ligar para outras funções de *atividade* e passar parâmetros de entrada utilizando o seu método `CallActivityAsync`.

O código chama `E1_SayHello` três vezes em sequência com diferentes valores de parâmetros. O valor de retorno de cada chamada é adicionado à lista de `outputs`, que é devolvida no final da função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> As Funções Duráveis JavaScript estão disponíveis apenas para as funções 2.0 tempo de funcionamento.

#### <a name="functionjson"></a>function.json

Se utilizar o Visual Studio Code ou o portal Azure para desenvolvimento, aqui está o conteúdo do ficheiro *function.json* para a função de orquestrador. A maioria dos *ficheiros de orquestração.json* são quase exatamente assim.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

O importante é o tipo `orchestrationTrigger` de ligação. Todas as funções de orquestrador devem usar este tipo de gatilho.

> [!WARNING]
> Para respeitar a regra "não Em I/O" das funções orquestradoras, não utilize nenhuma encadernação de entrada ou saída ao utilizar a ligação do gatilho `orchestrationTrigger`.  Se forem necessárias outras encadernações de entrada ou de saída, devem, em vez disso, ser utilizadas no contexto de funções `activityTrigger`, que são chamadas pelo orquestrador. Para mais informações, consulte o artigo de restrições de código de [função orquestrador.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js

Aqui está a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração JavaScript devem incluir o [módulo`durable-functions`](https://www.npmjs.com/package/durable-functions). É uma biblioteca que lhe permite escrever Funções Duráveis no JavaScript. Existem três diferenças significativas entre uma função de orquestração e outras funções JavaScript:

1. A função é uma [função geradora. .](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. A função está embrulhada numa chamada para o método de `orchestrator` do módulo `durable-functions` (aqui `df`).
3. A função deve ser sincronizada. Uma vez que o método "orquestrador" trata de chamar "context.done", a função deve simplesmente "devolver".

O objeto `context` contém um objeto de contexto de orquestração `df` durável que permite ligar para outras funções de *atividade* e passar parâmetros de entrada usando o seu método `callActivity`. O código chama `E1_SayHello` três vezes em sequência com diferentes valores de parâmetros, utilizando `yield` para indicar que a execução deve aguardar que as chamadas de função de atividade assinária sejam devolvidas. O valor de retorno de cada chamada é adicionado ao conjunto de `outputs`, que é devolvido no final da função.

---

### <a name="e1_sayhello-activity-function"></a>função de atividade E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

As atividades usam o atributo `ActivityTrigger`. Utilize o `IDurableActivityContext` fornecido para realizar ações relacionadas com a atividade, tais como aceder ao valor de entrada utilizando `GetInput<T>`.

A implementação de `E1_SayHello` é uma operação de formatação de cordas relativamente trivial.

Em vez de se ligar a um `IDurableActivityContext`, pode ligar-se diretamente ao tipo que é passado para a função de atividade. Por exemplo:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/função.json

O ficheiro *função.json* para a função de atividade `E1_SayHello` é semelhante ao da `E1_HelloSequence` exceto que utiliza um tipo de ligação `activityTrigger` em vez de um tipo de ligação `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Qualquer função chamada por uma função de orquestração deve utilizar o `activityTrigger` encadernação.

A implementação de `E1_SayHello` é uma operação de formatação de cordas relativamente trivial.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário de uma função de orquestração JavaScript, uma função de atividade não precisa de configuração especial. A entrada que lhe foi passada pela função orquestradora está localizada no `context.bindings` objeto sob o nome da ligação `activityTrigger` - neste caso, `context.bindings.name`. O nome de ligação pode ser definido como parâmetro da função exportada e acedido diretamente, que é o que o código da amostra faz.

---

### <a name="httpstart-client-function"></a>Função do cliente httpStart

Pode iniciar uma instância de função de orquestrador utilizando uma função do cliente. Utilizará a função `HttpStart` HTTP ativada para iniciar casos de `E1_HelloSequence`.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Para interagir com os orquestradores, a função deve incluir uma ligação de entrada `DurableClient`. Usa o cliente para iniciar uma orquestração. Também pode ajudá-lo a devolver uma resposta HTTP contendo URLs para verificar o estado da nova orquestração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Para interagir com os orquestradores, a função deve incluir uma ligação de entrada `durableClient`.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Utilize `df.getClient` para obter um objeto `DurableOrchestrationClient`. Usa o cliente para iniciar uma orquestração. Também pode ajudá-lo a devolver uma resposta HTTP contendo URLs para verificar o estado da nova orquestração.

---

## <a name="run-the-sample"></a>Executar o exemplo

Para executar a orquestração `E1_HelloSequence`, envie o seguinte pedido HTTP POST para a função `HttpStart`.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> O snippet HTTP anterior pressupõe que existe uma entrada no ficheiro `host.json` que remove o prefixo `api/` prefixo padrão de todas as funções de gatilho HTTP URLs. Pode encontrar a marcação para esta configuração no ficheiro `host.json` nas amostras.

Por exemplo, se estiver a executar a amostra numa aplicação de função chamada "myfunctionapp", substitua "{host}" por "myfunctionapp.azurewebsites.net".

O resultado é uma resposta HTTP 202, como esta (aparada para brevidade):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste ponto, a orquestração está em fila e começa a funcionar imediatamente. O URL do cabeçalho `Location` pode ser utilizado para verificar o estado da execução.

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

Como pode ver, o `runtimeStatus` da instância está *concluído* e o `output` contém o resultado em série da JSON da execução da função orquestradora.

> [!NOTE]
> Pode implementar uma lógica de arranque semelhante para outros tipos de gatilhos, como `queueTrigger`, `eventHubTrigger`ou `timerTrigger`.

Olhe para os registos de execução da função. A função `E1_HelloSequence` começou e completou várias vezes devido ao comportamento de repetição descrito no tópico de fiabilidade da [orquestração.](durable-functions-orchestrations.md#reliability) Por outro lado, houve apenas três execuções de `E1_SayHello` uma vez que essas execuções de funções não são reproduzidas.

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou uma simples orquestração de corrente de função. A amostra seguinte mostra como implementar o padrão de ventilação/fã-in.

> [!div class="nextstepaction"]
> [Executar a amostra fan-out/fan-in](durable-functions-cloud-backup.md)

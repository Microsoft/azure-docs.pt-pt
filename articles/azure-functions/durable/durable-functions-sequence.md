---
title: Acorrentação de funções em funções duradouras - Azure
description: Aprenda a executar uma amostra de Funções Duradouras que executa uma sequência de funções.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: b117fca23b26919f3c404dd32ba64c0c89d66ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033569"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Acorrentação de funções em funções duradouras - Amostra de sequência de olá

Acorrentamento de funções refere-se ao padrão de execução de uma sequência de funções numa determinada ordem. Muitas vezes, a saída de uma função tem de ser aplicada à entrada de outra função. Este artigo descreve a sequência de acorrentação que cria quando completa o arranque rápido das Funções Duráveis[(C#](durable-functions-create-first-csharp.md) ou [JavaScript).](quickstart-js-vscode.md) Para obter mais informações sobre funções duradouras, consulte [a visão geral das funções duradouras](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E1_HelloSequence`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que chama `E1_SayHello` várias vezes numa sequência. Armazena as saídas das `E1_SayHello` chamadas e regista os resultados.
* `E1_SayHello`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que prepara uma corda com "Olá".
* `HttpStart`: Uma função HTTP desencadeada que inicia uma instância do orquestrador.

### <a name="e1_hellosequence-orchestrator-function"></a>função orquestrador E1_HelloSequence

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Todas as funções de orquestração C# devem ter um parâmetro de `DurableOrchestrationContext` tipo, que exista na `Microsoft.Azure.WebJobs.Extensions.DurableTask` montagem. Este objeto de contexto permite-lhe chamar outras funções de *atividade* e passar parâmetros de entrada usando o seu `CallActivityAsync` método.

O código chama `E1_SayHello` três vezes em sequência com diferentes valores de parâmetro. O valor de retorno de cada chamada é adicionado à `outputs` lista, que é devolvida no final da função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> As funções duradouras javaScript estão disponíveis apenas para as funções 2.0.

#### <a name="functionjson"></a>function.json

Se utilizar o Código do Estúdio Visual ou o portal Azure para o desenvolvimento, aqui está o conteúdo do *function.jsem* ficheiro para a função de orquestrador. A maioria dosfunction.jsde orquestra * em* ficheiros são quase exatamente assim.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

O importante é o `orchestrationTrigger` tipo de encadernação. Todas as funções do orquestrador devem utilizar este tipo de gatilho.

> [!WARNING]
> Para respeitar a regra "não I/O" das funções do orquestrador, não utilize nenhuma entrada ou encadernação de saída quando utilizar a ligação do `orchestrationTrigger` gatilho.  Se forem necessárias outras ligações de entrada ou saída, devem ser utilizadas no contexto das `activityTrigger` funções, que são chamadas pelo orquestrador. Para obter mais informações, consulte o artigo de restrições de [função do orquestrador.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js

Aqui está a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração JavaScript devem incluir o [ `durable-functions` módulo.](https://www.npmjs.com/package/durable-functions) É uma biblioteca que lhe permite escrever Funções Duradouras em JavaScript. Existem três diferenças significativas entre uma função de orquestração e outras funções JavaScript:

1. A função é uma [função geradora.](/scripting/javascript/advanced/iterators-and-generators-javascript). .
2. A função é envolta numa chamada para o `durable-functions` método do módulo `orchestrator` `df` (aqui).
3. A função deve ser sincronizada. Como o método "orquestrador" trata do chamado "context.done", a função deve simplesmente "regressar".

O `context` objeto contém um objeto de contexto de `df` orquestração durável que permite chamar outras funções de *atividade* e passar parâmetros de entrada usando o seu `callActivity` método. O código chama `E1_SayHello` três vezes em sequência com diferentes valores de parâmetro, utilizando `yield` para indicar que a execução deve aguardar as chamadas de função de atividade assíco a serem devolvidas. O valor de retorno de cada chamada é adicionado à `outputs` matriz, que é devolvida no final da função.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello função de atividade

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

As atividades usam o `ActivityTrigger` atributo. Utilize as medidas `IDurableActivityContext` fornecidas para realizar ações relacionadas com a atividade, tais como aceder ao valor de entrada utilizando `GetInput<T>` .

A implementação `E1_SayHello` de é uma operação de formatação de cordas relativamente trivial.

Em vez de se ligar a um `IDurableActivityContext` , pode ligar-se diretamente ao tipo que é passado para a função de atividade. Por exemplo:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsem

O *function.jsem* ficheiro para a função de atividade é semelhante ao da função de `E1_SayHello` `E1_HelloSequence` atividade, exceto que utiliza um tipo de `activityTrigger` encadernação em vez de um tipo de `orchestrationTrigger` ligação.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Qualquer função chamada por uma função de orquestração deve utilizar a `activityTrigger` ligação.

A implementação `E1_SayHello` de é uma operação de formatação de cordas relativamente trivial.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário de uma função de orquestração JavaScript, uma função de atividade não necessita de configuração especial. A entrada que lhe foi transmitida pela função do orquestrador está localizada no `context.bindings` objeto sob o nome da `activityTrigger` encadernação - neste caso, `context.bindings.name` . O nome de ligação pode ser definido como um parâmetro da função exportada e acedido diretamente, que é o que o código de amostra faz.

---

### <a name="httpstart-client-function"></a>Função do cliente HttpStart

Pode iniciar uma função orquestradora utilizando uma função de cliente. Utilizará a `HttpStart` função httpizada para iniciar casos de `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Para interagir com os orquestradores, a função deve incluir uma `DurableClient` ligação de entrada. Usas o cliente para iniciar uma orquestração. Também pode ajudá-lo a devolver uma resposta HTTP contendo URLs para verificar o estado da nova orquestração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsem

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Para interagir com os orquestradores, a função deve incluir uma `durableClient` ligação de entrada.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Use `df.getClient` para obter um `DurableOrchestrationClient` objeto. Usas o cliente para iniciar uma orquestração. Também pode ajudá-lo a devolver uma resposta HTTP contendo URLs para verificar o estado da nova orquestração.

---

## <a name="run-the-sample"></a>Executar o exemplo

Para executar a `E1_HelloSequence` orquestração, envie o seguinte pedido HTTP POST para a `HttpStart` função.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> O snippet HTTP anterior pressupõe que existe uma entrada no `host.json` ficheiro que remove o `api/` prefixo prefixo precável de todas as funções de gatilho HTTP URLs. Pode encontrar a marcação para esta configuração no `host.json` ficheiro nas amostras.

Por exemplo, se estiver a executar a amostra numa aplicação de função chamada "myfunctionapp", substitua "{host}" por "myfunctionapp.azurewebsites.net".

O resultado é uma resposta HTTP 202, como esta (aparada para a brevidade):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste ponto, a orquestração é em fila e começa a correr imediatamente. O URL no `Location` cabeçalho pode ser usado para verificar o estado da execução.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

O resultado é o estatuto da orquestração. Ele corre e completa rapidamente, por isso vê-lo no estado *concluído* com uma resposta que se parece com esta (aparada para a brevidade):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como pode ver, `runtimeStatus` o da instância é *concluído* e contém o `output` resultado serializado JSON da execução da função orquestradora.

> [!NOTE]
> Pode implementar lógica de arranque semelhante para outros tipos de gatilho, como `queueTrigger` `eventHubTrigger` , ou `timerTrigger` .

Olhe para os registos de execução de funções. A `E1_HelloSequence` função começou e completou várias vezes devido ao comportamento de repetição descrito no tópico de fiabilidade da [orquestração.](durable-functions-orchestrations.md#reliability) Por outro lado, houve apenas três `E1_SayHello` execuções, uma vez que essas execuções de funções não são repetidas.

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou uma simples orquestração de acorrentação de funções. A próxima amostra mostra como implementar o padrão de fan-out/fan-in.

> [!div class="nextstepaction"]
> [Executar a amostra Fan-out/fan-in](durable-functions-cloud-backup.md)

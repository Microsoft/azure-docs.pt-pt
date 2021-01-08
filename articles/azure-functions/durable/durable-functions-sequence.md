---
title: Acorrentação de funções em funções duradouras - Azure
description: Aprenda a executar uma amostra de Funções Duradouras que executa uma sequência de funções.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028255"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Acorrentação de funções em funções duradouras - Amostra de sequência de olá

Acorrentamento de funções refere-se ao padrão de execução de uma sequência de funções numa determinada ordem. Muitas vezes, a saída de uma função tem de ser aplicada à entrada de outra função. Este artigo descreve a sequência de acorrentação que cria quando completa o quickstart[(C#](durable-functions-create-first-csharp.md), [JavaScript](quickstart-js-vscode.md), ou [Python).](quickstart-python-vscode.md) Para obter mais informações sobre funções duradouras, consulte [a visão geral das funções duradouras](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E1_HelloSequence`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que chama `E1_SayHello` várias vezes numa sequência. Armazena as saídas das `E1_SayHello` chamadas e regista os resultados.
* `E1_SayHello`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que prepara uma corda com "Olá".
* `HttpStart`: Uma função [de cliente durável](durable-functions-bindings.md#orchestration-client) de HTTP que inicia uma instância do orquestrador.

### <a name="e1_hellosequence-orchestrator-function"></a>função orquestrador E1_HelloSequence

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Todas as funções de orquestração C# devem ter um parâmetro de `DurableOrchestrationContext` tipo, que exista na `Microsoft.Azure.WebJobs.Extensions.DurableTask` montagem. Este objeto de contexto permite-lhe chamar outras funções de *atividade* e passar parâmetros de entrada usando o seu `CallActivityAsync` método.

O código chama `E1_SayHello` três vezes em sequência com diferentes valores de parâmetro. O valor de retorno de cada chamada é adicionado à `outputs` lista, que é devolvida no final da função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> As funções duradouras javaScript estão disponíveis apenas para as funções 3.0.

#### <a name="functionjson"></a>function.json

Se utilizar o Código do Estúdio Visual ou o portal Azure para o desenvolvimento, aqui está o conteúdo do *function.jsem* ficheiro para a função de orquestrador. A maioria dosfunction.jsde orquestra *em* ficheiros são quase exatamente assim.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

O importante é o `orchestrationTrigger` tipo de encadernação. Todas as funções do orquestrador devem utilizar este tipo de gatilho.

> [!WARNING]
> Para respeitar a regra "não I/O" das funções do orquestrador, não utilize nenhuma entrada ou encadernação de saída quando utilizar a ligação do `orchestrationTrigger` gatilho.  Se forem necessárias outras ligações de entrada ou saída, devem ser utilizadas no contexto das `activityTrigger` funções, que são chamadas pelo orquestrador. Para obter mais informações, consulte o artigo de restrições de [função do orquestrador.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js

Aqui está a função orquestradora:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração JavaScript devem incluir o [ `durable-functions` módulo.](https://www.npmjs.com/package/durable-functions) É uma biblioteca que lhe permite escrever Funções Duradouras em JavaScript. Existem três diferenças significativas entre uma função orquestradora e outras funções JavaScript:

1. A função orquestradora é uma [função geradora.](/scripting/javascript/advanced/iterators-and-generators-javascript)
2. A função é envolta numa chamada para o `durable-functions` método do módulo `orchestrator` `df` (aqui).
3. A função deve ser sincronizada. Como o método "orquestrador" trata do chamado "context.done", a função deve simplesmente "regressar".

O `context` objeto contém um objeto de contexto de `df` orquestração durável que permite chamar outras funções de *atividade* e passar parâmetros de entrada usando o seu `callActivity` método. O código chama `E1_SayHello` três vezes em sequência com diferentes valores de parâmetro, utilizando `yield` para indicar que a execução deve aguardar as chamadas de função de atividade assíco a serem devolvidas. O valor de retorno de cada chamada é adicionado à `outputs` matriz, que é devolvida no final da função.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> As Funções Duradouras python estão disponíveis apenas para as funções 3.0.


#### <a name="functionjson"></a>function.json

Se utilizar o Código do Estúdio Visual ou o portal Azure para o desenvolvimento, aqui está o conteúdo do *function.jsem* ficheiro para a função de orquestrador. A maioria dosfunction.jsde orquestra *em* ficheiros são quase exatamente assim.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

O importante é o `orchestrationTrigger` tipo de encadernação. Todas as funções do orquestrador devem utilizar este tipo de gatilho.

> [!WARNING]
> Para respeitar a regra "não I/O" das funções do orquestrador, não utilize nenhuma entrada ou encadernação de saída quando utilizar a ligação do `orchestrationTrigger` gatilho.  Se forem necessárias outras ligações de entrada ou saída, devem ser utilizadas no contexto das `activityTrigger` funções, que são chamadas pelo orquestrador. Para obter mais informações, consulte o artigo de restrições de [função do orquestrador.](durable-functions-code-constraints.md)

#### <a name="__init__py"></a>\_\_.py \_ init \_

Aqui está a função orquestradora:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Todas as funções de orquestração Python devem incluir o [ `durable-functions` pacote.](https://pypi.org/project/azure-functions-durable) É uma biblioteca que lhe permite escrever Funções Duradouras em Python. Existem duas diferenças significativas entre uma função orquestradora e outras funções python:

1. A função orquestradora é uma [função geradora.](https://wiki.python.org/moin/Generators)
2. O _ficheiro_ deve registar a função de orquestrador, indicando `main = df.Orchestrator.create(<orchestrator function name>)` no final do processo. Isto ajuda a distingui-lo de outras funções, ajudantes, declaradas no ficheiro.

O `context` objeto permite-lhe chamar outras funções de *atividade* e passar parâmetros de entrada usando o seu `call_activity` método. O código chama `E1_SayHello` três vezes em sequência com diferentes valores de parâmetro, utilizando `yield` para indicar que a execução deve aguardar as chamadas de função de atividade assíco a serem devolvidas. O valor de retorno de cada chamada é devolvido no final da função.

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
> Todas as funções de atividade chamadas por uma função de orquestração devem utilizar a `activityTrigger` ligação.

A implementação `E1_SayHello` de é uma operação de formatação de cordas relativamente trivial.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário da função de orquestração, uma função de atividade não precisa de uma configuração especial. A entrada que lhe foi transmitida pela função do orquestrador está localizada no `context.bindings` objeto sob o nome da `activityTrigger` encadernação - neste caso, `context.bindings.name` . O nome de ligação pode ser definido como um parâmetro da função exportada e acedido diretamente, que é o que o código de amostra faz.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsem

O *function.jsem* ficheiro para a função de atividade é semelhante ao da função de `E1_SayHello` `E1_HelloSequence` atividade, exceto que utiliza um tipo de `activityTrigger` encadernação em vez de um tipo de `orchestrationTrigger` ligação.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> Todas as funções de atividade chamadas por uma função de orquestração devem utilizar a `activityTrigger` ligação.

A implementação `E1_SayHello` de é uma operação de formatação de cordas relativamente trivial.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ \_ \_ init .py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

Ao contrário da função orquestradora, uma função de atividade não necessita de uma configuração especial. A entrada que lhe foi transmitida pela função orquestradora é diretamente acessível como parâmetro para a função.

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

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsem

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

Para interagir com os orquestradores, a função deve incluir uma `durableClient` ligação de entrada.

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ .py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

Utilize o `DurableOrchestrationClient` construtor para obter um cliente De Funções Duradouras. Usas o cliente para iniciar uma orquestração. Também pode ajudá-lo a devolver uma resposta HTTP contendo URLs para verificar o estado da nova orquestração.

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

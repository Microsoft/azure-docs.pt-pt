---
title: Função de encadeamento de funções duráveis - Azure
description: Saiba como executar um exemplo de funções duráveis que executa uma sequência de funções.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4657bd136592c66b5dab9a712f5f1d6df898876c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730549"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Função de encadeamento de funções duráveis - exemplo de sequência de Hello

Encadeamento de função refere-se para o padrão de uma seqüência de funções em execução numa determinada ordem. Muitas vezes, a saída de uma função tem de ser aplicadas para a entrada de outra função. Este artigo descreve a sequência de encadeamento que criou quando conclui o guia de introdução de funções durável ([ C# ](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md)). Para obter mais informações sobre as funções durável, consulte [padrões de funções duráveis e conceitos técnicos](durable-functions-concepts.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação de exemplo:

* `E1_HelloSequence`: Uma função de orquestrador que chama `E1_SayHello` várias vezes numa seqüência. Ele armazena as saídas resultantes o `E1_SayHello` chama e regista os resultados.
* `E1_SayHello`: Uma função de atividade que acrescenta uma cadeia de caracteres com "Olá".

As secções seguintes explicam a configuração e o código que são utilizados para c# scripts e JavaScript. O código para o desenvolvimento do Visual Studio é mostrado no final do artigo.

> [!NOTE]
> Funções duráveis do JavaScript estão disponíveis para o runtime de 2.x das funções apenas.

## <a name="e1hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>ficheiro de Function

Se utilizar o Visual Studio Code ou o portal do Azure para desenvolvimento, aqui está o conteúdo do *Function* ficheiro para a função de orquestrador. A maioria dos orchestrator *Function* ficheiros quase exatamente este aspeto.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

O importante é o `orchestrationTrigger` tipo de enlace. Todas as funções do orchestrator tem de utilizar este tipo de Acionador.

> [!WARNING]
> Para cumprir a regra "nenhuma e/s" das funções do orchestrator, não utilize qualquer entrada ou enlaces de saída, ao utilizar o `orchestrationTrigger` acionar a ligação.  Se outro de entrada ou enlaces de saída são necessários, em vez disso, devem ser utilizadas no contexto de `activityTrigger` funções, o que são chamadas pelo orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Script c# (código de exemplo do portal Visual Studio Code e o Azure)

Eis o código-fonte:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Todas as funções de orquestração da linguagem c# tem de ter um parâmetro do tipo `DurableOrchestrationContext`, que existe no `Microsoft.Azure.WebJobs.Extensions.DurableTask` assembly. Se estiver a utilizar o script c#, o assembly pode ser referenciado através da `#r` notação. Este objeto de contexto permite que chamar outro *atividade* as funções e os parâmetros de entrada pass com seu [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) método.

O código chama `E1_SayHello` três vezes numa sequência com valores de parâmetros diferentes. O valor de cada chamada de retorno é adicionado para o `outputs` lista, que é devolvida no final da função.

### <a name="javascript"></a>Javascript

Eis o código-fonte:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração de JavaScript têm de incluir o [ `durable-functions` módulo](https://www.npmjs.com/package/durable-functions). Esta é uma biblioteca que permite escrever funções duráveis em JavaScript. Existem três diferenças significativas entre uma função de orquestração e outras funções de JavaScript:

1. A função é um [função gerador.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. A função é encapsulada numa chamada para o `durable-functions` do módulo `orchestrator` método (aqui `df`).
3. A função tem de ser síncrona. Uma vez que o método "orchestrator" processa a chamada "context.done', a função deve simplesmente 'return'.

O `context` objeto contém um `df` objeto permite que chame outro *atividade* funções e os parâmetros de entrada pass com seu `callActivity` método. O código chama `E1_SayHello` três vezes numa sequência com valores de parâmetros diferentes, utilizando `yield` para indicar a execução deve aguardar nas chamadas de função de atividade assíncrona a serem retornados. O valor de cada chamada de retorno é adicionado para o `outputs` lista, que é devolvida no final da função.

## <a name="e1sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>ficheiro de Function

O *Function* ficheiro para a função de atividade `E1_SayHello` é semelhante da `E1_HelloSequence` exceto que ele usa um `activityTrigger` enlace de tipo, em vez de um `orchestrationTrigger` tipo de enlace.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Qualquer função chamada por uma função de orquestração tem de utilizar o `activityTrigger` enlace.

A implementação de `E1_SayHello` é uma operação de formatação de cadeia de caracteres relativamente trivial.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Esta função tem um parâmetro do tipo [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), que utiliza para obter a entrada que foi passada a ele por chamada da função de orquestrador a [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário de uma função de orquestração de JavaScript, uma função de atividade não precisa nenhuma configuração especial. A entrada transmitida a ele pela função orchestrator está localizada no `context.bindings` objeto no nome da `activityTrigger` vinculando - neste caso, `context.bindings.name`. O nome da ligação pode ser definido como um parâmetro da função exportado e acedidos diretamente, que é o que faz o código de exemplo.

## <a name="run-the-sample"></a>Executar o exemplo

Para executar o `E1_HelloSequence` orquestração, o envio do pedido HTTP POST seguinte.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> O trecho de código anterior do HTTP pressupõe que existe uma entrada no `host.json` ficheiros que remove a predefinição `api/` prefixo a partir de todos os URLs de funções de Acionador HTTP. Pode encontrar a marcação para esta configuração no `host.json` ficheiro nos exemplos.

Por exemplo, se estiver a executar o exemplo de uma aplicação de funções com o nome "myfunctionapp", substitua "{host}" com "myfunctionapp.azurewebsites.net".

O resultado é uma resposta de HTTP 202, como este (cortados para fins de brevidade):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste momento, a orquestração é na fila e começa a ser executado imediatamente. O URL no `Location` cabeçalho pode ser usado para verificar o estado da execução.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

O resultado é o estado da orquestração. Ele é executado e é concluída rapidamente, para que vê-lo na *concluído* Estado com uma resposta como esta (cortados para fins de brevidade):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como pode ver, o `runtimeStatus` da instância está *Completed* e o `output` contém o resultado JSON serializado da execução de função do orchestrator.

> [!NOTE]
> O ponto final de HTTP POST que iniciou a função de orquestrador é implementado na aplicação de exemplo como um HTTP acionar a função com o nome "HttpStart". Pode implementar a lógica de arranque semelhantes para outros tipos de Acionador, como `queueTrigger`, `eventHubTrigger`, ou `timerTrigger`.

Consulte os registos de execução de função. O `E1_HelloSequence` função iniciados e concluídos várias vezes devido ao comportamento de repetição descrito o [descrição geral](durable-functions-concepts.md). Por outro lado, houve apenas três execuções de `E1_SayHello` , uma vez que essas execuções de função não obter repetidas.

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Eis a orquestração como um único arquivo c# num projeto do Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Passos Seguintes

Este exemplo tenha demonstrado uma orquestração simple de encadeamento de função. O exemplo seguinte mostra como implementar o padrão fan-out/fan-in.

> [!div class="nextstepaction"]
> [Executar o exemplo Fan-out/fan-in](durable-functions-cloud-backup.md)

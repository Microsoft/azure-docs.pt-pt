---
title: Encadeamento de funções no Durable Functions-Azure
description: Saiba como executar um exemplo de Durable Functions que executa uma sequência de funções.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee5b18ddc734335ddac2a7d3352de0e4388f445d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933261"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Encadeamento de funções no exemplo de sequência de Durable Functions de saudação

O encadeamento de funções refere-se ao padrão de executar uma sequência de funções em uma ordem específica. Geralmente, a saída de uma função precisa ser aplicada à entrada de outra função. Este artigo descreve a sequência de encadeamento que você cria ao concluir o Durable Functions início rápido ([C#](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md)). Para obter mais informações sobre Durable Functions, consulte [Durable Functions visão geral](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E1_HelloSequence`: Uma função de orquestrador que `E1_SayHello` chama várias vezes em uma sequência. Ele armazena as saídas das `E1_SayHello` chamadas e registra os resultados.
* `E1_SayHello`: Uma função de atividade que precede uma cadeia de caracteres com "Olá".

As seções a seguir explicam a configuração e o código que C# são usados para scripts e JavaScript. O código para desenvolvimento do Visual Studio é mostrado no final do artigo.

> [!NOTE]
> Durable Functions de JavaScript estão disponíveis apenas para o tempo de execução do Functions 2. x.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>arquivo function. JSON

Se você usar Visual Studio Code ou o portal do Azure para desenvolvimento, aqui está o conteúdo do arquivo *Function. JSON* para a função de orquestrador. A maioria dos arquivos *. JSON da função* de orquestrador parece quase exatamente assim.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

O importante é o tipo `orchestrationTrigger` de associação. Todas as funções de orquestrador devem usar esse tipo de gatilho.

> [!WARNING]
> Para obedecer à regra "sem e/s" das funções de orquestrador, não use nenhuma associação de entrada ou saída ao usar a `orchestrationTrigger` Associação de gatilho.  Se outras associações de entrada ou saída forem necessárias, elas deverão ser usadas no contexto de `activityTrigger` funções, que são chamadas pelo orquestrador.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#script (Visual Studio Code e portal do Azure código de exemplo)

Este é o código-fonte:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Todas C# as funções de orquestração devem ter um parâmetro `DurableOrchestrationContext`do tipo, `Microsoft.Azure.WebJobs.Extensions.DurableTask` que existe no assembly. Se você estiver usando C# o script, o assembly poderá ser referenciado `#r` usando a notação. Esse objeto de contexto permite chamar outras funções de *atividade* e passar parâmetros de entrada usando seu método [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) .

O código chama `E1_SayHello` três vezes em sequência com valores de parâmetro diferentes. O valor de retorno de cada chamada é adicionado à `outputs` lista, que é retornada ao final da função.

### <a name="javascript"></a>Javascript

Este é o código-fonte:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração do JavaScript devem incluir o [ `durable-functions` módulo](https://www.npmjs.com/package/durable-functions). Essa é uma biblioteca que permite que você escreva Durable Functions em JavaScript. Há três diferenças significativas entre uma função de orquestração e outras funções JavaScript:

1. A função é uma [função de gerador.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. A função é encapsulada em uma chamada para `durable-functions` o método `orchestrator` do módulo ( `df`aqui).
3. A função deve ser síncrona. Como o método ' Orchestrator ' trata da chamada de ' Context. done ', a função deve simplesmente ' Return '.

O `context` objeto contém um `df` objeto que permite chamar outras funções de *atividade* e passar parâmetros de entrada `callActivity` usando seu método. O código chama `E1_SayHello` três vezes em sequência com valores de parâmetro diferentes, `yield` usando para indicar que a execução deve esperar que as chamadas de função de atividade assíncronas sejam retornadas. O valor de retorno de cada chamada é adicionado à `outputs` lista, que é retornada ao final da função.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>arquivo function. JSON

O arquivo *Function. JSON* para a `E1_SayHello` função de atividade é semelhante ao de `E1_HelloSequence` , exceto pelo fato de que ele usa um `activityTrigger` tipo `orchestrationTrigger` de associação em vez de um tipo de associação.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Qualquer função chamada por uma função de orquestração deve usar `activityTrigger` a associação.

A implementação de `E1_SayHello` é uma operação de formatação de cadeia de caracteres relativamente trivial.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Essa função tem um parâmetro do tipo [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), que ele usa para obter a entrada passada para ela pela chamada da função de orquestrador para [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário de uma função de orquestração de JavaScript, uma função de atividade não precisa de nenhuma configuração especial. A entrada passada para ela pela função de orquestrador está localizada no `context.bindings` objeto sob o nome `activityTrigger` da Associação – nesse caso, `context.bindings.name`. O nome da associação pode ser definido como um parâmetro da função exportada e acessado diretamente, que é o que o código de exemplo faz.

## <a name="run-the-sample"></a>Executar o exemplo

Para executar a `E1_HelloSequence` orquestração, envie a seguinte solicitação HTTP post.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> O trecho http anterior pressupõe que há uma entrada no `host.json` arquivo que remove o prefixo padrão `api/` de todas as URLs de funções de gatilho http. Você pode encontrar a marcação para essa configuração no `host.json` arquivo nos exemplos.

Por exemplo, se você estiver executando o exemplo em um aplicativo de funções chamado "myfunctionapp", substitua "{host}" por "myfunctionapp.azurewebsites.net".

O resultado é uma resposta HTTP 202, como esta (cortada para fins de brevidade):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste ponto, a orquestração é enfileirada e começa a ser executada imediatamente. A URL no `Location` cabeçalho pode ser usada para verificar o status da execução.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

O resultado é o status da orquestração. Ele é executado e concluído rapidamente, portanto, você o vê no estado *concluído* com uma resposta semelhante a esta (cortada para fins de brevidade):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como você pode ver, o `runtimeStatus` da instância é `output` *concluído* e contém o resultado serializado em JSON da execução da função de orquestrador.

> [!NOTE]
> O ponto de extremidade HTTP POST que iniciou a função de orquestrador é implementado no aplicativo de exemplo como uma função de gatilho HTTP chamada "HttpStart". Você pode implementar uma lógica inicial semelhante para outros tipos de gatilho, `queueTrigger`como `eventHubTrigger`, ou `timerTrigger`.

Examine os logs de execução da função. A `E1_HelloSequence` função foi iniciada e concluída várias vezes devido ao comportamento de reprodução descrito no tópico de [confiabilidade da orquestração](durable-functions-orchestrations.md#reliability) . Por outro lado, havia apenas três execuções de, uma `E1_SayHello` vez que essas execuções de função não são reproduzidas.

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Aqui está a orquestração como um único C# arquivo em um projeto do Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Passos seguintes

Este exemplo demonstrou uma orquestração de encadeamento de funções simples. O exemplo a seguir mostra como implementar o padrão Fan-out/Fan-in.

> [!div class="nextstepaction"]
> [Executar o exemplo de Fan-out/Fan-in](durable-functions-cloud-backup.md)

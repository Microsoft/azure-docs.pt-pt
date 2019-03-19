---
title: Pontos de verificação e repetição nas funções duráveis - Azure
description: Saiba como pontos de verificação e resposta funciona na extensão de funções duráveis para as funções do Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9edcc313a9e88b657337ba631218388f70d4b41f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086756"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Pontos de verificação e repetição nas funções durável (funções do Azure)

Um dos principais atributos de funções durável é **uma execução fiável**. Funções do Orchestrator e as funções de atividade poderão estar em execução em diferentes VMs dentro de um centro de dados, e essas VMs ou a infraestrutura de rede subjacente não é 100% de fiabilidade.

Apesar disso, as funções duráveis garante uma execução fiável dos orquestrações. Isso é feito ao utilizar as filas de armazenamento para a invocação de função de unidade e por periodicamente histórico de execução do ponto de verificação em tabelas de armazenamento (usando um padrão de conceção de nuvem conhecido como [origem do evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Esse histórico, em seguida, pode ser reproduzido para reconstruir automaticamente o estado de dentro da memória de uma função de orquestrador.

## <a name="orchestration-history"></a>Histórico de orquestração

Suponha que tenha a seguinte função do orchestrator:

### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

Em cada `await` (c#) ou `yield` instrução (JavaScript), os pontos de verificação do Framework de tarefa durável o estado de execução da função para o armazenamento de tabela. Este estado é o que é conhecido como o *histórico de orquestração*.

## <a name="history-table"></a>Tabela de histórico

Em termos gerais, a estrutura de tarefa durável faz o seguinte em cada ponto de verificação:

1. Guarda o histórico de execução para tabelas de armazenamento do Azure.
2. Coloca em fila mensagens para as funções do orchestrator deseja invocar.
3. Coloca em fila mensagens para o orchestrator próprio &mdash; por exemplo, as mensagens de temporizador durável.

Quando o ponto de verificação estiver concluído, a função de orquestrador é gratuita a serem removidos da memória até que haja mais de trabalho para executar.

> [!NOTE]
> O armazenamento do Azure não fornece quaisquer garantias transacionais entre a guardar dados no armazenamento de tabelas e filas. Para lidar com falhas, utiliza o fornecedor de armazenamento de funções duráveis *consistência eventual* padrões. Estes padrões Certifique-se de que nenhum dado seja perdido se ocorrer uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, o histórico da função mostrado anteriormente um aspeto semelhante ao seguinte no armazenamento de tabelas do Azure (abreviado para fins de ilustração):

| PartitionKey (InstanceId)                     | EventType             | Carimbo de data/hora               | Input | Name             | Resultado                                                    | Estado |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | nulo  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Hello Tóquio!" ""                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Olá, Seattle!" ""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Hello Londres!" ""                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Concluído           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Algumas observações sobre os valores da coluna:

* **PartitionKey**: Contém o ID de instância da orquestração.
* **EventType**: Representa o tipo do evento. Pode ser um dos seguintes tipos:
  * **OrchestrationStarted**: A função de orquestrador retomado a partir de uma expressão await, ou está a ser executado pela primeira vez. O `Timestamp` coluna é utilizada para preencher o valor determinista para o [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API.
  * **ExecutionStarted**: A função de orquestrador iniciadas pela primeira vez. Este evento também contém a entrada de função no `Input` coluna.
  * **TaskScheduled**: Uma função de atividade foi agendada. O nome da função de atividade é capturado no `Name` coluna.
  * **TaskCompleted**: Uma função de atividade foi concluída. O resultado da função está no `Result` coluna.
  * **TimerCreated**: Um temporizador durável foi criado. O `FireAt` coluna contém a hora agendada de UTC em que o timer expira.
  * **TimerFired**: Um temporizador durável disparado.
  * **EventRaised**: Um evento externo foi enviado para a instância de orquestração. O `Name` coluna captura o nome do evento e o `Input` coluna captura o payload do evento.
  * **OrchestratorCompleted**: A função de orquestrador aguardada.
  * **ContinueAsNew**: A função de orquestrador concluída e reiniciado em si com o novo Estado. O `Result` coluna contém o valor, que é utilizado como entrada na instância reiniciada.
  * **ExecutionCompleted**: A função de orquestrador foi executado para conclusão (ou falhadas). As saídas de função ou os detalhes do erro são armazenadas no `Result` coluna.
* **Timestamp**: O carimbo de hora UTC do evento de histórico.
* **Nome**: O nome da função que foi invocado.
* **Entrada**: A entrada da função formatada em JSON.
* **Resultado**: A saída da função; ou seja, seu valor de retorno.

> [!WARNING]
> Embora seja útil como uma ferramenta de depuração, não se qualquer dependência nesta tabela. Eles podem mudar à medida que a extensão de funções duráveis evolui.

Sempre que a função sai do modo de um `await` (C#) ou `yield` (JavaScript), a estrutura de tarefa durável volta executar a função de orquestrador do zero. Em cada voltar a executar que ele consulta o histórico de execução para determinar se a operação assíncrona atual apresentou a colocar.  Se ocorreu a operação, a estrutura replays a saída dessa operação imediatamente e passa para o próximo `await` (C#) ou `yield` (JavaScript). Este processo continua até que o histórico completo tem sido repetido, altura em que todas as variáveis locais da função de orquestrador são restauradas para os valores anteriores.

## <a name="orchestrator-code-constraints"></a>Restrições de código do Orchestrator

O comportamento de repetição cria restrições no tipo de código que pode ser escrito numa função de orquestrador:

* Código do Orchestrator tem de ser **determinística**. Ele vai ser repetido várias vezes e deve produzir o mesmo resultado cada vez. Por exemplo, não direct chama-se para obter a data/hora atual, obter números aleatórios, gerar GUIDs aleatórios ou aceder a pontos de extremidade remotos.

  Se precisar de código do orchestrator obter a data/hora atual, deve utilizar o [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) ou `currentUtcDateTime` (JavaScript) API, que é seguro para repetição.

  Se precisar de código do orchestrator gerar um GUID aleatório, deve utilizar o [Novo_guid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) API (.NET), que é seguro para reprodução ou a geração de GUID de delegado para uma função de atividade (JavaScript), como neste exemplo:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  Operações não-determinística devem ser feitas nas funções de atividade. Isto inclui qualquer interação com as outras ligações de entrada ou de saída. Isto garante que quaisquer valores determinística serão gerados uma vez na primeira execução e guardados no histórico de execução. As execuções subsequentes, em seguida, utilizará o valor guardado automaticamente.

* O código do Orchestrator deve estar **sem bloqueio**. Por exemplo, significa que nenhuma e/s e nenhuma chamada para `Thread.Sleep` (.NET) ou APIs equivalentes.

  Se precisar de um orquestrador de atraso, pode utilizar o [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.NET) ou `createTimer` (JavaScript) API.

* Tem de código do Orchestrator **nunca iniciar qualquer operação de async** exceto utilizando o [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API ou `context.df` API do objeto. Por exemplo, não `Task.Run`, `Task.Delay` ou `HttpClient.SendAsync` no .NET, ou `setTimeout()` e `setInterval()` em JavaScript. A estrutura de tarefa durável executa o código do orchestrator num único thread e não pode interagir com outros threads que poderiam ser agendados por outra APIs de async.

* **Devem ser evitados loops infinitos** no código do orchestrator. Uma vez que a estrutura de tarefa durável salva o histórico de execução conforme o andamento da função de orquestração, um loop infinito poderia fazer com que uma instância do orchestrator a ficar sem memória. Para cenários de loop infinito, utilize as APIs, como [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) ou `continueAsNew` (JavaScript) para reiniciar a execução de função e eliminar histórico de execução anterior.

* Funções de orchestrator de JavaScript não podem ser `async`. Tem de ser declaradas como funções de gerador síncrona.

Embora essas restrições podem parecer assustadora em primeiro lugar, na prática que eles não são difíceis de acompanhar. A estrutura de tarefa durável tenta detetar violações das regras acima e lança um `NonDeterministicOrchestrationException`. No entanto, esse comportamento de deteção é melhor esforço e que não deve confiar nele.

> [!NOTE]
> Todas estas regras aplicam-se apenas a funções acionadas pelo `orchestrationTrigger` enlace. Funções de atividade acionados pela `activityTrigger` associação e funções que utilizam o `orchestrationClient` não vinculação, ter nenhuma dessas limitações.

## <a name="durable-tasks-net"></a>Tarefas duráveis (.NET)

> [!NOTE]
> Esta secção descreve os detalhes de implementação interna do Framework tarefas durável. Pode utilizar funções duráveis sem conhecer estas informações. Destina-se apenas para o ajudar a compreender o comportamento de repetição.

Tarefas que podem ser aguardadas com segurança nas funções do orchestrator são ocasionalmente designadas *tarefas duráveis*. Estas são tarefas que são criadas e geridas pela estrutura de tarefa durável. Os exemplos são tarefas retornados pelos `CallActivityAsync`, `WaitForExternalEvent`, e `CreateTimer`.

Estes *tarefas duráveis* internamente são geridos com uma lista de `TaskCompletionSource` objetos. Durante a reprodução, estas tarefas são criadas como parte da execução de código do orchestrator e são concluídas, como o dispatcher enumera os eventos de histórico correspondente. Tudo isso é feito sincronicamente usando um único thread até que todo o histórico foi reproduzido. Quaisquer tarefas duráveis, o que não estão concluídas no final de repetição de histórico tem levadas a cabo as ações apropriadas. Por exemplo, uma mensagem pode ser colocados em fila para chamar uma função de atividade.

O comportamento de execução descrito aqui deve ajudá-lo a compreender por que o código de função do orchestrator tem nunca `await` uma tarefa não duradoura: o thread da dispatcher não puder aguardar pela conclusão da mesma e qualquer retorno de chamada por essa tarefa poderia corromper o rastreio Estado da função de orquestrador. Algumas verificações de tempo de execução estão em vigor para experimentar evitar esta situação.

Se desejar obter mais informações sobre como o Framework de tarefa durável executa as funções do orchestrator, a melhor coisa a fazer é consultar o [código-fonte no GitHub tarefas durável](https://github.com/Azure/durabletask). Em particular, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o gerenciamento de instância](durable-functions-instance-management.md)

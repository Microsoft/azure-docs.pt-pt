---
title: Pontos de verificação e reprodução no Durable Functions – Azure
description: Saiba como o ponto de verificação e a resposta funcionam na extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1e6d3b78887c9d195fdf0137553860c141bdaaba
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241049"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Pontos de verificação e reprodução em Durable Functions (Azure Functions)

Um dos principais atributos de Durable Functions é a **execução confiável**. Funções de orquestrador e funções de atividade podem ser executadas em VMs diferentes dentro de um data center, e essas VMs ou a infraestrutura de rede subjacente não é de 100% confiável.

Apesar disso, Durable Functions garante a execução confiável de orquestrações. Ele faz isso usando filas de armazenamento para orientar a invocação de função e periodicamente verificando o histórico de execução em tabelas de armazenamento (usando um padrão de design de nuvem conhecido como [fornecimento de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Esse histórico pode ser reproduzido para recriar automaticamente o estado na memória de uma função de orquestrador.

## <a name="orchestration-history"></a>Histórico de orquestração

Suponha que você tenha a seguinte função de orquestrador:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

Em cada `await` instruçãoC#() `yield` ou (JavaScript), o Framework de tarefa durável verifica o estado de execução da função no armazenamento de tabela. Esse estado é o que é conhecido como o *histórico*de orquestração.

## <a name="history-table"></a>Tabela de histórico

Em termos gerais, o Framework de tarefa durável faz o seguinte em cada ponto de verificação:

1. Salva o histórico de execução em tabelas do armazenamento do Azure.
2. Enfileira mensagens para funções que o orquestrador deseja invocar.
3. Enfileira mensagens para o orquestrador em si &mdash; , por exemplo, mensagens de temporizador duráveis.

Após a conclusão do ponto de verificação, a função de orquestrador é livre para ser removida da memória até que haja mais trabalho a ser feito.

> [!NOTE]
> O armazenamento do Azure não fornece nenhuma garantia transacional entre salvar dados em filas e armazenamento de tabelas. Para lidar com falhas, o provedor de armazenamento Durable Functions usa padrões de *consistência eventual* . Esses padrões garantem que nenhum dado seja perdido se houver uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, o histórico da função mostrado anteriormente é semelhante ao seguinte no armazenamento de tabelas do Azure (abreviado para fins de ilustração):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Input | Name             | Resultado                                                    | State |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | nulo  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" Hello Tokyo! "" "                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" Olá, Seattle! ""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" Hello London! "" "                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[" "Olá Tokyo!" "," "Olá, Seattle!" "," "Olá, Londres!" "]" | Concluído           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Algumas observações sobre os valores da coluna:

* **PartitionKey**: Contém a ID da instância da orquestração.
* **EventType**: Representa o tipo do evento. Pode ser um dos seguintes tipos:
  * **OrchestrationStarted**: A função de orquestrador voltou de um Await ou está em execução pela primeira vez. A `Timestamp` coluna é usada para preencher o valor determinístico para a API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) .
  * **ExecutionStarted**: A função de orquestrador começou a ser executada pela primeira vez. Esse evento também contém a entrada da função na `Input` coluna.
  * **TaskScheduled**: Uma função de atividade foi agendada. O nome da função de atividade é capturado na `Name` coluna.
  * **TaskCompleted**: Uma função de atividade foi concluída. O resultado da função está na `Result` coluna.
  * **TimerCreated**: Um temporizador durável foi criado. A `FireAt` coluna contém a hora UTC agendada na qual o temporizador expira.
  * **TimerFired**: Um temporizador durável foi acionado.
  * **EventRaised**: Um evento externo foi enviado para a instância de orquestração. A `Name` coluna captura o nome do evento e a `Input` coluna captura a carga do evento.
  * **OrchestratorCompleted**: A função de orquestrador aguardou.
  * **ContinueAsNew**: A função de orquestrador foi concluída e reiniciada com o novo estado. A `Result` coluna contém o valor, que é usado como entrada na instância reiniciada.
  * **ExecutionCompleted**: A função de orquestrador foi executada até a conclusão (ou falhou). As saídas da função ou os detalhes do erro são armazenados na `Result` coluna.
* **Carimbo de data/hora**: O carimbo de data/hora UTC do evento de histórico.
* **Nome**: O nome da função que foi invocada.
* **Entrada**: A entrada formatada em JSON da função.
* **Resultado**: A saída da função; ou seja, seu valor de retorno.

> [!WARNING]
> Embora seja útil como uma ferramenta de depuração, não assuma nenhuma dependência nessa tabela. Ele pode mudar à medida que a extensão de Durable Functions evolui.

Sempre que a função é retomada `await` deC#um ( `yield` ) ou (JavaScript), a estrutura de tarefa durável executa novamente a função de orquestrador do zero. Em cada nova execução, ele consulta o histórico de execução para determinar se a operação assíncrona atual foi realizada.  Se a operação tiver ocorrido, a estrutura repetirá a saída dessa operação imediatamente e passará para o próximo `await` (C#) ou `yield` (JavaScript). Esse processo continua até que todo o histórico tenha sido reproduzido e, nesse ponto, todas as variáveis locais na função de orquestrador sejam restauradas para seus valores anteriores.

## <a name="orchestrator-code-constraints"></a>Restrições de código do Orchestrator

O comportamento de reprodução cria restrições sobre o tipo de código que pode ser escrito em uma função de orquestrador:

* O código do orquestrador deve ser **determinístico**. Ele será reproduzido várias vezes e deverá produzir o mesmo resultado a cada vez. Por exemplo, nenhuma chamada direta para obter a data/hora atual, obter números aleatórios, gerar GUIDs aleatórios ou chamar pontos de extremidade remotos.

  Se o código do Orchestrator precisar obter a data/hora atual, ele deverá usar a API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET `currentUtcDateTime` ) ou (JavaScript), que é segura para reprodução.

  Se o código do Orchestrator precisar gerar um GUID aleatório, ele deverá usar a API [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.net), que é segura para reprodução ou delegar a geração de GUID a uma função de atividade (JavaScript), como neste exemplo:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  As operações não determinísticas devem ser feitas em funções de atividade. Isso inclui qualquer interação com outras associações de entrada ou saída. Isso garante que qualquer valor não determinístico será gerado uma vez na primeira execução e salvo no histórico de execução. As execuções subsequentes usarão o valor salvo automaticamente.

* O código do orquestrador deve ser **sem bloqueio**. Por exemplo, isso significa nenhuma e/s e nenhuma chamada para `Thread.Sleep` (.net) ou APIs equivalentes.

  Se um orquestrador precisar atrasar, ele poderá usar a API [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) CreateTimer (.net) ou `createTimer` (JavaScript).

* O código do Orchestrator **nunca deve iniciar nenhuma operação assíncrona** , exceto usando a `context.df` API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) ou a API do objeto. Por exemplo, não `Task.Run`, `Task.Delay` ou `HttpClient.SendAsync` no .net, ou `setTimeout()` e `setInterval()` em JavaScript. O Framework de tarefa durável executa o código do orquestrador em um único thread e não pode interagir com outros threads que podem ser agendados por outras APIs assíncronas. Caso isso ocorra, `InvalidOperationException` a exceção será lançada.

> [!NOTE]
> A API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) executa e/s assíncrona, o que não é permitido em uma função de orquestrador e só pode ser usado em funções que não sejam de orquestrador.

* **Loops infinitos devem ser evitados** no código do orquestrador. Como o Framework de tarefa durável salva o histórico de execução à medida que a função de orquestração progride, um loop infinito pode fazer com que uma instância de orquestrador fique sem memória. Para cenários de loop infinito, use APIs como [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.net) ou `continueAsNew` (JavaScript) para reiniciar a execução da função e descartar o histórico de execução anterior.

* As funções de orquestrador de `async`JavaScript não podem ser. Eles devem ser declarados como funções de gerador síncrono.

Embora essas restrições possam parecer assustadoras a princípio, na prática, elas não são difíceis de acompanhar. O Framework de tarefa durável tenta detectar violações das regras acima e gera um `NonDeterministicOrchestrationException`. No entanto, esse comportamento de detecção é o melhor esforço, e você não deve depender dele.

> [!NOTE]
> Todas essas regras se aplicam somente a funções disparadas pela `orchestrationTrigger` associação. As `activityTrigger` funções de atividade disparadas pela associação e funções que `orchestrationClient` usam a associação não têm essas limitações.

## <a name="durable-tasks-net"></a>Tarefas duráveis (.NET)

> [!NOTE]
> Esta seção descreve os detalhes de implementação interna do Framework de tarefa durável. Você pode usar Durable Functions sem conhecer essas informações. Ele destina-se apenas a ajudá-lo a entender o comportamento de reprodução.

As tarefas que podem ser aguardadas com segurança em funções de orquestrador são ocasionalmente chamadas de *tarefas duráveis*. Essas são tarefas que são criadas e gerenciadas pelo Framework de tarefa durável. Os exemplos são as tarefas retornadas `WaitForExternalEvent`por `CallActivityAsync`, `CreateTimer`e.

Essas *tarefas duráveis* são gerenciadas internamente usando uma lista de `TaskCompletionSource` objetos. Durante a repetição, essas tarefas são criadas como parte da execução do código do Orchestrator e são concluídas, pois o Dispatcher enumera os eventos de histórico correspondentes. Tudo isso é feito de forma síncrona usando um único thread até que todo o histórico tenha sido reproduzido. Todas as tarefas duráveis, que não são concluídas pelo fim da reprodução do histórico, têm as ações apropriadas executadas. Por exemplo, uma mensagem pode ser enfileirada para chamar uma função de atividade.

O comportamento de execução descrito aqui deve ajudá-lo a entender por que o código `await` de função do Orchestrator nunca deve ser uma tarefa não durável: o thread do Dispatcher não pode esperar que ele seja concluído e qualquer retorno de chamada por essa tarefa poderia corromper o controle estado da função de orquestrador. Algumas verificações de tempo de execução estão em vigor para tentar evitar isso.

Se você quiser mais informações sobre como o Framework de tarefa durável executa funções de orquestrador, a melhor coisa a fazer é consultar o [código-fonte de tarefa durável no GitHub](https://github.com/Azure/durabletask). Em particular, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o gerenciamento de instâncias](durable-functions-instance-management.md)

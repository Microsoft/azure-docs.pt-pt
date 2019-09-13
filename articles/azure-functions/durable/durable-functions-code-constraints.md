---
title: Restrições de código de orquestrador durável-Azure Functions
description: Reprodução de função de orquestração e restrições de código para Durable Functions do Azure.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935771"
---
# <a name="orchestrator-function-code-constraints"></a>Restrições de código de função do Orchestrator

Durable Functions é uma extensão de [Azure Functions](../functions-overview.md) que permite criar aplicativos com estado. Você pode usar uma [função de orquestrador](durable-functions-orchestrations.md) para orquestrar a execução de outras funções duráveis dentro de um aplicativo de funções. As funções de orquestrador são com estado, confiáveis e potencialmente demoradas.

## <a name="orchestrator-code-constraints"></a>Restrições de código do Orchestrator

As funções de orquestrador usam o [fornecimento de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) para garantir a execução confiável e para manter o estado da variável local. O [comportamento de reprodução](durable-functions-orchestrations.md#reliability) do código do Orchestrator cria restrições sobre o tipo de código que você pode escrever em uma função de orquestrador. Por exemplo, o código do orquestrador deve ser *determinístico*.  As funções de orquestrador serão reproduzidas várias vezes e deverão produzir o mesmo resultado a cada vez.

As seções a seguir fornecem algumas diretrizes simples para garantir que seu código seja determinístico.

### <a name="using-deterministic-apis"></a>Usando APIs determinísticas

As funções de orquestrador são gratuitas para chamar qualquer API que desejarem em seu idioma de destino. No entanto, é importante que as funções de orquestrador chamem apenas APIs *determinísticas* . Uma *API determinística* é uma API que sempre retorna exatamente o mesmo valor dado à mesma entrada, não importa quando ou com que frequência ela é chamada.

Veja a seguir alguns exemplos de APIs que devem ser evitadas porque *não* são determinísticas. Essas restrições se aplicam somente a funções de orquestrador. Outros tipos de função não têm essas restrições.

| Categoria da API | Reason | Solução |
| ------------ | ------ | ---------- |
| Datas/horas  | As APIs que retornam a data ou hora _atual_ são não determinísticas, pois elas retornaram um valor diferente para cada repetição. | Use a API do [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET `currentUtcDateTime` ) ou (JavaScript), que é segura para reprodução. |
| GUIDs/UUIDs  | As APIs que retornam um GUID/UUID _aleatório_ são não determinísticas porque o valor gerado será diferente para cada repetição. | Use o [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.net) ou `newGuid` (JavaScript) para gerar com segurança GUIDs aleatórios. |
| Números aleatórios | As APIs que retornam números aleatórios são não determinísticas porque o valor gerado será diferente para cada repetição. | Use uma função de atividade para retornar números aleatórios para uma orquestração. Os valores de retorno das funções de atividade são sempre seguros para reprodução. |
| Enlaces | As associações de entrada e saída normalmente fazem e/s e são não determinísticas. Até mesmo o [cliente de orquestração](durable-functions-bindings.md#orchestration-client) e as associações de [cliente de entidade](durable-functions-bindings.md#entity-client) não devem ser usadas diretamente por uma função de orquestrador. | Use associações de entrada e saída dentro de funções de cliente ou atividade. |
| Rede | As chamadas de rede envolvem sistemas externos e não são determinísticas. | Use funções de atividade para fazer chamadas de rede. Se você precisar fazer uma chamada HTTP de sua função de orquestrador, também terá a opção de usar as [APIs de http duráveis](durable-functions-http-features.md#consuming-http-apis). |
| APIs de bloqueio | APIs de bloqueio como `Thread.Sleep` o (.net) ou outras APIs semelhantes podem causar problemas de desempenho e escalação para funções de orquestrador e devem ser evitados. No plano de consumo de Azure Functions, eles podem até mesmo resultar em encargos de tempo de execução desnecessários. | Use alternativas para bloquear APIs quando disponíveis, como `CreateTimer` para introduzir atrasos na execução de orquestração. Os atrasos de [timer duráveis](durable-functions-timers.md) não contam para o tempo de execução de uma função de orquestrador. |
| APIs assíncronas | O código do Orchestrator **nunca deve iniciar nenhuma operação assíncrona** , exceto usando a `context.df` API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) ou a API do objeto. Por exemplo, não `Task.Run`, `Task.Delay` ou `HttpClient.SendAsync` no .net, ou `setTimeout()` e `setInterval()` em JavaScript. O Framework de tarefa durável executa o código do orquestrador em um único thread e não pode interagir com outros threads que podem ser agendados por outras APIs assíncronas. | Somente chamadas assíncronas *duráveis* devem ser feitas por uma função de orquestrador. Qualquer outra chamada de API assíncrona deve ser feita a partir de funções de atividade. |
| Funções assíncronas de JavaScript | As funções de orquestrador do `async` JavaScript não podem ser porque o tempo de execução do node. js não garante que as funções assíncronas sejam determinísticas. | As funções de orquestrador JavaScript devem ser declaradas como funções de gerador síncrono. |
| APIs de Threading | O Framework de tarefa durável executa o código do orquestrador em um único thread e não pode interagir com outros threads. A introdução de novos threads à execução de uma orquestração pode resultar em execução não determinística ou deadlocks. | As APIs de Threading devem quase nunca ser usadas em funções de orquestrador. Se forem necessárias, elas deverão ser limitadas às funções de atividade. |
| Variáveis estáticas | Variáveis estáticas não constantes devem ser evitadas em funções de orquestrador porque seus valores podem mudar ao longo do tempo, resultando em um comportamento de execução não determinístico. | Use constantes ou limite o uso de variáveis estáticas para funções de atividade. |
| Variáveis de ambiente | Não use variáveis de ambiente em funções de orquestrador. Seus valores podem mudar ao longo do tempo, resultando em um comportamento de execução não determinístico. | As variáveis de ambiente devem ser referenciadas apenas em funções de cliente ou funções de atividade. |
| Loops infinitos | Loops infinitos devem ser evitados em funções de orquestrador. A estrutura de tarefa durável salva o histórico de execução à medida que a função de orquestração progride, de modo que um loop infinito poderia fazer com que uma instância de orquestrador fique sem memória. | Para cenários de loop infinito, use APIs como [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.net) ou `continueAsNew` (JavaScript) para reiniciar a execução da função e descartar o histórico de execução anterior. |

Embora essas restrições possam parecer assustadoras a princípio, na prática, elas não são difíceis de acompanhar. O Framework de tarefa durável tenta detectar violações das regras acima e gera um `NonDeterministicOrchestrationException`. No entanto, esse comportamento de detecção é o melhor esforço, e você não deve depender dele.

## <a name="versioning"></a>Controlo de versões

Uma orquestração durável pode ser executada continuamente por dias, meses, anos ou até mesmo [eternamente](durable-functions-eternal-orchestrations.md). Quaisquer atualizações de código feitas em Durable Functions aplicativos que afetam orquestrações ainda não concluídas podem interromper seu comportamento de reprodução. Portanto, é importante planejar com cuidado ao fazer atualizações no código. Para obter uma descrição mais detalhada de como fazer a versão de seu código, consulte o artigo [controle de versão](durable-functions-versioning.md) .

## <a name="durable-tasks"></a>Tarefas duráveis

> [!NOTE]
> Esta seção descreve os detalhes de implementação interna do Framework de tarefa durável. Você pode usar Durable Functions sem conhecer essas informações. Ele destina-se apenas a ajudá-lo a entender o comportamento de reprodução.

As tarefas que podem ser aguardadas com segurança em funções de orquestrador são ocasionalmente chamadas de *tarefas duráveis*. Essas tarefas são criadas e gerenciadas pelo Framework de tarefa durável. Os exemplos são as tarefas retornadas `WaitForExternalEvent`por `CallActivityAsync`, `CreateTimer` e nas funções do .net Orchestrator.

Essas *tarefas duráveis* são gerenciadas internamente usando uma lista de `TaskCompletionSource` objetos no .net. Durante a repetição, essas tarefas são criadas como parte da execução do código do Orchestrator e são concluídas, pois o Dispatcher enumera os eventos de histórico correspondentes. A execução é feita de forma síncrona usando um único thread até que todo o histórico tenha sido reproduzido. Todas as tarefas duráveis que não são concluídas pelo fim da reprodução do histórico têm as ações apropriadas executadas. Por exemplo, uma mensagem pode ser enfileirada para chamar uma função de atividade.

O comportamento de execução descrito aqui deve ajudá-lo a entender por que o código `await` de `yield` função do Orchestrator nunca deve ou uma tarefa não durável: o thread do Dispatcher não pode esperar que ele seja concluído e qualquer retorno de chamada por essa tarefa poderia potencialmente corromper o estado de acompanhamento da função de orquestrador. Algumas verificações de tempo de execução estão em vigor para tentar detectar essas violações.

Se você quiser mais informações sobre como o Framework de tarefa durável executa funções de orquestrador, a melhor coisa a fazer é consultar o [código-fonte de tarefa durável no GitHub](https://github.com/Azure/durabletask). Em particular, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como invocar as suborquestrações](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como lidar com controle de versão](durable-functions-versioning.md)

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
ms.openlocfilehash: 66e33874c3cfe8f9d2594489b3165f81b2ce84ab
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694828"
---
# <a name="orchestrator-function-code-constraints"></a>Restrições de código de função do Orchestrator

Durable Functions é uma extensão de [Azure Functions](../functions-overview.md) que permite criar aplicativos com estado. Você pode usar uma [função de orquestrador](durable-functions-orchestrations.md) para orquestrar a execução de outras funções duráveis dentro de um aplicativo de funções. As funções de orquestrador são com estado, confiáveis e potencialmente demoradas.

## <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

As funções de orquestrador usam o [fornecimento de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) para garantir a execução confiável e para manter o estado da variável local. O [comportamento de reprodução](durable-functions-orchestrations.md#reliability) do código do Orchestrator cria restrições sobre o tipo de código que você pode escrever em uma função de orquestrador. Por exemplo, as funções de orquestrador devem ser *determinísticas*: uma função de orquestrador será repetida várias vezes e deverá produzir o mesmo resultado a cada vez.

### <a name="using-deterministic-apis"></a>Usando APIs determinísticas

Esta seção fornece algumas diretrizes simples que ajudam a garantir que seu código seja determinístico.

As funções de orquestrador podem chamar qualquer API em seus idiomas de destino. No entanto, é importante que as funções de orquestrador chamem apenas APIs determinísticas. Uma *API determinística* é uma API que sempre retorna o mesmo valor devido à mesma entrada, não importa quando ou com que frequência ela é chamada.

A tabela a seguir mostra exemplos de APIs que você deve evitar porque elas *não* são determinísticas. Essas restrições se aplicam somente a funções de orquestrador. Outros tipos de função não têm essas restrições.

| Categoria da API | Reason | Solução |
| ------------ | ------ | ---------- |
| Datas e horas  | As APIs que retornam a data ou hora atual são não determinísticas porque o valor retornado é diferente para cada repetição. | Use a API do [**CurrentUtcDateTime**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) no .net ou a API do **CurrentUtcDateTime** em JavaScript, que é segura para reprodução. |
| GUIDs e UUIDs  | As APIs que retornam um GUID aleatório ou UUID são não determinísticas porque o valor gerado é diferente para cada repetição. | Use [**NewGuid**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) no .net ou **NewGuid** no JavaScript para gerar com segurança GUIDs aleatórios. |
| Números aleatórios | As APIs que retornam números aleatórios são não determinísticas porque o valor gerado é diferente para cada repetição. | Use uma função de atividade para retornar números aleatórios para uma orquestração. Os valores de retorno das funções de atividade são sempre seguros para reprodução. |
| Enlaces | As associações de entrada e saída normalmente fazem e/s e não são determinísticas. Uma função de orquestrador não deve usar diretamente as associações [cliente de orquestração](durable-functions-bindings.md#orchestration-client) e [cliente de entidade](durable-functions-bindings.md#entity-client) . | Use associações de entrada e saída dentro de funções de cliente ou atividade. |
| Rede | As chamadas de rede envolvem sistemas externos e não são determinísticas. | Use funções de atividade para fazer chamadas de rede. Se você precisar fazer uma chamada HTTP de sua função de orquestrador, também poderá usar as [APIs de http duráveis](durable-functions-http-features.md#consuming-http-apis). |
| APIs de bloqueio | Bloqueando APIs como **Thread. Suspensão @ no__t-0 no .NET e APIs semelhantes podem causar problemas de desempenho e escalação para funções de orquestrador e devem ser evitadas. No plano de consumo de Azure Functions, eles podem até mesmo resultar em encargos de tempo de execução desnecessários. | Use alternativas para bloquear APIs quando elas estiverem disponíveis. Por exemplo, use **CreateTimer** para introduzir atrasos na execução de orquestração. Os atrasos de [temporizadores duráveis](durable-functions-timers.md) não contam para o tempo de execução de uma função de orquestrador. |
| APIs assíncronas | O código do Orchestrator nunca deve iniciar nenhuma operação assíncrona, exceto usando a API [**DurableOrchestrationContext**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) ou a API do objeto **Context. DF** . Por exemplo, você não pode usar **Task. Run**, **Task. Delay**e **HttpClient. SendAsync** em .net ou **setTimeout** e **setInterval** em JavaScript. O Framework de tarefa durável executa o código do Orchestrator em um único thread. Ele não pode interagir com nenhum outro thread que possa ser chamado por outras APIs assíncronas. | Uma função de orquestrador deve fazer apenas chamadas assíncronas duráveis. As funções de atividade devem fazer outras chamadas de API assíncronas. |
| Funções assíncronas de JavaScript | Não é possível declarar funções de orquestrador JavaScript como **Async** porque o tempo de execução do node. js não garante que as funções assíncronas sejam determinísticas. | Declare as funções de orquestrador JavaScript como funções de gerador síncrono. |
| APIs de Threading | O Framework de tarefa durável executa o código do Orchestrator em um único thread e não pode interagir com outros threads. A introdução de novos threads à execução de uma orquestração pode resultar em execução não determinística ou deadlocks. | Funções de orquestrador quase nunca devem usar APIs de Threading. Se essas APIs forem necessárias, limite seu uso apenas às funções de atividade. |
| Variáveis estáticas | Evite usar variáveis estáticas não constantes em funções de orquestrador porque seus valores podem mudar ao longo do tempo, resultando em um comportamento de tempo de execução não determinístico. | Use constantes ou limite o uso de variáveis estáticas para funções de atividade. |
| Variáveis de ambiente | Não use variáveis de ambiente em funções de orquestrador. Seus valores podem mudar ao longo do tempo, resultando em um comportamento de tempo de execução não determinístico. | As variáveis de ambiente devem ser referenciadas somente de dentro de funções de cliente ou funções de atividade. |
| Loops infinitos | Evite loops infinitos em funções de orquestrador. Como o Framework de tarefa durável salva o histórico de execução à medida que a função de orquestração progride, um loop infinito pode fazer com que uma instância de orquestrador fique sem memória. | Para cenários de loop infinito, use APIs como [**ContinueAsNew**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) no .net ou **ContinueAsNew** no JavaScript para reiniciar a execução da função e para descartar o histórico de execução anterior. |

Embora a aplicação dessas restrições possa parecer difícil em primeiro lugar, na prática, elas são fáceis de acompanhar.

O Framework de tarefa durável tenta detectar violações das regras anteriores. Se encontrar uma violação, a estrutura lançará uma exceção **NonDeterministicOrchestrationException** . No entanto, esse comportamento de detecção não capturará todas as violações e você não deverá depender dela.

## <a name="versioning"></a>Controlo de versões

Uma orquestração durável pode ser executada continuamente por dias, meses, anos ou até mesmo [eternamente](durable-functions-eternal-orchestrations.md). Quaisquer atualizações de código feitas em Durable Functions aplicativos que afetam orquestrações não concluídas podem interromper o comportamento de reprodução das orquestrações. É por isso que é importante planejar com cuidado ao fazer atualizações no código. Para obter uma descrição mais detalhada de como fazer a versão de seu código, consulte o [artigo controle de versão](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Tarefas duráveis

> [!NOTE]
> Esta seção descreve os detalhes de implementação interna do Framework de tarefa durável. Você pode usar funções duráveis sem conhecer essas informações. Ele destina-se apenas a ajudá-lo a entender o comportamento de reprodução.

As tarefas que podem esperar com segurança nas funções de orquestrador são ocasionalmente chamadas de *tarefas duráveis*. A estrutura de tarefa durável cria e gerencia essas tarefas. Os exemplos são as tarefas retornadas por **CallActivityAsync**, **WaitForExternalEvent**e **CreateTimer** nas funções do orquestrador do .net.

Essas tarefas duráveis são gerenciadas internamente por uma lista de objetos **TaskCompletionSource** no .net. Durante a repetição, essas tarefas são criadas como parte da execução do código do Orchestrator. Elas são concluídas à medida que o Dispatcher enumera os eventos de histórico correspondentes.

As tarefas são executadas de forma síncrona usando um único thread até que todo o histórico tenha sido reproduzido. As tarefas duráveis que não são concluídas pelo fim da reprodução do histórico têm as ações apropriadas executadas. Por exemplo, uma mensagem pode ser enfileirada para chamar uma função de atividade.

A descrição desta seção de comportamento de tempo de execução deve ajudá-lo a entender por que uma função de orquestrador não pode usar **Await** ou **yield** em uma tarefa não durável. Há dois motivos: o thread do Dispatcher não pode aguardar a conclusão da tarefa e qualquer retorno de chamada por essa tarefa pode corromper o estado de controle da função de orquestrador. Algumas verificações de tempo de execução estão em vigor para ajudar a detectar essas violações.

Para saber mais sobre como o Framework de tarefa durável executa funções de orquestrador, consulte o [código-fonte de tarefa durável no GitHub](https://github.com/Azure/durabletask). Em particular, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como invocar as suborquestrações](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como lidar com controle de versão](durable-functions-versioning.md)

---
title: Restrições de código orquestrador duráveis - Funções Azure
description: Repetição da função de orquestração e restrições de código para Funções Duradouras Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 14e0b86f11c3eabf93e7d4f0ebf563e59c0c21e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081870"
---
# <a name="orchestrator-function-code-constraints"></a>Restrições do código de função do orquestrador

Funções Duradouras é uma extensão de [Funções Azure](../functions-overview.md) que permite construir aplicações imponentes. Pode utilizar uma [função orquestradora](durable-functions-orchestrations.md) para orquestrar a execução de outras funções duráveis dentro de uma aplicação de função. As funções de orquestrador são imponentes, fiáveis e potencialmente de longa duração.

## <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

As funções do orquestrador utilizam [o abastecimento de eventos](/azure/architecture/patterns/event-sourcing) para garantir uma execução fiável e para manter o estado variável local. O comportamento de [repetição](durable-functions-orchestrations.md#reliability) do código orquestrador cria constrangimentos no tipo de código que pode escrever numa função orquestradora. Por exemplo, as funções orquestradoras devem ser *determinísticas:* uma função orquestradora será repetida várias vezes, e deve produzir o mesmo resultado de cada vez.

### <a name="using-deterministic-apis"></a>Utilização de APIs determinísticos

Esta secção fornece algumas diretrizes simples que ajudam a garantir que o seu código é determinístico.

As funções de orquestrador podem chamar qualquer API nas suas línguas-alvo. No entanto, é importante que as funções do orquestrador chamem apenas APIs deterministas. Uma *API determinística* é uma API que devolve sempre o mesmo valor dada a mesma entrada, independentemente de quando ou com que frequência é chamada.

A tabela que se segue mostra exemplos de APIs que deve evitar porque *não* são deterministas. Estas restrições aplicam-se apenas às funções de orquestrador. Outros tipos de funções não têm tais restrições.

| Categoria API | Razão | Solução |
| ------------ | ------ | ---------- |
| Datas e horas  | As APIs que devolvem a data ou hora corrente não são desdeterministas porque o valor devolvido é diferente para cada repetição. | Utilize a `CurrentUtcDateTime` API em .NET ou na `currentUtcDateTime` API em JavaScript, que são seguras para repetição. |
| GUIDs e UUIDs  | As APIs que devolvem um GUID ou UUID aleatórios não são desdeterministas porque o valor gerado é diferente para cada repetição. | Utilize `NewGuid` em .NET ou `newGuid` em JavaScript para gerar GUIDs aleatórios de forma segura. |
| Números aleatórios | As APIs que devolvem números aleatórios não são desdeterministas porque o valor gerado é diferente para cada repetição. | Utilize uma função de atividade para devolver números aleatórios a uma orquestração. Os valores de retorno das funções de atividade são sempre seguros para a repetição. |
| Enlaces | As ligações de entrada e saída normalmente fazem E/S e não são desdeterminísticas. Uma função orquestradora não deve utilizar diretamente nem mesmo as ligações do [cliente de orquestração](durable-functions-bindings.md#orchestration-client) e [da entidade](durable-functions-bindings.md#entity-client) cliente. | Utilize encadernações de entrada e saída dentro das funções de cliente ou de atividade. |
| Rede | As chamadas de rede envolvem sistemas externos e não são desdeterminais. | Utilize funções de atividade para escamar chamadas de rede. Se necessitar de fazer uma chamada HTTP da função do seu orquestrador, também pode utilizar as [APIS HTTP duráveis.](durable-functions-http-features.md#consuming-http-apis) |
| APIs de bloqueio | Bloquear APIs como `Thread.Sleep` em .NET e APIs semelhantes pode causar problemas de desempenho e escala para funções de orquestrador e deve ser evitado. No plano de consumo de funções Azure, podem mesmo resultar em taxas de execução desnecessárias. | Use alternativas para bloquear APIs quando estiverem disponíveis. Por exemplo, use  `CreateTimer` para introduzir atrasos na execução da orquestração. [Atrasos temporais duradouros](durable-functions-timers.md) não contam para o tempo de execução de uma função orquestradora. |
| Async APIs | O código do orquestrador nunca deve iniciar qualquer operação de assíduo, exceto utilizando a `IDurableOrchestrationContext` API ou a `context.df` API do objeto. Por exemplo, não é possível utilizar `Task.Run` `Task.Delay` , e em `HttpClient.SendAsync` .NET ou em `setTimeout` `setInterval` JavaScript. O Quadro de Tarefas Duráveis executa o código do orquestrador num único fio. Não pode interagir com outros fios que possam ser chamados por outras APIs async. | Uma função orquestradora deve fazer apenas chamadas de async duráveis. As funções de atividade devem escama para qualquer outra chamada de API de apimento. |
| Funções Async JavaScript | Não é possível declarar funções de orquestrador JavaScript como `async` porque o tempo de execução node.js não garante que as funções assíncronas sejam determinísticas. | Declare funções de orquestrador JavaScript como funções de gerador sincronizado. |
| APIs de roscar | O Quadro de Tarefas Duráveis executa código de orquestrador num único fio e não pode interagir com outros fios. Introduzir novos fios na execução de uma orquestração pode resultar em execuções não deterministas ou impasses. | As funções do orquestrador quase nunca devem utilizar APIs de roscar. Por exemplo, em .NET, evite a `ConfigureAwait(continueOnCapturedContext: false)` utilização; isto garante que as continuações de tarefas são executadas no original da função orquestradora `SynchronizationContext` . Se tais APIs forem necessárias, limite a sua utilização apenas a funções de atividade. |
| Variáveis estáticas | Evite utilizar variáveis estáticas não conseticas em funções orquestradoras porque os seus valores podem mudar ao longo do tempo, resultando em comportamentos de execução não determinísticos. | Use constantes ou limite a utilização de variáveis estáticas para funções de atividade. |
| Variáveis de ambiente | Não utilize variáveis ambientais em funções orquestradoras. Os seus valores podem mudar ao longo do tempo, resultando num comportamento não desdeterminista. | As variáveis ambientais só devem ser referenciadas a partir de funções de cliente ou funções de atividade. |
| Laços infinitos | Evite laços infinitos nas funções orquestradoras. Como o Quadro de Tarefas Duráveis salva a história da execução à medida que a função de orquestração progride, um loop infinito pode fazer com que um exemplo de orquestrador fique sem memória. | Para cenários de loop infinitos, utilize APIs como `ContinueAsNew` em .NET ou `continueAsNew` em JavaScript para reiniciar a execução da função e descartar o histórico de execução anterior. |

Embora aplicar estes constrangimentos possa parecer difícil no início, na prática são fáceis de seguir.

O Quadro de Tarefas Duráveis tenta detetar violações das regras anteriores. Se encontrar uma violação, o quadro lança uma exceção **não determinísticaOrchestrationException.** No entanto, este comportamento de deteção não vai apanhar todas as violações, e não deves depender disso.

## <a name="versioning"></a>Controlo de versões

Uma orquestração duradoura pode funcionar continuamente durante dias, meses, anos, ou mesmo [eternamente.](durable-functions-eternal-orchestrations.md) Quaisquer atualizações de código feitas para aplicações Durable Functions que afetem orquestrações inacabadas podem quebrar o comportamento de repetição das orquestrações. É por isso que é importante planear cuidadosamente ao fazer atualizações para código. Para uma descrição mais detalhada de como ver o seu código, consulte o [artigo de versão](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Tarefas duradouras

> [!NOTE]
> Esta secção descreve detalhes de implementação interna do Quadro de Tarefas Duráveis. Pode utilizar funções duráveis sem conhecer esta informação. Destina-se apenas a ajudá-lo a entender o comportamento de repetição.

Tarefas que podem esperar com segurança nas funções de orquestrador são ocasionalmente referidas como *tarefas duradouras*. O Quadro de Tarefas Durável cria e gere estas tarefas. Exemplos são as tarefas devolvidas por **CallActivityAsync**, **WaitForExternalEvent**e **CreateTimer** em funções de orquestrador .NET.

Estas tarefas duráveis são geridas internamente por uma lista de `TaskCompletionSource` objetos em .NET. Durante a repetição, estas tarefas são criadas como parte da execução do código orquestrador. Estão acabados enquanto o despachante enumera os correspondentes eventos históricos.

As tarefas são executadas sincronizadamente usando um único fio até que toda a história tenha sido reproduzida. Tarefas duradouras que não estão terminadas até ao final da repetição da história têm ações apropriadas executadas. Por exemplo, uma mensagem pode ser encostada para chamar uma função de atividade.

A descrição desta secção de comportamento em tempo de execução deve ajudá-lo a entender por que uma função orquestradora não pode usar `await` ou numa tarefa não `yield` roficadora. Há duas razões: o fio do despachante não pode esperar que a tarefa termine, e qualquer retorno por essa tarefa pode potencialmente corromper o estado de rastreio da função do orquestrador. Algumas verificações de tempo de execução estão no local para ajudar a detetar estas violações.

Para saber mais sobre como o Quadro de Tarefas Duráveis executa funções de orquestrador, consulte o [código fonte de tarefa durável no GitHub](https://github.com/Azure/durabletask). Em particular, ver [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como invocar sub-orquestrações](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como lidar com a versão](durable-functions-versioning.md)

---
title: Restrições de código orquestrador duráveis - Funções Azure
description: Repetição da função de orquestração e restrições de código para funções duráveis Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77562144"
---
# <a name="orchestrator-function-code-constraints"></a>Restrições de código de função orquestrador

Funções Duráveis é uma extensão das [Funções Azure](../functions-overview.md) que permite construir aplicações imponentes. Pode utilizar uma [função de orquestrador](durable-functions-orchestrations.md) para orquestrar a execução de outras funções duráveis dentro de uma aplicação de função. As funções de orquestrador são estatais, fiáveis e potencialmente de longa duração.

## <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

As funções de orquestrador usam [o fornecimento de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) para garantir uma execução fiável e para manter o estado variável local. O comportamento de [repetição](durable-functions-orchestrations.md#reliability) do código orquestrador cria constrangimentos no tipo de código que pode escrever numa função orquestradora. Por exemplo, as funções de orquestrador devem ser *determinísticas:* uma função orquestradora será repetida várias vezes, e deve produzir o mesmo resultado de cada vez.

### <a name="using-deterministic-apis"></a>Utilização de APIs determinísticos

Esta secção fornece algumas diretrizes simples que ajudam a garantir que o seu código é determinista.

As funções de orquestradores podem chamar qualquer API nas suas línguas-alvo. No entanto, é importante que as funções orquestradoras chamem apenas APIs deterministas. Uma *API determinística* é uma API que devolve sempre o mesmo valor dado o mesmo contributo, não importa quando ou com que frequência é chamado.

A tabela que se segue mostra exemplos de APIs que deve evitar porque *não* são deterministas. Estas restrições aplicam-se apenas às funções orquestradoras. Outros tipos de funções não têm tais restrições.

| Categoria API | Razão | Solução |
| ------------ | ------ | ---------- |
| Datas e horas  | As APIs que devolvem a data ou a hora atuais não são determinísticas porque o valor devolvido é diferente para cada repetição. | Utilize`CurrentUtcDateTime` a API em `currentUtcDateTime` .NET ou a API no JavaScript, que são seguras para repetição. |
| GUIDs e UUIDs  | As APIs que devolvem um GUID ou UUID aleatórios não são determinísticas porque o valor gerado é diferente para cada repetição. | Utilize `NewGuid` em .NET ou `newGuid` em JavaScript para gerar GUIDs aleatórios com segurança. |
| Números aleatórios | ApIs que devolvem números aleatórios não são determinísticos porque o valor gerado é diferente para cada repetição. | Use uma função de atividade para devolver números aleatórios a uma orquestração. Os valores de retorno das funções de atividade são sempre seguros para repetição. |
| Enlaces | As encadernações de entrada e saída normalmente fazem I/O e não são determinísticas. Uma função orquestradora não deve utilizar diretamente nem mesmo as ligações do cliente da [orquestração](durable-functions-bindings.md#orchestration-client) e [da entidade.](durable-functions-bindings.md#entity-client) | Utilize encadernações de entrada e saída dentro das funções de cliente ou atividade. |
| Rede | As chamadas de rede envolvem sistemas externos e não são determinísticas. | Utilize funções de atividade para fazer chamadas de rede. Se precisar de fazer uma chamada http da sua função de orquestrador, também pode utilizar as [APIs http duráveis](durable-functions-http-features.md#consuming-http-apis). |
| Bloqueio de APIs | Bloquear APIs `Thread.Sleep` como em .NET e APIs semelhantes pode causar problemas de desempenho e escala para funções orquestradoras e deve ser evitado. No plano de consumo de funções Azure, podem mesmo resultar em encargos de tempo de funcionamento desnecessários. | Use alternativas para bloquear APIs quando estão disponíveis. Por exemplo, `CreateTimer` usar para introduzir atrasos na execução da orquestração. [Atrasos duradouros](durable-functions-timers.md) não contam para o tempo de execução de uma função orquestradora. |
| APIs de asincronização | O código orquestrador nunca deve iniciar qualquer `IDurableOrchestrationContext` operação de `context.df` asincronização, exceto utilizando a API ou a API do objeto. Por exemplo, não pode `Task.Run` `Task.Delay`usar `HttpClient.SendAsync` , e `setTimeout` em `setInterval` .NET ou e em JavaScript. O Quadro de Tarefas Duráveis executa o código orquestrador num único fio. Não pode interagir com outros fios que possam ser chamados por outras APIs asincronizadas. | Uma função orquestradora deve fazer apenas chamadas assinásias duráveis. As funções de atividade devem fazer quaisquer outras chamadas API asincronizadas. |
| Funções JavaScript asincronizadas | Não pode declarar as funções do `async` orquestrador JavaScript porque o tempo de funcionamento do nó.js não garante que funções assíncronas sejam determinísticas. | Declare as funções do orquestrador JavaScript como funções sincronizadas do gerador. |
| APIs roscados | O Quadro de Tarefas Duráveis executa o código orquestrador num único fio e não pode interagir com outros fios. Introduzir novos fios na execução de uma orquestração pode resultar em execução não determinística ou impasses. | As funções de orquestrador quase nunca devem usar APIs roscados. Por exemplo, em .NET, evite utilizar; `ConfigureAwait(continueOnCapturedContext: false)` isto garante a continuação da tarefa no `SynchronizationContext`original da função orquestradora. Se tais APIs forem necessárias, limite a sua utilização apenas a funções de atividade. |
| Variáveis estáticas | Evite utilizar variáveis estáticas não constantes em funções orquestradoras porque os seus valores podem mudar ao longo do tempo, resultando num comportamento de tempo de execução não determinístico. | Utilize constantes ou limite o uso de variáveis estáticas às funções de atividade. |
| Variáveis de ambiente | Não use variáveis ambientais em funções orquestradoras. Os seus valores podem mudar ao longo do tempo, resultando num comportamento não determinístico do tempo de execução. | As variáveis ambientais só devem ser referenciadas a partir de funções ou funções de atividade do cliente. |
| Laços infinitos | Evite laços infinitos em funções orquestradoras. Como o Quadro de Tarefas Duráveis salva a história da execução à medida que a função de orquestração progride, um ciclo infinito pode fazer com que um caso orquestrador fique sem memória. | Para cenários de loop infinitos, utilize APIs como `ContinueAsNew` em .NET ou `continueAsNew` em JavaScript para reiniciar a execução da função e descartar o histórico de execução anterior. |

Embora aplicar estes constrangimentos possa parecer difícil no início, na prática são fáceis de seguir.

O Quadro de Tarefas Duráveis tenta detetar violações das regras anteriores. Se encontrar uma violação, o quadro lança uma exceção não **determinística.** No entanto, este comportamento de deteção não vai apanhar todas as violações, e não deves depender disso.

## <a name="versioning"></a>Controlo de versões

Uma orquestração durável pode funcionar continuamente durante dias, meses, anos, ou mesmo [eternamente.](durable-functions-eternal-orchestrations.md) Quaisquer atualizações de código feitas para aplicações de Funções Duráveis que afetem orquestrações inacabadas podem quebrar o comportamento de repetição das orquestrações. É por isso que é importante planear cuidadosamente ao fazer atualizações para o código. Para obter uma descrição mais detalhada de como vero seu código, consulte o artigo de [versão](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Tarefas duradouras

> [!NOTE]
> Esta secção descreve detalhes internos de implementação do Quadro de Tarefas Duráveis. Pode utilizar funções duradouras sem conhecer esta informação. Destina-se apenas a ajudá-lo a entender o comportamento de repetição.

As tarefas que podem esperar com segurança em funções orquestradoras são ocasionalmente referidas como *tarefas duráveis.* O Quadro de Tarefas Duráveis cria e gere estas tarefas. Exemplos são as tarefas devolvidas por **CallActivityAsync,** **WaitForExternalEvent**e **CreateTimer** em funções de orquestrador .NET.

Estas tarefas duradouras são geridas `TaskCompletionSource` internamente por uma lista de objetos em .NET. Durante a repetição, estas tarefas são criadas como parte da execução de código orquestrador. Estão acabados como o despachante enumera os eventos de história correspondentes.

As tarefas são executadas sincronizadamente usando um único fio até que toda a história tenha sido reproduzida. Tarefas duradouras que não estão terminadas até ao final da história têm ações apropriadas realizadas. Por exemplo, uma mensagem pode ser enquecida para chamar uma função de atividade.

A descrição desta secção do comportamento do tempo de execução `await` deve `yield` ajudá-lo a entender por que uma função orquestradora não pode usar ou numa tarefa não durável. Há duas razões: o fio dedespacho não pode esperar que a tarefa termine, e qualquer chamada por essa tarefa pode potencialmente corromper o estado de rastreio da função orquestradora. Alguns controlos de tempo de execução estão em vigor para ajudar a detetar estas violações.

Para saber mais sobre como o Quadro de Tarefas Duráveis executa funções de orquestrador, consulte o código fonte de [tarefadurável no GitHub](https://github.com/Azure/durabletask). Em particular, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs.](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprenda a invocar suborquestrações](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como lidar com a versão](durable-functions-versioning.md)

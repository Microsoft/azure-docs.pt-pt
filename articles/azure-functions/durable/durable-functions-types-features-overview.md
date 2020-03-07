---
title: Tipos de funções em funções duráveis azure
description: Conheça os tipos de funções e funções que suportam a comunicação função-a-função numa orquestração de Funções Duráveis em Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357874"
---
# <a name="durable-functions-types-and-features"></a>Tipos e características de funções duráveis

Funções Duráveis é uma extensão das [Funções Azure.](../functions-overview.md) Pode utilizar Funções Duráveis para uma orquestração audatuosa da execução da função. Uma aplicação de função durável é uma solução que é composta por diferentes funções Azure. As funções podem desempenhar diferentes papéis numa orquestração de funções duráveis. 

Existem atualmente quatro tipos de funções duráveis em Funções Azure: atividade, orquestrador, entidade e cliente. O resto desta secção vai mais detalhes sobre os tipos de funções envolvidas numa orquestração.

## <a name="orchestrator-functions"></a>Funções orquestradoras

As funções de orquestrador descrevem como as ações são executadas e a ordem em que as ações são executadas. As funções do orquestrador descrevemC# a orquestração em código (ou JavaScript) como mostrado nos padrões de aplicação de [Funções Duráveis](durable-functions-overview.md#application-patterns). Uma orquestração pode ter muitos tipos diferentes de ações, incluindo [funções](#activity-functions)de atividade, [suborquestrações,](durable-functions-orchestrations.md#sub-orchestrations) [esperando por eventos externos,](durable-functions-orchestrations.md#external-events) [HTTP,](durable-functions-http-features.md)e [temporizadores.](durable-functions-orchestrations.md#durable-timers) As funções de orquestrador também podem interagir com [funções da entidade.](#entity-functions)

> [!NOTE]
> As funções de orquestrador são escritas usando código comum, mas existem requisitos rigorosos sobre como escrever o código. Especificamente, o código de função orquestrador deve ser *determinista.* Não seguir estes requisitos de determinismo pode fazer com que as funções de orquestração não funcionem corretamente. Informações detalhadas sobre estes requisitos e como trabalhar à sua volta podem ser encontradas no tópico de restrições de [código.](durable-functions-code-constraints.md)

Para obter informações mais detalhadas sobre as funções orquestradoras e as suas características, consulte o artigo [de orquestrações duráveis.](durable-functions-orchestrations.md)

## <a name="activity-functions"></a>Funções de atividade

As funções de atividade são a unidade básica de trabalho numa orquestração de funções duráveis. As funções de atividade são as funções e tarefas que são orquestradas no processo. Por exemplo, pode criar uma função orquestradora para processar uma ordem. As tarefas envolvem verificar o inventário, cobrar o cliente e criar um carregamento. Cada tarefa seria uma função de atividade separada. Estas funções de atividade podem ser executadas em série, paralelamente, ou alguma combinação de ambas.

Ao contrário das funções orquestradoras, as funções de atividade não são restritas no tipo de trabalho que se pode fazer nelas. As funções de atividade são frequentemente utilizadas para fazer chamadas de rede ou executar operações intensivas cpu. Uma função de atividade também pode devolver os dados à função de orquestrador. O Quadro de Tarefas Duráveis garante que cada função de atividade será executada *pelo menos uma vez* durante a execução de uma orquestração.

> [!NOTE]
> Como as funções de atividade só garantem *pelo menos uma vez a* execução, recomendamos que torne a sua função de atividade lógica *idempotente* sempre que possível.

Utilize um gatilho de [atividade](durable-functions-bindings.md#activity-trigger) para definir uma função de atividade. as funções .NET recebem uma `DurableActivityContext` como parâmetro. Também pode ligar o gatilho a qualquer outro objeto sériedo JSON para passar as inputs à função. No JavaScript, pode aceder a uma entrada através da propriedade `<activity trigger binding name>` no [objeto`context.bindings`](../functions-reference-node.md#bindings). As funções de atividade só podem ter um único valor passado para eles. Para passar vários valores, deve utilizar tuples, matrizes ou tipos complexos.

> [!NOTE]
> Só pode desencadear uma função de atividade a partir de uma função de orquestrador.

## <a name="entity-functions"></a>Funções da entidade

As funções da entidade definem as operações de leitura e atualização de pequenas partes de Estado. Muitas vezes referimo-nos a estas entidades audais como *entidades duráveis.* Tal como as funções orquestradoras, as funções da entidade são funções com um tipo especial de gatilho, *gatilho de entidade.* Também podem ser invocados a partir de funções do cliente ou de funções orquestradoras. Ao contrário das funções orquestradoras, as funções da entidade não têm restrições de código específicas. As funções da entidade também gerem explicitamente o Estado em vez de representar implicitamente o Estado através do fluxo de controlo.

> [!NOTE]
> As funções da entidade e funcionalidades conexas só estão disponíveis nas Funções Duráveis 2.0 ou superior.

Para mais informações sobre as funções da entidade, consulte o artigo [Entidades Duráveis.](durable-functions-entities.md)

## <a name="client-functions"></a>Funções do cliente

As funções de orquestrador são desencadeadas por um gatilho de [orquestração](durable-functions-bindings.md#orchestration-trigger) e as funções da entidade são desencadeadas por uma [ligação](durable-functions-bindings.md#entity-trigger)de gatilho de entidade . Ambos os gatilhos funcionam reagindo a mensagens que são encaminhadas para um centro de [tarefas.](durable-functions-task-hubs.md) A principal forma de entregar estas mensagens é utilizando uma ligação de [cliente orquestrador](durable-functions-bindings.md#orchestration-client) ou uma [entidade vinculada](durable-functions-bindings.md#entity-client) a partir de dentro de uma *função cliente*. Qualquer função não orquestradora pode ser uma *função do cliente.* Por exemplo, pode acionar o orquestrador a partir de uma função desencadeada por HTTP, uma função ativada pelo Azure Event Hub, etc. O que torna uma função de cliente uma *função* é a utilização da ligação de saída duradoura do cliente.

> [!NOTE]
> Ao contrário de outros tipos de funções, as funções de orquestrador e entidade não podem ser acionadas diretamente utilizando os botões do Portal Azure. Se pretender testar uma função de orquestrador ou entidade no Portal Azure, deve, em vez disso, executar uma *função de cliente* que inicie uma função de orquestrador ou entidade como parte da sua implementação. Para a experiência de teste mais simples, recomenda-se uma função manual do *gatilho.*

Além de desencadear funções de orquestrador ou entidade, a ligação duradoura do *cliente* pode ser usada para interagir com orquestrações e entidades de corrida. Por exemplo, as orquestrações podem ser consultadas, encerradas, e podem ter eventos criados para eles. Para obter mais informações sobre gestão de orquestrações e entidades, consulte o artigo de gestão da [Instância.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Passos seguintes

Para começar, crie a [C#](durable-functions-create-first-csharp.md) sua primeira função durável em ou [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Ler mais sobre orquestrações de Funções Duráveis](durable-functions-orchestrations.md)
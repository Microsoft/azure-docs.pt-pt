---
title: Tipos de funções em Funções Duráveis Azure
description: Conheça os tipos de funções e funções que suportam a comunicação funcional numa orquestração de funções duráveis em Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697712"
---
# <a name="durable-functions-types-and-features"></a>Tipos e funcionalidades de funções duráveis

Funções Duradouras é uma extensão das [Funções Azure](../functions-overview.md). Pode utilizar Funções Duradouras para orquestração imponente da execução de funções. Uma aplicação de função durável é uma solução composta por diferentes funções Azure. As funções podem desempenhar diferentes papéis numa orquestração de funções duradouras. 

Existem atualmente quatro tipos de funções duráveis em Funções Azure: atividade, orquestrador, entidade e cliente. O resto desta secção vai para mais detalhes sobre os tipos de funções envolvidas numa orquestração.

## <a name="orchestrator-functions"></a>Funções orquestradoras

As funções orquestradoras descrevem como as ações são executadas e a ordem em que as ações são executadas. As funções orquestradoras descrevem a orquestração em código (C# ou JavaScript) como mostrado nos [padrões de aplicação de Funções Duráveis](durable-functions-overview.md#application-patterns). Uma orquestração pode ter muitos tipos diferentes de ações, incluindo funções de [atividade,](#activity-functions) [sub-orquestrações,](durable-functions-orchestrations.md#sub-orchestrations) [à espera de eventos externos,](durable-functions-orchestrations.md#external-events) [HTTP,](durable-functions-http-features.md)e [temporizadores.](durable-functions-orchestrations.md#durable-timers) As funções orquestradoras também podem interagir com [as funções da entidade.](#entity-functions)

> [!NOTE]
> As funções de orquestrador são escritas usando código comum, mas existem requisitos rigorosos sobre como escrever o código. Especificamente, o código de função do orquestrador deve ser *determinístico.* O não cumprimento destes requisitos de determinismo pode fazer com que as funções do orquestrador não funcionem corretamente. Informações detalhadas sobre estes requisitos e como trabalhar em torno deles podem ser encontradas no tópico de restrições de [código.](durable-functions-code-constraints.md)

Para obter informações mais detalhadas sobre as funções do orquestrador e as suas características, consulte o artigo [de orquestração durável.](durable-functions-orchestrations.md)

## <a name="activity-functions"></a>Funções de atividade

As funções de atividade são a unidade básica de trabalho numa orquestração de funções duráveis. As funções de atividade são as funções e tarefas que são orquestradas no processo. Por exemplo, pode criar uma função orquestradora para processar uma encomenda. As tarefas envolvem verificar o inventário, cobrar o cliente e criar um envio. Cada tarefa seria uma função de atividade separada. Estas funções de atividade podem ser executadas em série, paralelamente, ou alguma combinação de ambas.

Ao contrário das funções de orquestrador, as funções de atividade não são restritas no tipo de trabalho que se pode fazer nelas. As funções de atividade são frequentemente utilizadas para fazer chamadas de rede ou executar operações intensivas de CPU. Uma função de atividade também pode devolver os dados à função orquestradora. O Quadro de Tarefas Durável garante que cada função de atividade será executada *pelo menos uma vez* durante a execução de uma orquestração.

> [!NOTE]
> Como as funções de atividade só garantem *pelo menos uma vez a* execução, recomendamos que torne a sua função de atividade *idempotente* sempre que possível.

Utilize um [gatilho de atividade](durable-functions-bindings.md#activity-trigger) para definir uma função de atividade. .AS funções .NET recebem um `DurableActivityContext` como parâmetro. Também pode ligar o gatilho a qualquer outro objeto serializável JSON para passar entradas para a função. No JavaScript, pode aceder a uma entrada através da `<activity trigger binding name>` propriedade no [ `context.bindings` objeto.](../functions-reference-node.md#bindings) As funções de atividade só podem ter um único valor passado para elas. Para passar vários valores, deve utilizar tuples, matrizes ou tipos complexos.

> [!NOTE]
> Pode desencadear uma função de atividade apenas a partir de uma função orquestradora.

## <a name="entity-functions"></a>Funções de entidade

As funções da entidade definem operações de leitura e atualização de pequenos pedaços de estado. Muitas vezes nos referimos a estas entidades imponentes como *entidades duráveis.* Tal como as funções de orquestrador, as funções da entidade são funções com um tipo especial de gatilho, *gatilho de entidade*. Também podem ser invocados a partir de funções de cliente ou de funções orquestradoras. Ao contrário das funções de orquestrador, as funções da entidade não têm quaisquer restrições específicas de código. As funções da entidade também gerem o estado explicitamente em vez de representar implicitamente o estado através do fluxo de controlo.

> [!NOTE]
> As funções de entidade e a funcionalidade relacionada só estão disponíveis em Funções Duráveis 2.0 ou superiores.

Para mais informações sobre funções de entidade, consulte o artigo [Entidades Duráveis.](durable-functions-entities.md)

## <a name="client-functions"></a>Funções do cliente

As funções de orquestrador são desencadeadas por uma [ligação de gatilho](durable-functions-bindings.md#orchestration-trigger) de orquestração e as funções de entidade são desencadeadas por uma [entidade que desencadeia a ligação](durable-functions-bindings.md#entity-trigger). Ambos os gatilhos funcionam reagindo a mensagens que são encadeadas num [centro de tarefas.](durable-functions-task-hubs.md) A principal forma de entregar estas mensagens é utilizando uma [ligação de cliente orquestrador](durable-functions-bindings.md#orchestration-client) ou uma [entidade que vincula](durable-functions-bindings.md#entity-client) a partir de uma *função*do cliente . Qualquer função não orquestradora pode ser uma *função do cliente*. Por exemplo, pode ativar o orquestrador a partir de uma função acionada por HTTP, uma função ativada pelo Azure Event Hub, etc. O que torna uma função de *cliente* é o seu uso da ligação de saída do cliente durável.

> [!NOTE]
> Ao contrário de outros tipos de funções, as funções de orquestrador e de entidade não podem ser ativadas diretamente utilizando os botões do Portal Azure. Se pretender testar uma função de orquestrador ou entidade no Portal Azure, deve, em vez disso, executar uma *função de cliente* que inicie uma função de orquestrador ou entidade como parte da sua implementação. Para uma experiência de teste mais simples, recomenda-se uma função *de gatilho manual.*

Além de desencadear funções de orquestrador ou entidade, a ligação duradoura do *cliente* pode ser usada para interagir com orquestrações e entidades em execução. Por exemplo, as orquestrações podem ser questionadas, encerradas e podem ter eventos elevados a eles. Para obter mais informações sobre a gestão de orquestrações e entidades, consulte o artigo [de gestão de Instância.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Passos seguintes

Para começar, crie a sua primeira função durável em [C#](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Ler mais sobre orquestrações de funções duradouras](durable-functions-orchestrations.md)
---
title: Faturação de funções duráveis - Funções Azure
description: Conheça os comportamentos internos das Funções Duráveis e como afetam a faturação das Funções Azure.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74233003"
---
# <a name="durable-functions-billing"></a>Faturação de Funções Duráveis

[As Funções Duráveis](durable-functions-overview.md) são faturadas da mesma forma que as Funções Azure. Para mais informações, consulte o preço das [Funções Azure](https://azure.microsoft.com/pricing/details/functions/).

Ao executar funções de orquestrador no plano de [consumo](../functions-scale.md#consumption-plan)de funções azure, você precisa estar ciente de alguns comportamentos de faturação. As seguintes secções descrevem estes comportamentos e o seu efeito com mais detalhes.

## <a name="orchestrator-function-replay-billing"></a>Faturação de reprodução de função orquestradora

[As funções](durable-functions-orchestrations.md) de orquestrador podem reproduzir-se várias vezes ao longo da vida de uma orquestração. Cada repetição é vista pelo tempo de funcionamento das Funções Azure como uma invocação de função distinta. Por esta razão, no plano de consumo de funções Azure você é cobrado para cada repetição de uma função orquestradora. Outros tipos de planos não cobram para a repetição da função orquestradora.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Esperando e cedendo em funções orquestradoras

Quando uma função orquestradora espera que uma ação assíncrona termine usando **a aguardar** em C# ou **render** em JavaScript, o tempo de execução considera que a execução particular deve ser terminada. A faturação para a função de orquestrador para nessa altura. Não retoma até que a próxima função orquestradora reproduza. Não é cobrado por nenhum tempo passado à espera ou a ceder numa função de orquestrador.

> [!NOTE]
> Funções que chamam outras funções são consideradas por alguns como um antipadrão. Isto deve-se a um problema conhecido como _faturação dupla._ Quando uma função chama outra função diretamente, ambas funcionam ao mesmo tempo. A função chamada está ativamente a funcionar código enquanto a função de chamada aguarda uma resposta. Neste caso, tem de pagar pelo tempo que a função de chamada passa à espera que a função seja executada.
>
> Não há faturação dupla em funções orquestradoras. A faturação de uma função orquestradora para enquanto aguarda o resultado de uma função de atividade ou sub-orquestração.

## <a name="durable-http-polling"></a>Sondagem http durável

As funções de orquestrador podem fazer chamadas http de longa duração para pontos finais externos, conforme descrito no artigo http [features](durable-functions-http-features.md). O método **CallHttpAsync** em C# e o método **callHttp** no JavaScript podem pesquisar internamente um ponto final HTTP enquanto seguem o [padrão assíncrono de 202](durable-functions-http-features.md#http-202-handling).

Atualmente, não existe faturação direta para operações internas de sondagens http. No entanto, as sondagens internas podem fazer com que a função orquestradora reproduza periodicamente. Serão cobrados encargos padrão para estas repetições de funções internas.

## <a name="azure-storage-transactions"></a>Transações de Armazenamento Azure

As Funções Duráveis utilizam o Armazenamento Azure por padrão para manter as mensagens de processo persistentes do estado e gerir divisórias através de arrendamentos blob. Como é dono desta conta de armazenamento, quaisquer custos de transação são cobrados à sua subscrição Azure. Para obter mais informações sobre os artefactos de armazenamento azure utilizados por Funções Duráveis, consulte o [artigo Task hubs](durable-functions-task-hubs.md).

Vários fatores contribuem para os custos reais de Armazenamento Azure incorridos pela sua aplicação Funções Duráveis:

* Uma única aplicação de função está associada a um único centro de tarefas, que partilha um conjunto de recursos de Armazenamento Azure. Estes recursos são utilizados por todas as funções duráveis numa aplicação de função. O número real de funções na aplicação de funções não tem qualquer efeito nos custos de transação de Armazenamento Azure.
* Cada aplicação de funções clica internamente em várias filas na conta de armazenamento usando um algoritmo exponencial de sondagens. Uma aplicação ociosa sondagem as filas com menos frequência do que uma aplicação ativa, o que resulta em menos custos de transação. Para obter mais informações sobre o comportamento das sondagens de fila das Funções Duráveis, consulte a [secção de sondagens de fila do artigo Performance e Escala](durable-functions-perf-and-scale.md#queue-polling).
* Ao executar os planos de consumo de funções azure ou premium, o controlador de [escala Funções Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) vota regularmente todas as filas de centros de tarefas em segundo plano. Se uma aplicação de função estiver sob escala leve a moderada, apenas uma instância de controlador de escala única irá fazer uma sondagem nestas filas. Se a aplicação de função se basear num grande número de casos, poderão ser adicionadas mais instâncias de controlador escamas. Estes casos de controlador de escala adicional podem aumentar os custos totais de transação de fila.
* Cada instância de aplicação de funções compete por um conjunto de arrendamentos blob. Estes casos irão periodicamente fazer chamadas para o serviço Azure Blob, quer para renovar os contratos de arrendamento realizados, quer para tentar adquirir novos contratos de arrendamento. A contagem de partições configurada do centro de tarefas determina o número de arrendamentos blob. A escala para um maior número de instâncias de aplicações de funções provavelmente aumenta os custos de transação de Armazenamento Azure associados a estas operações de locação.

Pode encontrar mais informações sobre os preços do Armazenamento Azure na documentação de preços do [Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/) 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os preços das Funções Azure](https://azure.microsoft.com/pricing/details/functions/)

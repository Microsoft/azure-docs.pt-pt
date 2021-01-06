---
title: Faturação de funções duradouras - Funções Azure
description: Conheça os comportamentos internos das Funções Duradouras e como afetam a faturação das Funções Azure.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 2ec1b080c195a47caafd0120240b5fb61ede062b
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932287"
---
# <a name="durable-functions-billing"></a>Faturação de funções duradouras

[As funções duradouras](durable-functions-overview.md) são faturadas da mesma forma que as Funções Azure. Para obter mais informações, consulte [os preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Ao executar funções de orquestrador no plano de [consumo](../consumption-plan.md)de funções Azure, é necessário estar atento a alguns comportamentos de faturação. As seguintes secções descrevem estes comportamentos e o seu efeito em mais detalhes.

## <a name="orchestrator-function-replay-billing"></a>Função de orquestrador reproduzir faturação

[As funções orquestradoras](durable-functions-orchestrations.md) podem ser repetidas várias vezes ao longo da vida de uma orquestração. Cada repetição é vista pelo tempo de funcionamento das Funções Azure como uma invocação de função distinta. Por esta razão, no plano de consumo de funções Azure você está faturado para cada repetição de uma função orquestradora. Outros tipos de planos não cobram para a repetição da função do orquestrador.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Aguardando e cedendo em funções orquestradoras

Quando uma função orquestradora espera que uma ação assíncrona termine usando **aguarde** em C# ou **rendimento** em JavaScript, o tempo de execução considera que a execução particular deve ser terminada. A faturação para a função de orquestrador para nessa altura. Não retoma até que a próxima função de orquestrador seja repetida. Não é cobrado por nenhum tempo passado à espera ou cedendo numa função de orquestrador.

> [!NOTE]
> As funções que chamam outras funções são consideradas por alguns como um anti-passado. Isto deve-se a um problema conhecido como _faturação dupla._ Quando uma função chama outra função diretamente, ambas funcionam ao mesmo tempo. A função chamada está a executar ativamente o código enquanto a função de chamada aguarda uma resposta. Neste caso, deve pagar o tempo que a função de chamada passa à espera que a função chamada funcione.
>
> Não há faturação dupla nas funções de orquestrador. A faturação de uma função orquestradora para enquanto aguarda o resultado de uma função de atividade ou sub-orquestração.

## <a name="durable-http-polling"></a>Sondagem HTTP durável

As funções de orquestrador podem fazer chamadas HTTP de longa duração para pontos finais externos, conforme descrito no [artigo http features](durable-functions-http-features.md). O método **CallHttpAsync** em C# e o método **callHttp** em JavaScript podem sondar internamente um ponto final HTTP enquanto seguem o [padrão assíncrona de 202](durable-functions-http-features.md#http-202-handling).

Atualmente não há faturação direta para operações internas de sondagens HTTP. No entanto, as sondagens internas podem fazer com que a função do orquestrador se repita periodicamente. Serão cobrados os encargos padrão para estas repetições de funções internas.

## <a name="azure-storage-transactions"></a>Transações de Armazenamento Azure

Funções Duradouras usam o Azure Storage por padrão para manter as mensagens de processo persistentes do estado e gerir divisórias através de locações blob. Como é dono desta conta de armazenamento, quaisquer custos de transação são cobrados à sua subscrição do Azure. Para obter mais informações sobre os artefactos de armazenamento Azure utilizados pelas Funções Duradouras, consulte o [artigo dos centros de tarefas](durable-functions-task-hubs.md).

Vários fatores contribuem para os custos reais de Armazenamento Azure incorridos pela sua aplicação Funções Duradouras:

* Uma única aplicação de função está associada a um único centro de tarefas, que partilha um conjunto de recursos de Armazenamento Azure. Estes recursos são utilizados por todas as funções duráveis numa aplicação de função. O número real de funções na aplicação de funções não tem qualquer efeito nos custos de transação de armazenamento Azure.
* Cada instância de aplicação de função sonda internamente várias filas na conta de armazenamento usando um algoritmo de votação exponencial de backoff. Um caso de aplicação ociosa sonda as filas com menos frequência do que uma aplicação ativa, o que resulta em menos custos de transação. Para obter mais informações sobre o comportamento de votação em filas de trabalho, consulte a [secção de votação em fila do artigo Performance and Scale](durable-functions-perf-and-scale.md#queue-polling).
* Ao executar os planos Azure Functions Consumption ou Premium, o controlador de [escala Azure Functions](../event-driven-scaling.md) sonda regularmente todas as filas do centro de tarefas em segundo plano. Se uma aplicação de função estiver sob escala leve a moderada, apenas uma instância de controlador de escala única irá sondar estas filas. Se a aplicação de função se reduzir para um grande número de casos, podem ser adicionadas instâncias de controlador de escala mais. Estes casos adicionais de controlador de escala podem aumentar os custos totais de transação de filas.
* Cada instância de aplicação de função compete por um conjunto de locações blob. Estes casos farão periodicamente chamadas para o serviço Azure Blob para renovar contratos de arrendamento ou para tentar adquirir novos contratos de arrendamento. A contagem de divisórias configurada do centro de tarefas determina o número de locações blob. A escala para um maior número de instâncias de aplicações de função provavelmente aumenta os custos de transação de armazenamento Azure associados a estas operações de locação.

Pode encontrar mais informações sobre os preços do Azure Storage na [documentação de preços do Azure Storage.](https://azure.microsoft.com/pricing/details/storage/) 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o preço das Funções Azure](https://azure.microsoft.com/pricing/details/functions/)

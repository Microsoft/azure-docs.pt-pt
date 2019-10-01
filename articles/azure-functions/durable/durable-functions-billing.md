---
title: Cobrança de funções duráveis-Azure Functions
description: Saiba mais sobre os comportamentos internos de Durable Functions e como eles afetam a cobrança de Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: f2de6bdf24aa1a0a11349c8f0ec9b3995b026a47
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694890"
---
# <a name="durable-functions-billing"></a>Cobrança de Durable Functions

[Durable Functions](durable-functions-overview.md) é cobrado da mesma maneira que Azure functions. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/).

Ao executar funções de orquestrador no [plano de consumo](../functions-scale.md#consumption-plan)Azure functions, você precisa estar ciente de alguns comportamentos de cobrança. As seções a seguir descrevem esses comportamentos e seus efeitos em mais detalhes.

## <a name="orchestrator-function-replay-billing"></a>Cobrança de reprodução de função de orquestrador

As [funções de orquestrador](durable-functions-orchestrations.md) podem ser reproduzidas várias vezes durante o tempo de vida de uma orquestração. Cada repetição é exibida pelo tempo de execução de Azure Functions como uma invocação de função distinta. Por esse motivo, no plano de consumo de Azure Functions, você será cobrado por cada repetição de uma função de orquestrador. Outros tipos de plano não cobram pela reprodução da função do Orchestrator.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Aguardando e produzindo em funções de orquestrador

Quando uma função de orquestrador aguarda a conclusão de uma ação assíncrona usando **Await** em C# ou **yield** em JavaScript, o tempo de execução considera que a execução específica será concluída. A cobrança da função de orquestrador é interrompida nesse ponto. Ele não é retomado até a próxima reprodução da função de orquestrador. Você não é cobrado por nenhum tempo gasto aguardando ou produzindo em uma função de orquestrador.

> [!NOTE]
> As funções que chamam outras funções são consideradas como um antipadrão. Isso ocorre devido a um problema conhecido como _cobrança dupla_. Quando uma função chama outra função diretamente, ambas são executadas ao mesmo tempo. A função chamada está executando o código ativamente enquanto a função de chamada está aguardando uma resposta. Nesse caso, você deve pagar pelo tempo que a função de chamada gasta aguardando a execução da função chamada.
>
> Não há nenhuma cobrança dupla nas funções de orquestrador. A cobrança de uma função de orquestrador é interrompida enquanto aguarda o resultado de uma função de atividade ou de uma suborquestração.

## <a name="durable-http-polling"></a>Sondagem de HTTP durável

As funções de orquestrador podem fazer chamadas HTTP de longa execução para pontos de extremidade externos, conforme descrito no [artigo recursos http](durable-functions-http-features.md). O método **CallHttpAsync** no C# e o método **callHttp** no JavaScript podem sondar internamente um ponto de extremidade HTTP enquanto segue o [padrão assíncrono 202](durable-functions-http-features.md#http-202-handling).

Atualmente, não há uma cobrança direta para operações internas de sondagem HTTP. No entanto, a sondagem interna pode fazer com que a função de orquestrador reproduza periodicamente. Você será cobrado pelas cobranças padrão por essas repetições de função internas.

## <a name="azure-storage-transactions"></a>Transações de armazenamento do Azure

O Durable Functions usa o armazenamento do Azure por padrão para manter o estado persistente, processar mensagens e gerenciar partições por meio de concessões de BLOB. Como você é proprietário dessa conta de armazenamento, qualquer custo de transação é cobrado em sua assinatura do Azure. Para obter mais informações sobre os artefatos de armazenamento do Azure usados pelo Durable Functions, consulte o [artigo hubs de tarefas](durable-functions-task-hubs.md).

Vários fatores contribuem para os custos reais de armazenamento do Azure incorridos pelo seu aplicativo Durable Functions:

* Um único aplicativo de funções é associado a um único Hub de tarefas, que compartilha um conjunto de recursos de armazenamento do Azure. Esses recursos são usados por todas as funções duráveis em um aplicativo de funções. O número real de funções no aplicativo de funções não tem efeito sobre os custos de transação do armazenamento do Azure.
* Cada instância do aplicativo de funções sonda internamente várias filas na conta de armazenamento usando um algoritmo de sondagem de retirada exponencial. Uma instância de aplicativo ociosa sonda as filas com menos frequência do que um aplicativo ativo, o que resulta em menos custos de transação. Para obter mais informações sobre Durable Functions comportamento de sondagem de fila, consulte a [seção sondagem de fila do artigo desempenho e escala](durable-functions-perf-and-scale.md#queue-polling).
* Durante a execução nos planos de consumo Azure Functions ou Premium, o [controlador de escala de Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) sonda regularmente todas as filas do hub de tarefas em segundo plano. Se um aplicativo de funções estiver sob uma escala moderada, apenas uma única instância do controlador de escala sondará essas filas. Se o aplicativo de funções for dimensionado para um grande número de instâncias, mais instâncias do controlador de escala poderão ser adicionadas. Essas instâncias adicionais do controlador de escala podem aumentar os custos totais da transação de fila.
* Cada instância do aplicativo de funções compete para um conjunto de concessões de BLOB. Essas instâncias farão periodicamente chamadas para o serviço blob do Azure para renovar concessões mantidas ou para tentar adquirir novas concessões. A contagem de partições configuradas do hub de tarefas determina o número de concessões de BLOB. Expandir para um número maior de instâncias do aplicativo de funções provavelmente aumenta os custos de transação do armazenamento do Azure associados a essas operações de concessão.

Você pode encontrar mais informações sobre preços do armazenamento do Azure na documentação de [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) . 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre preços de Azure Functions](https://azure.microsoft.com/pricing/details/functions/)

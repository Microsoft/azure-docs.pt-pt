---
title: Cobrança de funções duráveis-Azure Functions
description: Saiba mais sobre os comportamentos internos das funções duráveis e como elas afetam a cobrança de Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935888"
---
# <a name="durable-functions-billing"></a>Cobrança de Durable Functions

[Durable Functions](durable-functions-overview.md) são cobradas da mesma forma que Azure functions. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/). Ao executar funções de orquestrador no [plano de consumo](../functions-scale.md#consumption-plan)de Azure functions, há alguns comportamentos de cobrança a serem cientes. As seções a seguir descrevem esses comportamentos e seu impacto em mais detalhes.

## <a name="orchestrator-function-replay-billing"></a>Cobrança de reprodução de função de orquestrador

As [funções de orquestrador](durable-functions-orchestrations.md) podem ser reproduzidas várias vezes durante o tempo de vida da orquestração. Cada repetição é exibida pelo tempo de execução de Azure Functions como uma invocação de função distinta. Por esse motivo, no plano de consumo de Azure Functions, você será cobrado por cada repetição da função de orquestrador. Outros tipos de plano não serão cobrados pela reprodução da função do Orchestrator.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Aguardando e produzindo em funções de orquestrador

Quando uma função de orquestrador aguarda a conclusão de uma ação assíncrona `await` usandoC#() `yield` ou (JavaScript), o tempo de execução considera a execução em particular concluída. A cobrança da função de orquestrador é interrompida nesse ponto e não é retomada até a próxima reprodução da função de orquestrador. Você não é cobrado por nenhum tempo gasto aguardando ou produzindo em uma função de orquestrador.

> [!NOTE]
> As funções que chamam outras funções são consideradas como um antipadrão. Isso ocorre devido a um problema conhecido como _cobrança dupla_. Quando uma função chama outra função diretamente, ambas são executadas ao mesmo tempo. O receptor está executando o código ativamente enquanto o chamador está aguardando uma resposta. Nesse caso, você deve pagar pelo tempo que o chamador gasta aguardando a execução do receptor. As funções de orquestrador não sofrem dessa cobrança dupla porque a cobrança da função de orquestrador é interrompida enquanto aguarda o resultado de uma função de atividade (ou suborquestração).

## <a name="durable-http-polling"></a>Sondagem de HTTP durável

As funções de orquestrador podem fazer chamadas HTTP de longa execução para pontos de extremidade externos, conforme descrito no artigo [recursos http](durable-functions-http-features.md) . O `CallHttpAsync` método (C#) e o `callHttp` método (JavaScript) podem sondar internamente um ponto de extremidade http ao seguir o [padrão assíncrono 202](durable-functions-http-features.md#http-202-handling). Atualmente, não há cobrança direta para as operações internas de sondagem HTTP. No entanto, a sondagem interna pode fazer com que a função de orquestrador reproduza periodicamente, e você receberá cobranças padrão por essas repetições de função internas.

## <a name="azure-storage-transactions"></a>Transações de armazenamento do Azure

O Durable Functions usa o armazenamento do Azure por padrão para persistir o estado, processar mensagens e gerenciar partições por meio de concessões de BLOB. Essa conta de armazenamento pertence a você, portanto, os custos de transações são cobrados em sua assinatura do Azure. Para obter mais informações sobre os artefatos de armazenamento do Azure usados pelo Durable Functions, consulte o artigo [hubs de tarefas](durable-functions-task-hubs.md) .

Vários fatores contribuem para os custos reais de armazenamento do Azure incorridos pelo seu aplicativo Durable Functions.

* Um único aplicativo de funções é associado a um único Hub de tarefas, que compartilha um conjunto de recursos de armazenamento do Azure. Esses recursos são usados por todas as funções duráveis em um aplicativo de funções. O número real de funções no aplicativo de funções não tem impacto sobre os custos de transação do armazenamento do Azure.
* Cada instância do aplicativo de funções sonda internamente várias filas na conta de armazenamento usando um algoritmo de sondagem de retirada exponencial. Uma instância de aplicativo ociosa sondará as filas com menos frequência do que um aplicativo ativo, resultando em menos custos de transação. Para obter mais informações sobre Durable Functions comportamento de sondagem de fila, consulte a [seção sondagem de fila do artigo desempenho e escala](durable-functions-perf-and-scale.md#queue-polling) .
* Durante a execução nos planos de consumo Azure Functions ou Premium, o [controlador de escala de Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) sonda regularmente todas as filas do hub de tarefas em segundo plano. Em escala de luz para moderada, apenas uma única instância do controlador de escala sondará essas filas. Se o aplicativo de funções for dimensionado para um grande número de instâncias, mais instâncias do controlador de escala poderão ser adicionadas. Essas instâncias de controlador de escala adicionais podem aumentar os custos de transação de fila total.
* Cada instância do aplicativo de funções compete para um conjunto de concessões de BLOB. Essas instâncias farão periodicamente chamadas para o serviço blob do Azure para renovar as concessões mantidas são tentadas para adquirir novas concessões. O número de concessões de blob é determinado pela contagem de partições configuradas do hub de tarefas. Expandir para um número maior de instâncias do aplicativo de funções provavelmente aumentará os custos de transação do armazenamento do Azure associados a essas operações de concessão.

Mais informações sobre o preço do armazenamento do Azure podem ser encontradas na documentação de [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre preços de Azure Functions](https://azure.microsoft.com/pricing/details/functions/)

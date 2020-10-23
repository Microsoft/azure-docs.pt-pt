---
title: Criar múltiplos gatilhos independentes de funções Azure para Cosmos DB
description: Aprenda a configurar múltiplos gatilhos independentes de Azure Functions para a Cosmos DB para criar arquiteturas orientadas por eventos.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 5be1cfc097da4f1f10bb775c9b20043096b9fb8b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279637"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Criar múltiplos gatilhos de funções Azure para Cosmos DB

Este artigo descreve como pode configurar vários acionadores das Funções do Azure para o Cosmos DB trabalhar em paralelo e reagir a alterações de forma independente.

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Funções baseadas em eventos sem servidor que trabalham com o gatilho de Funções Azure para Cosmos DB e partilhando um recipiente de arrendamento" border="false":::

## <a name="event-based-architecture-requirements"></a>Requisitos de arquitetura baseados em eventos

Ao construir arquiteturas sem servidor com [Funções Azure,](../azure-functions/functions-overview.md) [recomenda-se](../azure-functions/functions-best-practices.md#avoid-long-running-functions) criar pequenos conjuntos de funções que trabalhem em conjunto em vez de grandes funções de longa duração.

À medida que constrói fluxos sem servidor baseados em eventos utilizando o [gatilho Azure Functions para Cosmos DB,](./change-feed-functions.md)você vai encontrar o cenário onde você quer fazer várias coisas sempre que há um novo evento em um [determinado recipiente Azure Cosmos](./account-databases-containers-items.md#azure-cosmos-containers). Se as ações que pretende desencadear, são independentes umas das outras, a solução ideal seria **criar um gatilho de Funções Azure para Cosmos DB por ação** que queira fazer, todos a ouvir mudanças no mesmo contentor Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Otimização de recipientes para múltiplos Gatilhos

Tendo em conta os *requisitos* do gatilho das Funções Azure para a Cosmos DB, precisamos de um segundo contentor para armazenar o estado, também chamado, o *contentor de arrendamento.* Isto significa que precisa de um recipiente de locação separada para cada Função Azure?

Aqui, tem duas opções:

* Criar **um recipiente de locação por função:** Esta abordagem pode traduzir-se em custos adicionais, a menos que utilize uma base de [dados de produção partilhada.](./set-throughput.md#set-throughput-on-a-database) Lembre-se, a potência mínima ao nível do contentor é de 400 [Unidades de Solicitação,](./request-units.md)e no caso do contentor de locação, está apenas a ser usada para pôr em causa o progresso e manter o estado.
* Dispor **de um recipiente de locação e partilhá-lo** para todas as suas Funções: Esta segunda opção faz melhor uso das Unidades de Pedido previstas no contentor, uma vez que permite que várias Funções Azure partilhem e utilizem a mesma produção alojada.

O objetivo deste artigo é guiá-lo para realizar a segunda opção.

## <a name="configuring-a-shared-leases-container"></a>Configurar um recipiente de arrendamento partilhado

Para configurar o recipiente de locações partilhadas, a única configuração extra que precisa de fazer nos seus gatilhos é adicionar o `LeaseCollectionPrefix` [atributo](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) se estiver a utilizar C# ou `leaseCollectionPrefix` [atributo](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) se estiver a utilizar o JavaScript. O valor do atributo deve ser um descritor lógico do que esse gatilho em particular.

Por exemplo, se tiver três Triggers: um que envia e-mails, outro que faz uma agregação para criar uma visão materializada, e outro que envia as alterações para outro armazenamento, para análise posterior, pode atribuir o `LeaseCollectionPrefix` de "e-mails" ao primeiro, "materializado" para o segundo, e "analytics" para o terceiro.

A parte importante é que os três Triggers **podem utilizar a mesma configuração do contentor de locação** (conta, base de dados e nome do recipiente).

Uma amostra de código muito simples usando o `LeaseCollectionPrefix` atributo em C#, seria assim:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

E para o JavaScript, pode aplicar a configuração no `function.json` ficheiro, com o `leaseCollectionPrefix` atributo:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Monitorize sempre as Unidades de Pedido a provisionadas no seu recipiente de locação partilhada. Cada Gatilho que o partilhe, aumentará o consumo médio de produção, pelo que poderá ter de aumentar a produção a provisionada à medida que aumenta o número de Funções Azure que o utilizam.

## <a name="next-steps"></a>Passos seguintes

* Consulte a configuração completa para o [gatilho de Funções Azure para Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Verifique a lista alargada [de amostras](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) para todos os idiomas.
* Visite as receitas serverless com o [repositório Azure](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) Cosmos DB e Azure Functions GitHub para mais amostras.

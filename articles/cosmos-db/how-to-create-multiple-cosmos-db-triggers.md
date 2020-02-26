---
title: Criar múltiplos gatilhos independentes de Funções Azure para cosmos DB
description: Aprenda a configurar múltiplos gatilhos independentes de Funções Azure para a Cosmos DB para criar arquiteturas orientadas para eventos.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 32b680acdee29bf97a0e132fee93d5fee3377245
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604953"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Criar múltiplos gatilhos de funções Azure para cosmos DB

Este artigo descreve como pode configurar vários acionadores das Funções do Azure para o Cosmos DB trabalhar em paralelo e reagir a alterações de forma independente.

![Funções baseadas em eventos servidores que trabalham com o gatilho das Funções Azure para cosmos DB e partilham um recipiente de arrendamento](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Requisitos de arquitetura baseados em eventos

Ao construir arquiteturas sem servidor especados com [funções Azure,](../azure-functions/functions-overview.md)é [aconselhável](../azure-functions/functions-best-practices.md#avoid-long-running-functions) criar pequenos conjuntos de funções que funcionem em conjunto em vez de grandes funções de longo prazo.

À medida que constrói fluxos servidores baseados em eventos utilizando o [gatilho das Funções Azure para cosmos DB,](./change-feed-functions.md)você vai encontrar o cenário em que você quer fazer várias coisas sempre que há um novo evento em um [recipiente azure cosmos.](./databases-containers-items.md#azure-cosmos-containers) Se as ações que pretende desencadear, forem independentes umas das outras, a solução ideal seria criar um gatilho de **Funções Azure para cosmos DB por ação** que queira fazer, todos ouvindo alterações no mesmo recipiente Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Otimização de recipientes para múltiplos Gatilhos

Dadas as *exigências* do gatilho das Funções Azure para a Cosmos DB, precisamos de um segundo contentor para armazenar o estado, também chamado, o contentor de *arrendamento.* Isto significa que precisa de um recipiente de arrendamento separado para cada Função Azure?

Aqui, tem duas opções:

* Crie um recipiente de **arrendamento por Função**: Esta abordagem pode traduzir-se em custos adicionais, a menos que esteja a utilizar uma base de dados de [entrada partilhada](./set-throughput.md#set-throughput-on-a-database). Lembre-se que a entrada mínima ao nível do contentor é de 400 [Unidades de Pedido,](./request-units.md)e no caso do contentor de arrendamento, só está a ser utilizada para fazer um controlo do progresso e manter o estado.
* Disponha de um recipiente de **aluguer e partilhe-o** para todas as suas Funções: Esta segunda opção faz uma melhor utilização das Unidades de Pedido provisionadas no recipiente, uma vez que permite que várias Funções Azure partilhem e utilizem a mesma entrada provisionada.

O objetivo deste artigo é guiá-lo para realizar a segunda opção.

## <a name="configuring-a-shared-leases-container"></a>Configurar um recipiente de arrendamento compartilhado

Para configurar o recipiente de locação partilhada, a única configuração extra que precisa de fazer C# nos seus gatilhos é adicionar o [atributo](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) `LeaseCollectionPrefix` se estiver a utilizar ou `leaseCollectionPrefix` [atributo](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) se estiver a utilizar o JavaScript. O valor do atributo deve ser um descritor lógico do que esse gatilho em particular.

Por exemplo, se tiver três Gatilhos: um que envia e-mails, um que faz uma agregação para criar uma visão materializada, e outro que envia as alterações para outro armazenamento, para análise posterior, pode atribuir o `LeaseCollectionPrefix` de "e-mails" ao primeiro, "materializado" para o segundo, e "análise" ao terceiro.

A parte importante é que os três Gatilhos **podem utilizar a mesma configuração do contentor** de arrendamento (conta, base de dados e nome do contentor).

Uma amostra de código muito simples C#usando o atributo `LeaseCollectionPrefix`, seria assim:

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

E para o JavaScript, pode aplicar a configuração no ficheiro `function.json`, com o `leaseCollectionPrefix` atributo:

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
> Monitorize sempre as Unidades de Pedido disponibilizadas no seu recipiente de arrendamento partilhado. Cada Trigger que o partilha, aumentará o consumo médio de produção, pelo que poderá ter de aumentar a produção prevista à medida que aumenta o número de Funções Azure que o utilizam.

## <a name="next-steps"></a>Passos seguintes

* Consulte a configuração completa para o gatilho das [Funções Azure para Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Verifique a lista alargada [de amostras](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) para todos os idiomas.
* Visite as receitas Sem Servidor com o [repositório](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) Azure Cosmos DB e Azure Functions GitHub para mais amostras.
---
title: Como criar vários gatilhos de Azure Functions independentes para Cosmos DB
description: Saiba como configurar vários gatilhos de Azure Functions independentes para Cosmos DB para criar arquiteturas controladas por eventos.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 987136bf8aba1313e1bef21f58691bf9a860ea32
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093378"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Criar vários gatilhos Azure Functions para Cosmos DB

Este artigo descreve como você pode configurar vários disparadores de Azure Functions para que Cosmos DB funcionem em paralelo e reagir de forma independente às alterações.

![Funções baseadas em evento sem servidor trabalhando com o gatilho de Azure Functions para Cosmos DB e compartilhamento de um contêiner de concessões](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Requisitos de arquitetura baseada em evento

Ao criar arquiteturas sem servidor com o [Azure Functions](../azure-functions/functions-overview.md), é [recomendável](../azure-functions/functions-best-practices.md#avoid-long-running-functions) criar pequenos conjuntos de funções que funcionam juntos em vez de grandes funções de longa execução.

Ao criar fluxos sem servidor com base em eventos usando o [gatilho Azure Functions para Cosmos DB](./change-feed-functions.md), você encontrará o cenário em que deseja fazer várias coisas sempre que houver um novo evento em um determinado [contêiner Cosmos do Azure](./databases-containers-items.md#azure-cosmos-containers). Se as ações que você deseja disparar, são independentes umas das outras, a solução ideal seria **criar um Azure Functions gatilhos para Cosmos dB por ação** que você deseja fazer, todos ouvindo alterações no mesmo contêiner de Cosmos do Azure.

## <a name="optimizing-containers-for-multiple-triggers"></a>Otimizando contêineres para vários gatilhos

Devido aos *requisitos* do gatilho de Azure Functions para Cosmos DB, precisamos de um segundo contêiner para armazenar o estado, também chamado de *contêiner*de concessões. Isso significa que você precisa de um contêiner de concessões separado para cada função do Azure?

Aqui, você tem duas opções:

* Crie **um contêiner de concessões por função**: Essa abordagem pode ser traduzida em custos adicionais, a menos que você esteja usando um [banco de dados de produtividade compartilhado](./set-throughput.md#set-throughput-on-a-database). Lembre-se de que a taxa de transferência mínima no nível do contêiner é de 400 [unidades de solicitação](./request-units.md)e, no caso do contêiner de concessões, ela está sendo usada apenas para verificar o progresso e manter o estado.
* Ter **um contêiner de concessão e compartilhá-lo** para todas as suas funções: Essa segunda opção faz uso melhor das unidades de solicitação provisionadas no contêiner, pois permite que vários Azure Functions compartilhem e usem a mesma taxa de transferência provisionada.

O objetivo deste artigo é orientá-lo a realizar a segunda opção.

## <a name="configuring-a-shared-leases-container"></a>Configurando um contêiner de concessões compartilhadas

Para configurar o contêiner de concessões compartilhadas, a única configuração extra que você precisa fazer em seus gatilhos `LeaseCollectionPrefix` é adicionar o [atributo](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) se C# você `leaseCollectionPrefix` estiver usando o [atributo](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) ou se estiver usando o JavaScript. O valor do atributo deve ser um descritor lógico do que esse gatilho específico.

Por exemplo, se você tiver três gatilhos: um que envia emails, um que faz uma agregação para criar uma exibição materializada e outro que envia as alterações para outro armazenamento, para análise posterior, você pode `LeaseCollectionPrefix` atribuir o de "emails" para o primeiro " materializado "para o segundo, e" Analytics "para o terceiro.

A parte importante é que todos os três gatilhos **podem usar a mesma configuração de contêiner de concessões** (conta, banco de dados e nome do contêiner).

Um exemplo de código muito simples usando `LeaseCollectionPrefix` o atributo C#no, ficaria assim:

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

E para JavaScript, você pode aplicar a configuração no `function.json` arquivo, com o `leaseCollectionPrefix` atributo:

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
> Sempre monitore nas unidades de solicitação provisionadas no contêiner de concessões compartilhadas. Cada gatilho que o compartilha, aumentará o consumo médio da taxa de transferência, de modo que talvez seja necessário aumentar a taxa de transferência provisionada à medida que você aumentar o número de Azure Functions que o está usando.

## <a name="next-steps"></a>Passos Seguintes

* Consulte a configuração completa para o [gatilho de Azure Functions para Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Verifique a lista estendida [de amostras](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) de todos os idiomas.
* Visite as receitas sem servidor com Azure Cosmos DB e Azure Functions [repositório GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) para obter mais exemplos.
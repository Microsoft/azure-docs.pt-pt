---
title: Como criar múltiplos independentes do Azure Cosmos DB Acionadores
description: Saiba como configurar múltiplos independentes do Azure Cosmos DB Acionadores para criar arquiteturas condicionadas por eventos de funções do Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: maquaran
ms.openlocfilehash: 722da9f0112d63af52be8c9c3a746f6da9638bac
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241954"
---
# <a name="create-multiple-azure-cosmos-db-triggers"></a>Criar múltiplos Acionadores de BD do Cosmos do Azure

Este artigo descreve como pode configurar múltiplos Acionadores de BD do Cosmos para trabalhar em paralelo e reagir de forma independente a alterações.

![Funções sem servidor com base em eventos trabalhar com o acionador do Azure Cosmos DB e um contentor de concessões de partilha](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Requisitos de arquitetura baseada em eventos

Durante a criação de arquiteturas sem servidor com [as funções do Azure](../azure-functions/functions-overview.md), tem [recomendado](../azure-functions/functions-best-practices.md#avoid-long-running-functions) para criar conjuntos de função pequeno que funcionam em conjunto em vez de funções de execução demorada grandes.

À medida que cria fluxos sem servidor com base em eventos com o [acionador do Azure Cosmos DB](./change-feed-functions.md), que irá executar para o cenário em que deseja fazer várias coisas, sempre que houver um novo evento num determinado [contentor do Cosmos do Azure](./databases-containers-items.md#azure-cosmos-containers). Se pretender acionar, as ações são independentes entre si, a solução ideal seria **criar um acionador do DB Cosmos por ação** que pretende fazer, tudo escutando as alterações no mesmo contentor do Cosmos do Azure.

## <a name="optimizing-containers-for-multiple-triggers"></a>Otimizando o contentores múltiplos Acionadores

Tendo em conta a *requisitos* do acionador Cosmos DB, precisamos de um segundo contentor para armazenar o estado, também chamado, o *contentor de concessões*. Isso significa que terá um contentor de concessões separado para cada função do Azure?

Aqui, tem duas opções:

* Crie **um obtiver a concessão de contentor por função**: Essa abordagem pode se traduzir em custos adicionais, a menos que esteja a utilizar um [base de dados da taxa de transferência partilhada](./set-throughput.md#set-throughput-on-a-database). Lembre-se que o débito mínimo ao nível do contentor é 400 [unidades de pedido](./request-units.md), e no caso do contentor de concessões, ele é apenas a ser utilizado para o ponto de verificação, o progresso e manter o estado.
* Ter **um contentor da concessão e partilhá-lo** para as suas funções: Essa segunda opção faz melhor uso das unidades de pedido aprovisionadas no contentor, já que permite que várias funções do Azure partilhar e utilizar o mesmo débito aprovisionado.

O objetivo deste artigo é para orientá-lo para realizar a segunda opção.

## <a name="configuring-a-shared-leases-container"></a>Configurando um contêiner de concessões partilhado

Para configurar o contentor de concessões partilhado, uma configuração adicional apenas precisa de fazer no seu acionadores é adicionar o `LeaseCollectionPrefix` [atributo](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) se estiver a utilizar C# ou `leaseCollectionPrefix` [atributo](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example)se estiver a utilizar o JavaScript. O valor do atributo deve ser um descritor de lógico de que esse acionador específico.

Por exemplo, se tiver três Acionadores: um que envia mensagens de correio eletrónico, um que faz uma agregação para criar uma vista materializada e outro que envia as alterações para o armazenamento de outro, para análise posterior, pode atribuir a `LeaseCollectionPrefix` de "mensagens de e-mail" ao primeiro, " materializada"para a segunda e"analytics"para o terceiro.

O que importa é que todos os três Aciona **pode utilizar a mesma configuração de contentor de concessões** (nome de conta, base de dados e de contentor).

Um exemplos de código muito simples com o `LeaseCollectionPrefix` atributo C#, teria o seguinte aspeto:

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

E para JavaScript, pode aplicar a configuração do `function.json` arquivo, com o `leaseCollectionPrefix` atributo:

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
> Monitorize sempre as unidades de pedido aprovisionadas no seu contentor de concessões partilhado. Cada acionador que partilhe o mesmo, irá aumentar o consumo médio de débito, pelo que poderá precisar aumentar o débito aprovisionado, à medida que aumenta o número de funções do Azure que está a utilizar.

## <a name="next-steps"></a>Passos Seguintes

* Consulte a configuração completa para o [acionador do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Verifique o expandida [lista de exemplos](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) para todos os idiomas.
* Visite as receitas sem servidor com o Azure Cosmos DB e as funções do Azure [repositório do GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) para obter mais amostras.
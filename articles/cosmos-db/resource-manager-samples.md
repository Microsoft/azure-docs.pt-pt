---
title: Modelos de Azure Resource Manager para Azure Cosmos DB
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: ecdfe89e899e0d416784ae32a0d66b335c09e2b6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582813"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos de Azure Resource Manager para Azure Cosmos DB

As tabelas a seguir incluem links para modelos de Azure Resource Manager para Azure Cosmos DB:

## <a name="sql-core-api"></a>API de SQL (Core)

|**Modelo**|**Descrição**|
|---|---|
|[Criar uma conta do Azure Cosmos, um banco de dados, um contêiner](manage-sql-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do SQL (núcleo) em duas regiões com dois contêineres com taxa de transferência de banco de dados compartilhada e um contêiner com taxa de transferência dedicada. A taxa de transferência pode ser atualizada reenviando o modelo com o valor da propriedade de produtividade atualizado. |
|[Criar uma conta do Azure Cosmos, um banco de dados e um contêiner com um procedimento armazenado, um gatilho e um UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta de API do SQL (núcleo) em duas regiões com um procedimento armazenado, Trigger e UDF para um contêiner. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, banco de dados, coleção](manage-mongodb-with-resource-manager.md#create-resource) | Este modelo cria uma conta usando Azure Cosmos DB API para MongoDB em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá dois contêineres que compartilham a taxa de transferência no nível do banco de dados. |
|[Atualizar taxa de transferência (RU/s) para um banco de dados](manage-mongodb-with-resource-manager.md#database-ru-update) | Este modelo atualiza a taxa de transferência para um banco de dados em uma conta de API do MongoDB. |
|[Atualizar taxa de transferência (RU/s) para uma coleção](manage-mongodb-with-resource-manager.md#collection-ru-update) | Este modelo atualiza a taxa de transferência para um contêiner em uma conta de API do MongoDB. |

## <a name="cassandra-api"></a>API de Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, keyspace, tabela](manage-cassandra-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do Cassandra em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá duas tabelas que compartilham a taxa de transferência de nível de espaço. |
|[Taxa de transferência de atualização (RU/s) para um keyspace](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Este modelo atualiza a taxa de transferência para um keyspace em uma conta de API do Cassandra. |
|[Atualizar taxa de transferência (RU/s) para uma tabela](manage-cassandra-with-resource-manager.md#table-ru-update) | Este modelo atualiza a taxa de transferência de uma tabela em uma conta de API do Cassandra. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, um banco de dados, um grafo](manage-gremlin-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do Gremlin em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá dois grafos que compartilham a taxa de transferência no nível do banco de dados. |
|[Atualizar taxa de transferência (RU/s) para um banco de dados](manage-gremlin-with-resource-manager.md#database-ru-update) | Este modelo atualiza a taxa de transferência de um banco de dados em uma conta de API do Gremlin. |
|[Atualizar taxa de transferência (RU/s) para um grafo](manage-gremlin-with-resource-manager.md#graph-ru-update) | Este modelo atualiza a taxa de transferência para um grafo em uma conta de API do Gremlin. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API de Tabela em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá uma única tabela. |
|[Atualizar taxa de transferência (RU/s) para uma tabela](manage-table-with-resource-manager.md#table-ru-update) | Este modelo atualiza a taxa de transferência de uma tabela em uma conta de API de Tabela. |

> [!TIP]
> Para habilitar a taxa de transferência compartilhada ao usar API de Tabela, habilite a taxa de transferência no nível da conta no portal do Azure.

Consulte a página de [referência do ARM para Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) para obter a documentação de referência.

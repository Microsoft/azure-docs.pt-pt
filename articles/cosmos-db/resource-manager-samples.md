---
title: Modelos de Azure Resource Manager para Azure Cosmos DB
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7861196daf3b04430200096a0d42bdcc6e6d5cf9
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053228"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos de Azure Resource Manager para Azure Cosmos DB

As tabelas a seguir incluem links para modelos de Azure Resource Manager para Azure Cosmos DB:

## <a name="sql-core-api"></a>API de SQL (Core)

|**Modelo**|**Descrição**|
|---|---|
|[Criar uma conta do Azure Cosmos, um banco de dados, um contêiner](manage-sql-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do SQL (núcleo) em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá dois contêineres que compartilham a taxa de transferência no nível do banco de dados. |
|[Criar uma conta do Azure Cosmos, um banco de dados e um contêiner com um procedimento armazenado, um gatilho e um UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta de API do SQL (núcleo) em duas regiões com um procedimento armazenado, Trigger e UDF para um contêiner. |
|[Atualizar taxa de transferência (RU/s) para um banco de dados](manage-sql-with-resource-manager.md#database-ru-update) | Este modelo atualiza a taxa de transferência de um banco de dados em uma conta de API do SQL (núcleo). |
|[Atualizar taxa de transferência (RU/s) para um contêiner](manage-sql-with-resource-manager.md#container-ru-update) | Este modelo atualiza a taxa de transferência para um contêiner em uma conta de API do SQL (núcleo). |

## <a name="mongodb-api"></a>API MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, banco de dados, coleção](manage-mongodb-with-resource-manager.md#create-resource) | Este modelo cria uma conta usando Azure Cosmos DB API para MongoDB em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá dois contêineres que compartilham a taxa de transferência no nível do banco de dados. |
|[Atualizar taxa de transferência (RU/s) para um banco de dados](manage-mongodb-with-resource-manager.md#database-ru-update) | Este modelo atualiza a taxa de transferência para um banco de dados em uma conta de API do MongoDB. |
|[Atualizar taxa de transferência (RU/s) para uma coleção](manage-mongodb-with-resource-manager.md#collection-ru-update) | Este modelo atualiza a taxa de transferência para um contêiner em uma conta de API do MongoDB. |

## <a name="cassandra-api"></a>API para Cassandra

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

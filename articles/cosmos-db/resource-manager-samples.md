---
title: Modelos de Azure Resource Manager para Azure Cosmos DB
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961860"
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

## <a name="cassandra-api"></a>API de Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, keyspace, tabela](manage-cassandra-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do Cassandra em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá duas tabelas que compartilham a taxa de transferência de nível de espaço. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, um banco de dados, um grafo](manage-gremlin-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API do Gremlin em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá dois grafos que compartilham a taxa de transferência no nível do banco de dados. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API de Tabela em duas regiões com vários mestres habilitados. A conta do Azure Cosmos terá uma única tabela. |

> [!TIP]
> Para habilitar a taxa de transferência compartilhada ao usar API de Tabela, habilite a taxa de transferência no nível da conta no portal do Azure.

Consulte [Azure Resource Manager referência para Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) página para obter a documentação de referência.

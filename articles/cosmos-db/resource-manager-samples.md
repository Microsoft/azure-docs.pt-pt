---
title: Modelos do Azure Resource Manager para o Azure Cosmos DB
description: Utilize modelos Azure Resource Manager para criar e configurar o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969175"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB

As tabelas seguintes incluem ligações para modelos do Azure Resource Manager para o Azure Cosmos DB:

## <a name="sql-core-api"></a>API de SQL (Core)

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Cosmos do Azure, a base de dados, o contentor](manage-sql-with-resource-manager.md#create-resource) | Este modelo cria uma conta da API de SQL (núcleos) em duas regiões com vários mestres ativada. A conta do Cosmos do Azure tem dois contentores que partilhem o débito de nível de base de dados. |
|[Atualizar o débito (RU/s) para uma base de dados](manage-sql-with-resource-manager.md#database-ru-update) | Este modelo atualiza a taxa de transferência para uma base de dados numa conta de API do SQL (núcleos). |
|[Atualizar o débito (RU/s) para um contentor](manage-sql-with-resource-manager.md#container-ru-update) | Este modelo atualiza a taxa de transferência para um contentor numa conta de API do SQL (núcleos). |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Cosmos do Azure, a base de dados, a coleção](manage-mongodb-with-resource-manager.md#create-resource) | Este modelo cria uma conta com a API do Azure Cosmos DB para o MongoDB em duas regiões com vários mestres ativada. A conta do Cosmos do Azure tem dois contentores que partilhem o débito de nível de base de dados. |
|[Atualizar o débito (RU/s) para uma base de dados](manage-mongodb-with-resource-manager.md#database-ru-update) | Este modelo atualiza a taxa de transferência para uma base de dados numa conta de API do MongoDB. |
|[Atualizar o débito (RU/s) de uma coleção](manage-mongodb-with-resource-manager.md#collection-ru-update) | Este modelo atualiza a taxa de transferência para um contentor numa conta de API do MongoDB. |

## <a name="cassandra-api"></a>API de Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Cosmos do Azure, o keyspace, a tabela](manage-cassandra-with-resource-manager.md#create-resource) | Este modelo cria uma conta de API para Cassandra em duas regiões com vários mestres ativada. A conta do Cosmos do Azure terão duas tabelas que partilham o débito de nível de keyspace. |
|[Atualizar o débito (RU/s) para um keyspace](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Este modelo atualiza a taxa de transferência para um keyspace numa conta da API de Cassandra. |
|[Atualizar o débito (RU/s) para uma tabela](manage-cassandra-with-resource-manager.md#table-ru-update) | Este modelo atualiza a taxa de transferência para uma tabela numa conta da API de Cassandra. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Cosmos do Azure, a base de dados, o gráfico](manage-gremlin-with-resource-manager.md#create-resource) | Este modelo cria uma conta da API do Gremlin em duas regiões com vários mestres ativada. A conta do Cosmos do Azure tem dois gráficos que partilham o débito de nível de base de dados. |
|[Atualizar o débito (RU/s) para uma base de dados](manage-gremlin-with-resource-manager.md#database-ru-update) | Este modelo atualiza a taxa de transferência para uma base de dados numa conta de API do Gremlin. |
|[Débito (RU/s) para um gráfico de atualização](manage-gremlin-with-resource-manager.md#graph-ru-update) | Este modelo atualiza a taxa de transferência para um gráfico numa conta da Gremlin API. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta do Cosmos do Azure, tabela](manage-table-with-resource-manager.md#create-resource) | Este modelo cria uma conta da API de tabela em duas regiões com vários mestres ativada. A conta do Cosmos do Azure terão uma única tabela. |
|[Atualizar o débito (RU/s) para uma tabela](manage-table-with-resource-manager.md#table-ru-update) | Este modelo atualiza a taxa de transferência para uma tabela numa conta de API de tabela. |

> [!TIP]
> Para ativar a taxa de transferência partilhada ao utilizar a API de tabela, ative ao nível da conta de débito no Portal do Azure.

Ver [referência ARM para o Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) página para a documentação de referência.
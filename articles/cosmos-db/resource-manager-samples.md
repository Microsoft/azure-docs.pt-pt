---
title: Azure Resource Manager templates for Azure Cosmos DB (Modelos do Azure Resource Manager para o Azure Cosmos DB)
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390883"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager templates for Azure Cosmos DB (Modelos do Azure Resource Manager para o Azure Cosmos DB)

As seguintes tabelas incluem links para modelos de Gestor de Recursos Azure para Azure Cosmos DB:

## <a name="sql-core-api"></a>API de SQL (Core)

|**Modelo**|**Descrição**|
|---|---|
|[Criar uma conta Azure Cosmos, base de dados, contentor](manage-sql-with-resource-manager.md#create-resource) | Este modelo cria uma conta API SQL (Core) em duas regiões com dois recipientes com entrada de base de dados partilhada e um recipiente com entrada dedicada. A entrada pode ser atualizada resubmetendo o modelo com o valor de propriedade de entrada atualizado. |
|[Criar uma conta Azure Cosmos, base de dados e contentor com um procedimento armazenado, gatilho e UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta API SQL (Core) em duas regiões com um procedimento armazenado, gatilho e UDF para um recipiente. |
|[Criar um ponto final privado para uma conta azure cosmos existente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto final privado para uma conta API Azure Cosmos SQL existente numa rede virtual existente. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, base de dados, recolha](manage-mongodb-with-resource-manager.md#create-resource) | Este modelo cria uma conta utilizando a API Db Azure Cosmos para MongoDB em duas regiões com multi-master habilitado. A conta Azure Cosmos terá dois contentores que partilham o serviço de base de dados. |

## <a name="cassandra-api"></a>API de Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, espaço de chave, mesa](manage-cassandra-with-resource-manager.md#create-resource) | Este modelo cria uma conta Cassandra API em duas regiões com multi-mestrado habilitado. A conta Azure Cosmos terá duas tabelas que partilham a entrada de teclado ao nível do espaço. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, base de dados, gráfico](manage-gremlin-with-resource-manager.md#create-resource) | Este modelo cria uma conta Gremlin API em duas regiões com multi-master habilitado. A conta Azure Cosmos terá dois gráficos que partilham o nível de base de dados. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Este modelo cria uma conta API de tabela em duas regiões com multi-master habilitado. A conta Azure Cosmos terá uma única mesa. |

> [!TIP]
> Para permitir a entrada partilhada ao utilizar a API do quadro, ative a entrada ao nível da conta no Portal Azure.

Consulte a referência do Gestor de Recursos Azure para a página [D do Azure Cosmos](/azure/templates/microsoft.documentdb/allversions) para a documentação de referência.

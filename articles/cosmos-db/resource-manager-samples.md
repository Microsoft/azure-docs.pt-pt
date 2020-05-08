---
title: Azure Resource Manager templates for Azure Cosmos DB (Modelos do Azure Resource Manager para o Azure Cosmos DB)
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791634"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager templates for Azure Cosmos DB (Modelos do Azure Resource Manager para o Azure Cosmos DB)

As seguintes tabelas incluem links para modelos de Gestor de Recursos Azure para Azure Cosmos DB:

## <a name="core-sql-api"></a>API de Núcleo (SQL)

|**Modelo**|**Descrição**|
|---|---|
|[Criar uma conta Azure Cosmos, base de dados, recipiente com entrada em escala automática](manage-sql-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta Core (SQL) API em duas regiões, uma base de dados e um recipiente com entrada em escala automática. |
|[Criar uma conta Azure Cosmos, base de dados, recipiente com entrada padrão (manual)](manage-sql-with-resource-manager.md#create-manual) | Este modelo cria uma conta Core (SQL) API em duas regiões, uma base de dados e um recipiente com entrada padrão. |
|[Criar uma conta Azure Cosmos, base de dados e contentor com um procedimento armazenado, gatilho e UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta Core (SQL) API em duas regiões com um procedimento armazenado, gatilho e UDF para um recipiente. |
|[Criar um ponto final privado para uma conta azure cosmos existente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto final privado para uma conta API Azure Cosmos Core (SQL) existente numa rede virtual existente. |
|[Criar uma conta Azure Cosmos de nível livre](manage-sql-with-resource-manager.md#free-tier) |  Este modelo cria uma conta API Azure Cosmos DB Core (SQL) em nível livre. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, base de dados, recolha com produção de escala automática](manage-mongodb-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta utilizando a API Db Azure Cosmos para MongoDB em duas regiões com dois recipientes que partilham a produção automática de nível de base de dados. |
|[Criar uma conta Azure Cosmos, base de dados, recolha com entrada padrão (manual)](manage-mongodb-with-resource-manager.md#create-manual) | Este modelo cria uma conta utilizando a API Db Azure Cosmos para MongoDB em duas regiões com dois recipientes que partilham a entrada padrão de nível de base de dados. |

## <a name="cassandra-api"></a>API de Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, espaço de chave, tabela com entrada em escala automática](manage-cassandra-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta Cassandra API em duas regiões com um espaço de chave e tabela com entrada em escala automática. |
|[Criar uma conta Azure Cosmos, espaço de chave, tabela com entrada padrão (manual)](manage-cassandra-with-resource-manager.md#create-manual) | Este modelo cria uma conta Cassandra API em duas regiões com um espaço de chave e tabela com entrada manual. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, base de dados, gráfico com produção de escala automática](manage-gremlin-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta Gremlin API em duas regiões com uma base de dados e gráfico com entrada em escala automática. |
|[Criar uma conta Azure Cosmos, base de dados, gráfico com entrada padrão (manual)](manage-gremlin-with-resource-manager.md#create-manual) | Este modelo cria uma conta Gremlin API em duas regiões com uma base de dados e gráfico com entrada padrão. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, tabela com entrada em escala automática](manage-table-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta API de tabela em duas regiões e uma única tabela com entrada em escala automática. |
|[Criar uma conta Azure Cosmos, tabela com entrada padrão (manual)](manage-table-with-resource-manager.md#create-manual) | Este modelo cria uma conta API de tabela em duas regiões e uma única tabela com entrada padrão. |

Consulte a referência do Gestor de Recursos Azure para a página [D do Azure Cosmos](/azure/templates/microsoft.documentdb/allversions) para a documentação de referência.

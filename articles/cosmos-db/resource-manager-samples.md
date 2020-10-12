---
title: Azure Resource Manager templates for Azure Cosmos DB (Modelos do Azure Resource Manager para o Azure Cosmos DB)
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar a Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83586155"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager templates for Azure Cosmos DB (Modelos do Azure Resource Manager para o Azure Cosmos DB)

As seguintes tabelas incluem links para modelos Azure Resource Manager para Azure Cosmos DB:

## <a name="core-sql-api"></a>API de Núcleo (SQL)

|**Modelo**|**Descrição**|
|---|---|
|[Criar uma conta Azure Cosmos, base de dados, contentor com produção de escala automática](manage-sql-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta API Core (SQL) em duas regiões, uma base de dados e um contentor com produção de escala automática. |
|[Criar uma conta Azure Cosmos, base de dados, recipiente com loja analítica](manage-sql-with-resource-manager.md#create-analytical-store) | Este modelo cria uma conta API core (SQL) numa região com um recipiente configurado com TTL Analytical habilitado e opção de utilização manual ou de escala automática. |
|[Criar uma conta Azure Cosmos, base de dados, recipiente com produção padrão (manual)](manage-sql-with-resource-manager.md#create-manual) | Este modelo cria uma conta API core (SQL) em duas regiões, uma base de dados e um recipiente com produção padrão. |
|[Criar uma conta, base de dados e recipiente Azure Cosmos com um procedimento armazenado, gatilho e UDF](manage-sql-with-resource-manager.md#create-sproc) | Este modelo cria uma conta API core (SQL) em duas regiões com um procedimento armazenado, gatilho e UDF para um recipiente. |
|[Criar um ponto final privado para uma conta Azure Cosmos existente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto final privado para uma conta API AZure Cosmos Core (SQL) existente numa rede virtual existente. |
|[Criar uma conta Azure Cosmos de nível livre](manage-sql-with-resource-manager.md#free-tier) |  Este modelo cria uma conta API Azure Cosmos DB Core (SQL) em nível livre. |

## <a name="mongodb-api"></a>API do MongoDB

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, base de dados, coleção com produção de escala automática](manage-mongodb-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta usando Azure Cosmos DB API para MongoDB em duas regiões com dois contentores que partilham o nível de base de dados de potência. |
|[Criar uma conta Azure Cosmos, base de dados, coleção com produção padrão (manual)](manage-mongodb-with-resource-manager.md#create-manual) | Este modelo cria uma conta usando Azure Cosmos DB API para MongoDB em duas regiões com dois recipientes que partilham o nível padrão de base de dados. |

## <a name="cassandra-api"></a>API de Cassandra

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, espaço-chave, tabela com produção de escala automática](manage-cassandra-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta API Cassandra em duas regiões com um espaço-chave e tabela com produção de escala automática. |
|[Criar uma conta Azure Cosmos, espaço-chave, tabela com produção padrão (manual)](manage-cassandra-with-resource-manager.md#create-manual) | Este modelo cria uma conta API Cassandra em duas regiões com um espaço-chave e tabela com produção manual. |

## <a name="gremlin-api"></a>API do Gremlin

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, base de dados, gráfico com produção de escala automática](manage-gremlin-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta API gremlin em duas regiões com uma base de dados e gráfico com produção de escala automática. |
|[Criar uma conta Azure Cosmos, base de dados, gráfico com produção padrão (manual)](manage-gremlin-with-resource-manager.md#create-manual) | Este modelo cria uma conta API gremlin em duas regiões com uma base de dados e gráfico com produção padrão. |

## <a name="table-api"></a>API de Tabela

|**Modelo**|**Descrição**|
|---| ---|
|[Criar uma conta Azure Cosmos, tabela com produção de escala automática](manage-table-with-resource-manager.md#create-autoscale) | Este modelo cria uma conta API de tabela em duas regiões e uma única tabela com produção de escala automática. |
|[Criar uma conta Azure Cosmos, tabela com produção padrão (manual)](manage-table-with-resource-manager.md#create-manual) | Este modelo cria uma conta API de tabela em duas regiões e uma única tabela com produção padrão. |

Consulte a referência do Gestor de Recursos Azure para a página DB da [Azure Cosmos](/azure/templates/microsoft.documentdb/allversions) para obter a documentação de referência.

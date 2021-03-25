---
title: Modelos de Gestor de Recursos Azure para Azure Cosmos DB Core (SQL API)
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar a Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: 7163658024d150a7c5d75c3b3ac0b6b6b29cd3cb
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105037313"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager templates for Azure Cosmos DB (Modelos do Azure Resource Manager para o Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo apenas mostra exemplos de modelo do Azure Resource Manager para contas API Core (SQL). Também pode encontrar exemplos de modelos para [Cassandra,](templates-samples-cassandra.md) [Gremlin,](templates-samples-gremlin.md) [MongoDB](templates-samples-mongodb.md)e [APIs de tabela.](templates-samples-table.md)

## <a name="core-sql-api"></a>API de Núcleo (SQL)

|**Modelo**|**Descrição**|
|---|---|
|[Criar uma conta Azure Cosmos, base de dados, contentor com produção de escala automática](manage-with-templates.md#create-autoscale) | Este modelo cria uma conta API Core (SQL) em duas regiões, uma base de dados e um contentor com produção de escala automática. |
|[Criar uma conta Azure Cosmos, base de dados, recipiente com loja analítica](manage-with-templates.md#create-analytical-store) | Este modelo cria uma conta API core (SQL) numa região com um recipiente configurado com TTL Analytical habilitado e opção de utilização manual ou de escala automática. |
|[Criar uma conta Azure Cosmos, base de dados, recipiente com produção padrão (manual)](manage-with-templates.md#create-manual) | Este modelo cria uma conta API core (SQL) em duas regiões, uma base de dados e um recipiente com produção padrão. |
|[Criar uma conta, base de dados e recipiente Azure Cosmos com um procedimento armazenado, gatilho e UDF](manage-with-templates.md#create-sproc) | Este modelo cria uma conta API core (SQL) em duas regiões com um procedimento armazenado, gatilho e UDF para um recipiente. |
|[Criar uma conta Azure Cosmos com identidade AD Azure, Definições de Função e Atribuição de Funções](manage-with-templates.md#create-rbac) | Este modelo cria uma conta API core (SQL) com identidade AD AZure, Definições de Função e Atribuição de Função num Diretor de Serviço. |
|[Criar um ponto final privado para uma conta Azure Cosmos existente](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto final privado para uma conta API AZure Cosmos Core (SQL) existente numa rede virtual existente. |
|[Criar uma conta Azure Cosmos de nível livre](manage-with-templates.md#free-tier) |  Este modelo cria uma conta API Azure Cosmos DB Core (SQL) em nível livre. |

Consulte a referência do Gestor de Recursos Azure para a página DB da [Azure Cosmos](/azure/templates/microsoft.documentdb/allversions) para obter a documentação de referência.

---
title: Modelos do Azure Resource Manager para o Azure Cosmos DB
description: Utilize modelos Azure Resource Manager para criar e configurar o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078464"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB

A tabela seguinte inclui ligações para modelos Azure Resource Manager para o Azure Cosmos DB:

|**Tipo de API** | **Ligue ao exemplo**| **Descrição** |
|---|---| ---|
|API Core (SQL)| [Criar uma conta do Azure Cosmos DB (com vários mestres)](manage-sql-with-resource-manager.md) | Este modelo cria uma conta da API de SQL em duas regiões com vários mestres ativada. A conta do Cosmos do Azure tem dois contentores que partilhem o débito de nível de base de dados. |
| API do MongoDB | [Criar uma conta do Azure Cosmos DB (com vários mestres)](manage-mongodb-with-resource-manager.md) | Este modelo cria uma conta com a API do Azure Cosmos DB para o MongoDB em duas regiões com vários mestres ativada. A conta do Cosmos do Azure tem dois contentores que partilhem o débito de nível de base de dados. |
| API de Cassandra | [Criar uma conta do Azure Cosmos DB (com vários mestres)](manage-cassandra-with-resource-manager.md) | Este modelo cria uma conta de API para Cassandra em duas regiões com vários mestres ativada. A conta do Cosmos do Azure terão duas tabelas que partilham o débito de nível de keyspace. |
| API do Gremlin| [Criar uma conta do Azure Cosmos DB (com vários mestres)](manage-gremlin-with-resource-manager.md) | Este modelo cria uma conta da API do Gremlin em duas regiões com vários mestres ativada. A conta do Cosmos do Azure tem dois gráficos que partilham o débito de nível de base de dados. |
| API de Tabela | [Criar uma conta do Azure Cosmos DB (com vários mestres)](manage-table-with-resource-manager.md) | Este modelo cria uma conta da API de tabela em duas regiões com vários mestres ativada. A conta do Cosmos do Azure terão uma única tabela. |

> [!TIP]
> Para ativar a taxa de transferência partilhada ao utilizar a API de tabela, ative ao nível da conta de débito no Portal do Azure.

Ver [referência ARM para o Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) página para a documentação de referência.
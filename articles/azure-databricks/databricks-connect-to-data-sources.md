---
title: 'Conectar-se a diferentes fontes de dados de Azure Databricks '
description: Saiba como se conectar ao banco de dados SQL do Azure, Azure Data Lake Store, armazenamento de BLOBs, Cosmos DB, hubs de eventos e SQL Data Warehouse do Azure do Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: f40edec088172bf7460d99b611e6be80f818ff9c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601896"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Conectar-se a fontes de dados de Azure Databricks

Este artigo fornece links para todas as diferentes fontes de dados no Azure que podem ser conectadas a Azure Databricks. Siga os exemplos nestes links para extrair dados das fontes de dados do Azure (por exemplo, armazenamento de BLOBs do Azure, hubs de eventos do Azure, etc.) em um cluster Azure Databricks e executar trabalhos analíticos neles. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um espaço de trabalho Azure Databricks e um cluster Spark. Siga as instruções em introdução [ao Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Fontes de dados para Azure Databricks

A lista a seguir fornece as fontes de dados no Azure que você pode usar com Azure Databricks. Para obter uma lista completa de fontes de dados que podem ser usadas com Azure Databricks, consulte [fontes de dados para Azure Databricks](/azure/databricks/data/data-sources/index.html).

- [Banco de dados SQL do Azure](/azure/databricks/data/data-sources/sql-databases.html)

    Este link fornece a API dataframe para se conectar a bancos de dados SQL usando JDBC e como controlar o paralelismo de leituras por meio da interface JDBC. Este tópico fornece exemplos detalhados usando a API escalar, com exemplos abreviados do Python e do Spark SQL no final.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2.html)

    Este link fornece exemplos de como usar a entidade de serviço Azure Active Directory para autenticar com Azure Data Lake Storage. Ele também fornece instruções sobre como acessar os dados em Azure Data Lake Storage de Azure Databricks.

- [Armazenamento de Blobs do Azure](/azure/databricks/data/data-sources/azure/azure-storage.html)

    Este link fornece exemplos de como acessar diretamente o armazenamento de BLOBs do Azure de Azure Databricks usando a chave de acesso ou a SAS para um determinado contêiner. O link também fornece informações sobre como acessar o armazenamento de BLOBs do Azure de Azure Databricks usando a API RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector.html)

    Este link fornece instruções sobre como usar o [conector Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) do Azure Databricks para acessar dados no Azure Cosmos DB.

- [Azure Event Hubs](/azure/databricks/data/data-sources/azure/eventhubs-connector.html)

    Este link fornece instruções sobre como usar o [conector Spark dos hubs de eventos do Azure](https://github.com/Azure/azure-event-hubs-spark) da Azure Databricks para acessar dados nos hubs de eventos do Azure.

- [Azure SQL Data Warehouse](/azure/databricks/data/data-sources/azure/sql-data-warehouse.html)

    Este link fornece instruções sobre como usar o conector de SQL Data Warehouse do Azure para se conectar de Azure Databricks.
    

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre fontes de onde você pode importar dados para Azure Databricks, consulte [fontes de dados para Azure Databricks](/azure/databricks/data/data-sources/index.html#).



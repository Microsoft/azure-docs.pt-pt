---
title: 'Ligar-se a diferentes fontes de dados da Azure Databricks '
description: Saiba como ligar-se à Azure SQL Database, Azure Data Lake Store, armazenamento de bolhas, Cosmos DB, Event Hubs e Azure SQL Data Warehouse da Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 2b6d234c3845c220fd63c24db5bdbdae0f7c63fd
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255080"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Ligue-se a fontes de dados da Azure Databricks

Este artigo fornece ligações a todas as diferentes fontes de dados em Azure que podem ser ligadas a Azure Databricks. Siga os exemplos nestas ligações para extrair dados das fontes de dados do Azure (por exemplo, Azure Blob Storage, Azure Event Hubs, etc.) para um cluster Azure Databricks, e executar empregos analíticos neles. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um espaço de trabalho Azure Databricks e um cluster Spark. Siga as instruções da [Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Fontes de dados para Azure Databricks

A lista seguinte fornece as fontes de dados em Azure que pode utilizar com a Azure Databricks. Para obter uma lista completa de fontes de dados que podem ser utilizadas com Azure Databricks, consulte [fontes de dados para Azure Databricks](/azure/databricks/data/data-sources/index).

- [Base de Dados SQL do Azure](/azure/databricks/data/data-sources/sql-databases)

    Este link fornece a API dataFrame para ligação à Base de Dados SQL usando JDBC e como controlar o paralelismo das leituras através da interface JDBC. Este tópico fornece exemplos detalhados usando a API scala, com exemplos de Python e Spark SQL abreviados no final.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Este link fornece exemplos sobre como usar o diretor do serviço Azure Ative Directory para autenticar com o Azure Data Lake Storage. Também fornece instruções sobre como aceder aos dados no Azure Data Lake Storage a partir de Azure Databricks.

- [Armazenamento de Blobs do Azure](/azure/databricks/data/data-sources/azure/azure-storage)

    Este link fornece exemplos sobre como aceder diretamente ao Azure Blob Storage a partir de Azure Databricks usando a chave de acesso ou o SAS para um dado recipiente. O link também fornece informações sobre como aceder ao Azure Blob Storage a partir de Azure Databricks usando a API RDD.

- [BD do Cosmos para o Azure](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Este link fornece instruções sobre como usar o [conector Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) da Azure Databricks para aceder a dados em Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Este link fornece instruções sobre como usar o [conector Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) da Azure Databricks para aceder a dados em Azure Event Hubs.

- [Azure SQL Data Warehouse](/azure/synapse-analytics/sql-data-warehouse/)

    Este link fornece instruções sobre como usar o conector Azure SQL Data Warehouse para ligar a partir de Azure Databricks.
    

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as fontes de onde pode importar dados em Azure Databricks, consulte [fontes de dados da Azure Databricks](/azure/databricks/data/data-sources/index).



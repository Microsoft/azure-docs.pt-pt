---
title: 'Ligar a origens de dados diferentes do Azure Databricks '
description: Saiba como ligar a origens de dados do Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: f2b7136ec21416e31c2af658974577023a4494de
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997939"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Ligar a origens de dados do Azure Databricks

Este artigo fornece ligações para todas as origens de dados diferentes no Azure que podem ser ligadas ao Azure Databricks. Siga os exemplos estas ligações para extrair dados das origens de dados do Azure (por exemplo, armazenamento de Blobs do Azure, os Hubs de eventos do Azure, etc.) para um cluster do Azure Databricks e executar tarefas analíticas nos mesmos. 

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter uma área de trabalho do Azure Databricks e um cluster do Spark. Siga as instruções em [introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Origens de dados para o Azure Databricks

A lista seguinte apresenta as origens de dados no Azure que pode utilizar com o Azure Databricks. Para obter uma lista completa das origens de dados que podem ser utilizadas com o Azure Databricks, veja [origens de dados para o Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Base de dados SQL do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Este link fornece a API de pacote de dados para ligar a bases de dados do SQL usando o JDBC e como controlar o paralelismo de leituras por meio da interface JDBC. Este tópico fornece exemplos detalhados, com a API de Scala, Python abreviado e exemplos de Spark SQL no final.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Esta ligação fornece exemplos sobre como utilizar o principal de serviço do Azure Active Directory para autenticar com o Data Lake Store. Ele também fornece instruções sobre como aceder os dados no Data Lake Store a partir do Azure Databricks.

- [Armazenamento de Blobs do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Este link fornece exemplos sobre como aceder diretamente ao armazenamento de Blobs do Azure do Azure Databricks com a chave de acesso ou a SAS para um determinado contêiner. A ligação também fornece informações sobre como acessar o armazenamento de Blobs do Azure a partir do Azure Databricks com a API de RDD.

- [BD do Cosmos para o Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Esta ligação fornece instruções sobre como utilizar o [conector do Spark do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) do Azure Databricks para aceder a dados no Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Esta ligação fornece instruções sobre como utilizar o [conector do Spark de Hubs de eventos do Azure](https://github.com/Azure/azure-event-hubs-spark) do Azure Databricks para aceder a dados nos Hubs de eventos do Azure.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Esta ligação fornece instruções sobre como utilizar o conector Azure SQL Data Warehouse para ligar a partir do Azure Databricks.
    

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as origens de onde pode importar dados para o Azure Databricks, veja [origens de dados para o Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).



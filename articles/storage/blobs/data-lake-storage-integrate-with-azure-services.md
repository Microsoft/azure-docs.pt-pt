---
title: Integrar Azure Data Lake Storage com os serviços do Azure | Microsoft Docs
description: Saiba mais sobre quais serviços do Azure se integram com o Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: de57776ea3bcc7486c5c26182c7e2a65e643d81c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863007"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Integrar Azure Data Lake Storage com os serviços do Azure

Você pode usar os serviços do Azure para ingerir dados, executar análises e criar representações visuais. Este artigo fornece uma lista de serviços do Azure com suporte e fornece links para artigos que ajudam você a usar esses serviços com Azure Data Lake Storage Gen2.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2

A tabela a seguir lista os serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2.

| Serviço do Azure |  Artigos relacionados |
|---------------|-------------------|
|Azure Data Factory | [Carregar dados em Azure Data Lake Storage Gen2 com Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Usar com Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Início rápido: analisar dados em Azure Data Lake Storage Gen2 usando Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutorial: extrair, transformar e carregar dados usando Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: acessar dados de Data Lake Storage Gen2 com Azure Databricks usando o Spark](data-lake-storage-use-databricks-spark.md) |
|Captura de hubs de eventos do Azure| [Capturar eventos por meio dos hubs de eventos do Azure no armazenamento de BLOBs do Azure ou Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [Visão geral – o que são os aplicativos lógicos do Azure?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Acessar dados nos serviços de armazenamento do Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Pesquisa Cognitiva do Azure | [Indexar e Pesquisar Azure Data Lake Storage Gen2 documentos (visualização)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Stream Analytics| [Início rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Saída para Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Usar Azure Data Box para migrar dados de um repositório HDFS local para o armazenamento do Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Usando a CLI do HDFS com Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: extrair, transformar e carregar dados usando Apache Hive no Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|Hub IoT | [Usar o roteamento de mensagens do Hub IoT para enviar mensagens do dispositivo para a nuvem para diferentes pontos de extremidade](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Analisar dados em Data Lake Storage Gen2 usando Power BI](data-lake-storage-use-power-bi.md) |
|Data Warehouse SQL | [Usar com o Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|o SQL Server Integration Services (SSIS) | [Gerenciador de conexões do armazenamento do Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre quais ferramentas você pode usar para processar dados no data Lake. Consulte [usando Azure data Lake Storage Gen2 para Big data requisitos](data-lake-storage-data-scenarios.md).

- Saiba mais sobre o Data Lake Storage Gen2 e como começar a usar. Consulte [introdução ao Azure data Lake Storage Gen2](data-lake-storage-introduction.md)

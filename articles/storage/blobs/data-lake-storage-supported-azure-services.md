---
title: Serviços Azure que suportam Azure Data Lake Storage Gen2 [ Microsoft Docs
description: Saiba quais os serviços azure integrados com o Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 0cf8a4a78894cb7f0e1ddcf7aa18bbb4006bbeeb
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345641"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Serviços Azure que suportam Azure Data Lake Storage Gen2

Pode utilizar os serviços Azure para ingerir dados, realizar análises e criar representações visuais. Este artigo fornece uma lista de serviços Azure suportados, divulga o seu nível de suporte, e fornece-lhe links para artigos que o ajudam a usar estes serviços com o Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Serviços Azure suportados

Esta tabela lista os serviços Azure que pode utilizar com o Azure Data Lake Storage Gen2. Os itens que aparecem nestas tabelas vão mudar ao longo do tempo à medida que o apoio continua a expandir-se.

> [!NOTE]
> O nível de suporte refere-se apenas à forma como o serviço é suportado com data Lake Storage Gen 2.

|Serviço do Azure |Nível de apoio |Artigos relacionados |
|---------------|-------------------|---|
|Azure Data Factory|Disponível em Geral|[Carregue dados no Armazenamento de Lagos Azure Data Gen2 com fábrica de dados azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponível em Geral|[Utilizar com o Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Quickstart: Analise dados no Azure Data Lake Storage Gen2 utilizando tijolos de dados Azure](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutorial: Extrair, transformar e carregar dados utilizando os Databricks Azure](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: Aceder dados de armazenamento de dados do lago Gen2 com tijolos de dados Azure usando spark](data-lake-storage-use-databricks-spark.md)|
|Captura de Hubs de Eventos Azure| Disponível em Geral|[Capture eventos através de Hubs de Eventos Azure em Armazenamento De Blob Azure ou Armazenamento de Lago de Dados Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps|Disponível em Geral|[Visão geral - O que é Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Disponível em Geral|[Dados de acesso nos serviços de armazenamento do Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Disponível em Geral|[Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Egress para Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| Disponível em Geral|[Utilize a Caixa de Dados Azure para migrar dados de uma loja HDFS no local para o Armazenamento Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | Disponível em Geral|[Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Utilização do HDFS CLI com Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: Extrair, transformar e carregar dados utilizando a Hive Apache no Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub | Disponível em Geral|[Use o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-cloud para diferentes pontos finais](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| Disponível em Geral|[Analisar dados no Data Lake Storage Gen2 usando Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Disponível em Geral|[Utilizar com o Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Disponível em Geral|[Gestor de conexão Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Pré-visualização|[Index and search Azure Data Lake Storage Gen2 (pré-visualização)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Pré-visualização|[Dados de consulta no Lago de Dados Azure usando o Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Rede de Entrega de Conteúdos do Microsoft Azure|Ainda não apoiado|[Index and search Azure Data Lake Storage Gen2 (pré-visualização)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Consulte também

- [Questões conhecidas com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plataformas de código aberto que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso multiprotocolo ao Armazenamento de Lagos De Dados Azure](data-lake-storage-multi-protocol-access.md)
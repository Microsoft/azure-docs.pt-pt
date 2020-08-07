---
title: Serviços Azure que suportam Azure Data Lake Storage Gen2 Microsoft Docs
description: Saiba quais os serviços da Azure integrados com a Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b185b6e7706b2049f2821400db9d810588000419
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848218"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Serviços Azure que suportam Azure Data Lake Storage Gen2

Você pode usar os serviços Azure para ingerir dados, realizar análises e criar representações visuais. Este artigo fornece uma lista de serviços Azure suportados, divulga o seu nível de suporte, e fornece-lhe links para artigos que o ajudam a usar estes serviços com a Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Serviços do Azure suportados

Esta tabela lista os serviços Azure que pode utilizar com a Azure Data Lake Storage Gen2. Os itens que aparecem nestas tabelas mudarão ao longo do tempo à medida que o suporte continua a expandir-se.

> [!NOTE]
> O nível de suporte refere-se apenas à forma como o serviço é suportado com a Data Lake Storage Gen 2.

|Serviço do Azure |Nível de suporte |Azure AD |Chave Partilhada| Artigos relacionados |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Disponível em Geral|Sim|Sim|[Carregue os dados no Azure Data Lake Storage Gen2 com a Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponível em Geral|Sim|Sim|[Utilizar com o Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Quickstart: Analise os dados em Azure Data Lake Storage Gen2 utilizando Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutorial: Extrair, transformar e carregar dados utilizando dados do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: Acesso dados do Lago de Armazenamento De Dados Gen2 dados com Azure Databricks usando Spark](data-lake-storage-use-databricks-spark.md)|
|Hub de Eventos do Azure|Disponível em Geral|Não|Sim|[Capture eventos através de Azure Event Hubs em Azure Blob Storage ou Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Disponível em Geral|Sim|Sim|[Tutorial: Implementar o padrão de captura do lago de dados para atualizar uma tabela Delta databricks](data-lake-storage-events.md)|
|Azure Logic Apps|Disponível em Geral|Não|Sim|[Visão geral - O que é Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Disponível em Geral|Sim|Sim|[Dados de acesso nos serviços de armazenamento Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Disponível em Geral|Sim|Sim|[Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Egress para Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Disponível em Geral|Não|Sim|[Utilize a Caixa de Dados Azure para migrar dados de uma loja HDFS no local para o Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Disponível em Geral|Sim|Sim|[Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Utilização do HDFS CLI com data lake storage gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: Extrair, transformar e carregar dados utilizando a Colmeia Apache em Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Disponível em Geral|Não|Sim|[Utilize o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Disponível em Geral|Sim|Sim|[Analise os dados em Data Lake Storage Gen2 usando Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (anteriormente SQL Data Warehouse)|Disponível em Geral|Sim|Sim|[Utilização com Azure Synapse Analytics (anteriormente SQL Data Warehouse)](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|O SQL Server Integration Services (SSIS)|Disponível em Geral|Sim|Sim|[Gestor de conexão Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Data Explorer|Disponível em Geral|Sim|Sim|[Dados de consulta no Lago de Dados Azure usando O Explorador de Dados Azure](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|Pré-visualizar|Sim|Sim|[Indexe e pesquisa documentos da Azure Data Lake Storage Gen2 (pré-visualização)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Rede de Entrega de Conteúdos do Microsoft Azure|Ainda não suportado|Não aplicável|Não aplicável|[Indexe e pesquisa documentos da Azure Data Lake Storage Gen2 (pré-visualização)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Ver também

- [Problemas conhecidos com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Recursos de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plataformas open source que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso multi-protocolo no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
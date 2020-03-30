---
title: Integrando o Armazenamento de Dados Azure Gen1 com outros Serviços Azure [ Microsoft Docs
description: Entenda como azure Data Lake Storage Gen1 se integra com outros serviços Azure
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 43024b63a355646f607adbb6623cc6c349374ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535600"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integração de Armazenamento de Lagos De dados Azure Gen1 com outros serviços Azure
O Azure Data Lake Storage Gen1 pode ser utilizado em conjunto com outros serviços Azure para permitir uma maior variedade de cenários. O seguinte artigo lista os serviços com os que data Lake Storage Gen1 pode ser integrado.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Utilize data lake storage Gen1 com Azure HDInsight
Pode fornecer um cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que utiliza o Data Lake Storage Gen1 como armazenamento compatível com hDFS. Para esta versão, para aglomerados Hadoop e Storm no Windows e Linux, você pode usar Data Lake Storage Gen1 apenas como um armazenamento adicional. Estes clusters ainda utilizam o Armazenamento Azure (WASB) como armazenamento predefinido. No entanto, para clusters HBase no Windows e Linux, pode utilizar data Lake Storage Gen1 como armazenamento padrão, ou armazenamento adicional, ou ambos.

Para obter instruções sobre como fornecer um cluster HDInsight com Data Lake Storage Gen1, consulte:

* [Provisionar um cluster HDInsight com Data Lake Storage Gen1 usando o Portal Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Fornecer um cluster HDInsight com Data Lake Storage Gen1 como armazenamento padrão usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Fornecer um cluster HDInsight com Data Lake Storage Gen1 como armazenamento adicional usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Use data lake storage Gen1 com Azure Data Lake Analytics
[O Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) permite-lhe trabalhar com big data à escala de nuvem. Fornece recursos dinamicamente e permite-lhe fazer análises em terabytes ou até mesmo exabytes de dados que podem ser armazenados em várias fontes de dados suportadas, sendo uma delas data Lake Storage Gen1. Data Lake Analytics está especialmente otimizado para trabalhar com data Lake Storage Gen1 - fornecendo o mais alto nível de desempenho, entrada e paralelização para você grandes cargas de trabalho de dados.

Para obter instruções sobre como usar data lake analytics com Data Lake Storage Gen1, consulte [Start Started with Data Lake Analytics usando Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Utilize data lake storage Gen1 com Azure Data Factory
Pode utilizar a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para ingerir dados a partir de tabelas Azure, Base de Dados Azure SQL, Azure SQL DataWarehouse, Blobs de Armazenamento Azure e bases de dados no local. Sendo um cidadão de primeira classe no ecossistema Azure, a Azure Data Factory pode ser usada para orquestrar a ingestão de dados destas fontes para data Lake Storage Gen1.

Para obter instruções sobre como utilizar a Azure Data Factory com data lake storage Gen1, consulte [Move dados de e para data Lake Storage Gen1 utilizando data factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copiar dados de Blobs de Armazenamento Azure em Data Lake Storage Gen1
O Azure Data Lake Storage Gen1 fornece uma ferramenta de linha de comando, a AdlCopy, que lhe permite copiar dados do Armazenamento De Azure Blob numa conta gen1 de armazenamento de data lake. Para mais informações, consulte [os dados da Cópia das Bolhas de Armazenamento Azure para data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copiar dados entre a Base de Dados Azure SQL e o Data Lake Storage Gen1
Pode utilizar o Apache Sqoop para importar e exportar dados entre a Base de Dados Azure SQL e o Data Lake Storage Gen1. Para mais informações, consulte os dados de cópia entre data [Lake Storage Gen1 e Azure SQL base de dados utilizando Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Use data lake storage Gen1 com Stream Analytics
Pode utilizar o Data Lake Storage Gen1 como uma das saídas para armazenar dados transmitidos através do Azure Stream Analytics. Para mais informações, consulte [os dados do Stream do Azure Storage Blob para data lake storage Gen1 usando o Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Use Data Lake Storage Gen1 com Power BI
Pode utilizar o Power BI para importar dados de uma conta Gen1 de Armazenamento de Data Lake para analisar e visualizar os dados. Para mais informações, consulte [a Análise de Dados no Data Lake Storage Gen1 utilizando o Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Use data Lake Storage Gen1 com catálogo de dados
Pode registar dados do Data Lake Storage Gen1 no Catálogo de Dados Do Azure para tornar os dados detetáveis em toda a organização. Para mais informações consulte [Registar dados do Data Lake Storage Gen1 no Catálogo](data-lake-store-with-data-catalog.md)de Dados Azure .

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Utilize data lake storage Gen1 com Serviços de Integração de Servidores SQL (SSIS)
Você pode usar o gestor de conexão Data Lake Storage Gen1 no SSIS para ligar um pacote SSIS com data Lake Storage Gen1. Para mais informações, consulte [Use Data Lake Storage Gen1 com SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Utilize data lake storage Gen1 com Armazém de Dados SQL
Pode utilizar o PolyBase para carregar dados do Data Lake Storage Gen1 para o SQL Data Warehouse. Para mais informações consulte [Use Data Lake Storage Gen1 com SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Use Data Lake Storage Gen1 com Hubs de Eventos Azure
Pode utilizar o Azure Data Lake Storage Gen1 para arquivar e capturar dados recebidos pelo Azure Event Hubs. Para mais informações consulte [Use Data Lake Storage Gen1 com Hubs de Eventos Azure](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Consulte também
* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
* [Começar com data lake storage Gen1 usando portal](data-lake-store-get-started-portal.md)
* [Começar com data lake storage Gen1 usando PowerShell](data-lake-store-get-started-powershell.md)  


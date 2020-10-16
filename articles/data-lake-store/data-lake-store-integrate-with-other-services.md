---
title: Integrar data lake storage Gen1 com outros serviços Azure
description: Compreenda como pode integrar a Azure Data Lake Storage Gen1 com outros serviços Azure.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 388c0db11c38f3dcdb9c4452b8d7283b2e811885
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108699"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integração do Azure Data Lake Storage Gen1 com outros serviços Azure
A Azure Data Lake Storage Gen1 pode ser usada em conjunto com outros serviços Azure para permitir uma ampla gama de cenários. O artigo que se segue enumera os serviços com os que a Data Lake Storage Gen1 pode ser integrada.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Use data lake storage Gen1 com Azure HDInsight
Você pode providenciar um cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que usa data lake storage gen1 como o armazenamento compatível com HDFS. Para esta versão, para clusters Hadoop e Storm em Windows e Linux, você pode usar Data Lake Storage Gen1 apenas como um armazenamento adicional. Estes clusters ainda utilizam o Azure Storage (WASB) como o armazenamento predefinido. No entanto, para clusters HBase no Windows e Linux, pode utilizar a Data Lake Storage Gen1 como armazenamento padrão, ou armazenamento adicional, ou ambos.

Para obter instruções sobre como providenciar um cluster HDInsight com data lake storage gen1, consulte:

* [Fornecimento de um cluster HDInsight com Data Lake Storage Gen1 usando o portal Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Provisionar um cluster HDInsight com data lake storage Gen1 como armazenamento padrão usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Provisionar um cluster HDInsight com data lake storage Gen1 como armazenamento adicional usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Use data lake storage Gen1 com Azure Data Lake Analytics
[O Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) permite-lhe trabalhar com Big Data à escala de nuvem. Fornece dinamicamente recursos e permite fazer análises em terabytes ou mesmo exabytes de dados que podem ser armazenados em várias fontes de dados apoiadas, sendo uma delas a Data Lake Storage Gen1. O Data Lake Analytics está especialmente otimizado para trabalhar com a Data Lake Storage Gen1 - proporcionando o mais alto nível de desempenho, produção e paralelização para você grandes cargas de trabalho de dados.

Para obter instruções sobre como usar data lake analytics com data lake storage gen1, consulte [Get Started with Data Lake Analytics usando Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Use data lake storage Gen1 com Azure Data Factory
Pode utilizar [a Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para ingerir dados a partir de tabelas Azure, Base de Dados Azure SQL, DataWarehouse Azure SQL, Blobs de Armazenamento Azure e bases de dados no local. Sendo um cidadão de primeira classe no ecossistema Azure, a Azure Data Factory pode ser usada para orquestrar a ingestão de dados destas fontes para a Data Lake Storage Gen1.

Para obter instruções sobre como utilizar a Fábrica de Dados Azure com a Data Lake Storage Gen1, consulte [os dados de e para o Data Lake Storage Gen1 utilizando a Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copiar dados da Azure Storage Blobs na Data Lake Storage Gen1
A Azure Data Lake Storage Gen1 fornece uma ferramenta de linha de comando, AdlCopy, que lhe permite copiar dados do Azure Blob Storage numa conta gen1 de armazenamento de data lake. Para obter mais informações, consulte [copiar os dados das bolhas de armazenamento Azure para a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copiar dados entre a Base de Dados Azure SQL e a Data Lake Storage Gen1
Você pode usar Apache Sqoop para importar e exportar dados entre Azure SQL Database e Data Lake Storage Gen1. Para obter mais informações, consulte [os dados de cópia entre data lake storage gen1 e Azure SQL Database usando Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Use data lake storage Gen1 com stream analytics
Pode utilizar a Data Lake Storage Gen1 como uma das saídas para armazenar dados transmitidos através do Azure Stream Analytics. Para obter mais informações, consulte [os dados do Stream da Azure Storage Blob na Data Lake Storage Gen1 utilizando o Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Use data lake storage Gen1 com Power BI
Pode utilizar o Power BI para importar dados de uma conta da Data Lake Storage Gen1 para analisar e visualizar os dados. Para obter mais informações, consulte [os dados da Data Lake Storage Gen1 utilizando o Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Use data lake storage Gen1 com catálogo de dados
Pode registar dados da Data Lake Storage Gen1 no Catálogo de Dados Azure para tornar os dados detetáveis em toda a organização. Para mais informações consulte [os dados do Registo da Data Lake Storage Gen1 no Catálogo de Dados Azure.](data-lake-store-with-data-catalog.md)

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Utilizar data lake storage gen1 com serviços de integração de servidores SQL (SSIS)
Pode utilizar o gestor de conexão Gen1 de armazenamento de dados em SSIS para ligar um pacote SSIS com a Data Lake Storage Gen1. Para obter mais informações, consulte [a Data Lake Storage Gen1 com sSIS.](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Use data lake storage Gen1 com Azure Synapse Analytics
Pode utilizar o PolyBase para carregar dados da Data Lake Storage Gen1 em Azure Synapse Analytics (anteriormente SQL Data Warehouse). Para mais informações consulte [Use Data Lake Storage Gen1 com Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Use data lake storage Gen1 com Azure Event Hubs
Pode utilizar a Azure Data Lake Storage Gen1 para arquivar e capturar dados recebidos pelo Azure Event Hubs. Para mais informações consulte [Use Data Lake Storage Gen1 com Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Ver também
* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Começar com data lake storage Gen1 usando portal](data-lake-store-get-started-portal.md)
* [Começar com data lake storage Gen1 usando PowerShell](data-lake-store-get-started-powershell.md)
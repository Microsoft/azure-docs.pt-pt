---
title: Integração do Azure Data Lake Storage Gen1 com outros serviços do Azure | Documentos da Microsoft
description: Compreender como a geração 1 de armazenamento do Azure Data Lake integra-se com outros serviços do Azure
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
ms.openlocfilehash: e28863f9980d6403bef1f88de01b7a9b5271b444
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879309"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integração de geração 1 de armazenamento do Azure Data Lake com outros serviços do Azure
Geração de armazenamento 1 do Azure Data Lake pode ser utilizado em conjunto com outros serviços do Azure para ativar uma vasta gama de cenários. O artigo seguinte lista os serviços de geração 1 de armazenamento do Data Lake pode ser integrado.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Utilizar o Data Lake Storage Gen1 com o Azure HDInsight
Pode aprovisionar um [do Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) cluster que utiliza a geração 1 de armazenamento do Data Lake como o armazenamento compatível com HDFS. Nesta versão, para clusters do Hadoop e o Storm no Windows e Linux, pode utilizar Gen1 de armazenamento do Data Lake apenas como um armazenamento adicional. Estes clusters ainda usam o armazenamento do Azure (WASB) como armazenamento predefinido. No entanto, para clusters do HBase no Windows e Linux, pode utilizar Gen1 de armazenamento do Data Lake como armazenamento predefinido, ou armazenamento adicional ou ambos.

Para obter instruções sobre como aprovisionar um cluster do HDInsight com Data Lake Storage Gen1, consulte:

* [Aprovisionar um cluster do HDInsight com a geração 1 de armazenamento do Data Lake através do Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprovisionar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento predefinido, com o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Aprovisionar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento adicional, com o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Utilize a geração 1 do Data Lake Storage do Azure Data Lake Analytics
[O Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) permite-lhe trabalhar com macrodados à escala da cloud. Aprovisiona recursos dinamicamente e permite-lhe fazer análises sobre terabytes ou até exabytes de dados que podem ser armazenados num número de origens de dados suportadas, um deles sendo Gen1 de armazenamento do Data Lake. O Data Lake Analytics está otimizado especialmente para funcionar com o Data Lake Storage Gen1 - fornecendo o nível mais elevado de desempenho, débito e paralelização para cargas de trabalho de grandes volumes de dados.

Para obter instruções sobre como utilizar o Data Lake Analytics com a geração 1 de armazenamento do Data Lake, veja [introdução ao Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Utilizar o Data Lake Storage Gen1 com o Azure Data Factory
Pode usar [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para ingerir dados de tabelas do Azure, base de dados do Azure SQL, o armazém de dados do Azure SQL, Blobs de armazenamento do Azure e bases de dados no local. Sendo um cidadão de primeira classe no ecossistema do Azure, Azure Data Factory pode ser utilizado para orquestrar a ingestão de dados destes origem para a geração 1 de armazenamento do Data Lake.

Para obter instruções sobre como utilizar o Azure Data Factory com a geração 1 de armazenamento do Data Lake, veja [mover dados para e de geração 1 de armazenamento do Data Lake com o Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake
Geração de armazenamento 1 do Azure Data Lake fornece uma ferramenta da linha de comandos, AdlCopy, que permite-lhe copiar dados de armazenamento de Blobs do Azure para uma conta de geração 1 de armazenamento do Data Lake. Para obter mais informações, consulte [copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copiar dados entre a base de dados do Azure SQL e de geração 1 de armazenamento do Data Lake
Pode utilizar o Apache Sqoop para importar e exportar dados entre a base de dados do Azure SQL e de geração 1 de armazenamento do Data Lake. Para obter mais informações, consulte [copiar dados entre a geração 1 do Data Lake armazenamento e base de dados SQL do Azure com o Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Utilizar o Data Lake Storage Gen1 com o Stream Analytics
Pode utilizar Gen1 de armazenamento do Data Lake como um das saídas para armazenar os dados transmitidos em fluxo com o Azure Stream Analytics. Para obter mais informações, consulte [Stream dados do Azure Storage Blob para a geração 1 de armazenamento do Data Lake com o Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Utilizar o Data Lake Storage Gen1 com o Power BI
Pode utilizar o Power BI para importar dados a partir de uma conta de geração 1 de armazenamento do Data Lake para analisar e visualizar os dados. Para obter mais informações, consulte [analisar dados de geração 1 de armazenamento do Data Lake com o Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Utilizar o Data Lake Storage Gen1 no catálogo de dados
Pode registar dados de geração 1 de armazenamento do Data Lake para o catálogo de dados do Azure para tornar os dados detectável em toda a organização. Para obter mais informações, consulte [registar dados de geração 1 de armazenamento do Data Lake no catálogo de dados do Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Utilizar o Data Lake Storage Gen1 com SQL Server Integration Services (SSIS)
Pode utilizar o Gestor de ligações de geração 1 de armazenamento do Data Lake do SSIS para ligar um pacote do SSIS com a geração 1 de armazenamento do Data Lake. Para obter mais informações, consulte [utilizar o Data Lake armazenamento Gen1 com o SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Utilizar o Data Lake Storage Gen1 com o SQL Data Warehouse
Pode utilizar o PolyBase para carregar dados de geração 1 de armazenamento do Data Lake para o SQL Data Warehouse. Para obter mais informações, consulte [Use Data Lake armazenamento Gen1 com o SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Utilizar o Data Lake Storage Gen1 com os Hubs de eventos do Azure
Pode utilizar Gen1 de armazenamento do Azure Data Lake para o arquivo e a captura de dados recebidos pelo Event Hubs do Azure. Para obter mais informações, consulte [Use Data Lake armazenamento Gen1 com os Hubs de eventos do Azure](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Consulte também
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução ao Data Lake Storage Gen1 através do Portal](data-lake-store-get-started-portal.md)
* [Introdução à geração 1 de armazenamento do Data Lake com o PowerShell](data-lake-store-get-started-powershell.md)  


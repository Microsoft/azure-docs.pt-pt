---
title: Cenários de dados envolvendo Azure Data Lake Storage Gen2 | Microsoft Docs
description: Compreenda os diferentes cenários e ferramentas que utilizam quais os dados que podem ingerir, processar, descarregar e visualizar na Data Lake Storage Gen2 (anteriormente conhecida como Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4ed2458b09d200542ce8789d90250027a07a970d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735476"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Usando Azure Data Lake Storage Gen2 para grandes requisitos de dados

Existem quatro etapas-chave no processamento de big data:

> [!div class="checklist"]
> * Ingerir grandes quantidades de dados numa loja de dados, em tempo real ou em lotes
> * Processamento dos dados
> * Descarregar os dados
> * Visualização dos dados

Este artigo destaca as opções e ferramentas para cada fase de processamento.

Para obter uma lista completa dos serviços Azure que pode utilizar com a Azure Data Lake Storage Gen2, consulte [Integrate Azure Data Lake Storage with Azure services](./data-lake-storage-supported-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Ingerir os dados na Data Lake Storage Gen2

Esta secção destaca as diferentes fontes de dados e as diferentes formas pelas quais esses dados podem ser ingeridos numa conta da Data Lake Storage Gen2.

![Ingerir dados em Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingerir dados em Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dados ad hoc

Isto representa conjuntos de dados menores que são usados para prototipar uma aplicação de big data. Existem diferentes formas de ingerir dados ad hoc dependendo da origem dos dados. 

Aqui está uma lista de ferramentas que pode usar para ingerir dados ad hoc.

| Origem de dados | Ingeri-lo usando |
| --- | --- |
| Computador local |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[CLI do Azure](data-lake-storage-directory-file-acl-cli.md)<br><br>[Explorador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp em execução no cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dados transmitidos

Isto representa dados que podem ser gerados por várias fontes, tais como aplicações, dispositivos, sensores, etc. Estes dados podem ser ingeridos na Data Lake Storage Gen2 por uma variedade de ferramentas. Estas ferramentas geralmente capturam e processam os dados numa base de evento em tempo real e, em seguida, escreverão os eventos em lotes em Data Lake Storage Gen2 para que possam ser processados.

Aqui está uma lista de ferramentas que pode usar para ingerir dados transmitidos.

|Ferramenta | Orientação |
|---|--|
|Azure Stream Analytics|[Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Egress para Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Tempestade Azure HDInsight | [Escreva para Apache Hadoop HDFS de Apache Storm on HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Dados relacionais

Também pode obter dados a partir de bases de dados relacionais. Ao longo de um período de tempo, as bases de dados relacionais recolhem enormes quantidades de dados que podem fornecer informações chave se processadas através de um grande pipeline de dados. Pode utilizar as seguintes ferramentas para mover esses dados para a Data Lake Storage Gen2.

Aqui está uma lista de ferramentas que pode usar para ingerir dados relacionais.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](../../data-factory/copy-activity-overview.md) (Atividade de Cópia no Azure Data Factory) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo de servidor web (upload usando aplicações personalizadas)

Este tipo de conjunto de dados é especificamente chamado porque a análise dos dados de registo de servidores web é um caso de uso comum para aplicações de big data e requer grandes volumes de ficheiros de registo para ser enviado para Data Lake Storage Gen2. Pode utilizar qualquer uma das seguintes ferramentas para escrever os seus próprios scripts ou aplicações para fazer o upload desses dados.

Aqui está uma lista de ferramentas que pode usar para ingerir dados de registo de servidores web.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](../../data-factory/copy-activity-overview.md) (Atividade de Cópia no Azure Data Factory)  |
|CLI do Azure|[CLI do Azure](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Para o upload de dados de registo de servidores web, e também para o upload de outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem para escrever os seus próprios scripts/aplicações personalizadas porque lhe dá a flexibilidade para incluir o seu componente de upload de dados como parte da sua maior aplicação de big data. Em alguns casos, este código pode assumir a forma de um script ou de uma simples utilidade de linha de comando. Noutros casos, o código pode ser utilizado para integrar o processamento de big data numa aplicação ou solução de negócio.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) suportam a Data Lake Storage Gen2 como um repositório de armazenamento de dados. Os clusters HDInsight acedem aos dados de Azure Storage Blobs (WASB). Para um melhor desempenho, pode copiar os dados do WASB numa conta gen2 de armazenamento de dados associada ao cluster. Pode utilizar as seguintes ferramentas para copiar os dados.

Aqui está uma lista de ferramentas que pode usar para ingerir dados associados a clusters HDInsight.

|Ferramenta | Orientação |
|---|--|
|Apache DistCp | [Utilize o DistCp para copiar dados entre as bolhas de armazenamento Azure e a Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Ferramenta AzCopy | [Dados de transferência com o AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Copiar dados para ou a partir da Azure Data Lake Storage Gen2 utilizando a Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados em instalações ou aglomerados De Hadoop IaaS

Grandes quantidades de dados podem ser armazenados em clusters Hadoop existentes, localmente em máquinas que utilizem HDFS. Os aglomerados hadoop podem estar em uma implantação no local ou podem estar dentro de um aglomerado IaaS em Azure. Pode haver requisitos para copiar esses dados para a Azure Data Lake Storage Gen2 para uma abordagem única ou de forma recorrente. Existem várias opções que pode usar para o conseguir. Abaixo está uma lista de alternativas e as trocas comerciais associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Utilize a Azure Data Factory (ADF) para copiar dados diretamente dos clusters Hadoop para Azure Data Lake Storage Gen2 |[ADF suporta o HDFS como fonte de dados](../../data-factory/connector-hdfs.md) |A ADF fornece suporte fora da caixa para HDFS e gestão e monitorização de primeira classe |Exige que o Gateway de Gestão de Dados seja implantado no local ou no cluster IaaS |
| Utilize o Distcp para copiar dados de Hadoop para Azure Storage. Em seguida, copie os dados do Azure Storage para a Data Lake Storage Gen2 utilizando o mecanismo apropriado. |Pode copiar dados do Azure Storage para data lake storage gen2 utilizando: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp em execução em clusters HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Pode utilizar ferramentas de código aberto. |Processo em várias etapas que envolve múltiplas tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de dados realmente grandes

Para o upload de conjuntos de dados que variam em vários terabytes, usar os métodos acima descritos pode por vezes ser lento e dispendioso. Nestes casos, pode utilizar a Azure ExpressRoute.  

O Azure ExpressRoute permite criar ligações privadas entre centros de dados Azure e infraestruturas nas suas instalações. Isto fornece uma opção fiável para a transferência de grandes quantidades de dados. Para saber mais, consulte [a documentação do Azure ExpressRoute.](../../expressroute/expressroute-introduction.md)

## <a name="process-the-data"></a>Processar os dados

Uma vez que os dados estão disponíveis na Data Lake Storage Gen2, pode fazer análises sobre esses dados utilizando as aplicações de big data suportadas. 

![Analisar dados em Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analisar dados em Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que você pode usar para executar trabalhos de análise de dados em dados que são armazenados na Data Lake Storage Gen2.

|Ferramenta | Orientação |
|---|--|
|Azure HDInsight | [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) (Armazenamento do Azure Data Lake Gen2)<br><br>[Quickstart: Analise os dados em Azure Data Lake Storage Gen2 utilizando Azure Databricks](./data-lake-storage-use-databricks-spark.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrair, transformar e carregar dados através do Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Ver os dados

Utilize o conector Power BI para criar representações visuais de dados armazenados na Data Lake Storage Gen2. Consulte [os dados da Azure Data Lake Storage Gen2 utilizando o Power BI](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Transferir os dados

Também pode querer descarregar ou mover dados do Azure Data Lake Storage Gen2 para cenários como:

* Mova os dados para outros repositórios para interagir com os seus oleodutos de processamento de dados existentes. Por exemplo, é melhor mover dados da Data Lake Storage Gen2 para a Azure SQL Database ou uma instância do SQL Server.

* Faça o download de dados para o seu computador local para processamento em ambientes IDE enquanto constrói protótipos de aplicações.

![Dados da Egress do Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dados da Egress do Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que pode usar para descarregar dados da Data Lake Storage Gen2.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](../../data-factory/copy-activity-overview.md) (Atividade de Cópia no Azure Data Factory) |
|Apache DistCp | [Utilize o DistCp para copiar dados entre as bolhas de armazenamento Azure e a Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Explorador de Armazenamento do Azure|[Use Azure Storage Explorer to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md) (Utilizar o Explorador de Armazenamento do Azure para gerir diretórios, ficheiros e ACLs no Azure Data Lake Storage Gen2)|
|Ferramenta AzCopy|[Dados de transferência com armazenamento AzCopy e Blob](../common/storage-use-azcopy-v10.md#transfer-data)|
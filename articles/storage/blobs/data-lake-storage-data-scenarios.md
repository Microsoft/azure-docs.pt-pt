---
title: Cenários de dados envolvendo Azure Data Lake Storage Gen2 [ Microsoft Docs
description: Compreenda os diferentes cenários e ferramentas utilizando quais dados podem ingerir, processar, descarregar e visualizar em Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b0ebe6cb505fa2a145dd3cbb94398912f2933a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77369711"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Utilização do Azure Data Lake Storage Gen2 para grandes necessidades de dados

Existem quatro fases-chave no processamento de big data:

> [!div class="checklist"]
> * Ingerir grandes quantidades de dados numa loja de dados, em tempo real ou em lotes
> * Processamento dos dados
> * Descarregar os dados
> * Visualização dos dados

Este artigo destaca as opções e ferramentas para cada fase de processamento.

Para obter uma lista completa de serviços Azure que pode utilizar com o Azure Data Lake Storage Gen2, consulte [Integrate Azure Data Lake Storage com serviços Azure](data-lake-storage-integrate-with-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Ingerir os dados no Data Lake Storage Gen2

Esta secção destaca as diferentes fontes de dados e as diferentes formas de os dados poderem ser ingeridos numa conta gen2 de armazenamento de data Lake.

![Ingerir dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingerir dados no Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dados ad hoc

Isto representa conjuntos de dados mais pequenos que são usados para prototipagem de uma aplicação de big data. Existem diferentes formas de ingerir dados ad hoc dependendo da origem dos dados. 

Aqui está uma lista de ferramentas que pode usar para ingerir dados ad hoc.

| Origem de Dados | Ingelhe-lo usando |
| --- | --- |
| Computador local |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Explorador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)|
| Blob de armazenamento azure |[Fábrica de Dados Azure](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp em execução no cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dados transmitidos

Isto representa dados que podem ser gerados por várias fontes, tais como aplicações, dispositivos, sensores, etc. Estes dados podem ser ingeridos no Data Lake Storage Gen2 por uma variedade de ferramentas. Estas ferramentas geralmente capturarão e processarão os dados numa base evento-a-evento em tempo real, e depois escreverão os eventos em lotes em Data Lake Storage Gen2 para que possam ser processados.

Aqui está uma lista de ferramentas que pode usar para ingerir dados transmitidos.

|Ferramenta | Orientação |
|---|--|
|Azure Stream Analytics|[Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Egress para Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Tempestade Azure HDInsight | [Escreva para Apache Hadoop HDFS da Tempestade Apache no HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dados relacionais

Pode também obter dados a partir de bases de dados relacionais. Ao longo de um período de tempo, as bases de dados relacionais recolhem enormes quantidades de dados que podem fornecer insights fundamentais se processados através de um grande pipeline de dados. Pode utilizar as seguintes ferramentas para mover esses dados para data Lake Storage Gen2.

Aqui está uma lista de ferramentas que pode usar para ingerir dados relacionais.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) (Atividade de Cópia no Azure Data Factory) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo do servidor web (upload usando aplicações personalizadas)

Este tipo de conjunto de dados é especificamente chamado porque a análise de dados de registo de servidores web é um caso de uso comum para aplicações de big data e requer grandes volumes de ficheiros de registo para ser enviado para Data Lake Storage Gen2. Pode utilizar qualquer uma das seguintes ferramentas para escrever os seus próprios scripts ou aplicações para fazer o upload desses dados.

Aqui está uma lista de ferramentas que pode usar para ingerir dados de registo do servidor Web.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) (Atividade de Cópia no Azure Data Factory)  |
|CLI do Azure|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Para fazer o upload de dados de registo de servidores web, e também para o upload de outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem para escrever os seus próprios scripts/aplicações personalizadas porque lhe dá a flexibilidade para incluir o seu componente de carregamento de dados como parte da sua aplicação de big data maior. Em alguns casos, este código pode assumir a forma de um script ou de uma simples linha de comando. Noutros casos, o código pode ser utilizado para integrar o processamento de big data numa aplicação ou solução de negócio.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) suportam data lake storage Gen2 como um repositório de armazenamento de dados. Os clusters HDInsight acedem a dados de Blobs de Armazenamento Azure (WASB). Para um melhor desempenho, pode copiar os dados do WASB para uma conta Gen2 de Armazenamento de Data Lake associada ao cluster. Pode utilizar as seguintes ferramentas para copiar os dados.

Aqui está uma lista de ferramentas que pode usar para ingerir dados associados aos clusters HDInsight.

|Ferramenta | Orientação |
|---|--|
|Apache DistCp | [Utilize o DistCp para copiar dados entre as Bolhas de Armazenamento de Azure e o Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Ferramenta AzCopy | [Transferir dados com o AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Copiar dados de ou para o Azure Data Lake Storage Gen2 utilizando a Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados em aglomerados de Hadoop no local ou iaaS Hadoop

Grandes quantidades de dados podem ser armazenadas em aglomerados hadoop existentes, localmente em máquinas que utilizam HDFS. Os aglomerados Hadoop podem estar numa implantação no local ou podem estar dentro de um aglomerado IaaS em Azure. Pode haver requisitos para copiar esses dados para o Azure Data Lake Storage Gen2 para uma abordagem única ou de forma recorrente. Existem várias opções que pode usar para o conseguir. Abaixo está uma lista de alternativas e as compensações associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Utilize a Azure Data Factory (ADF) para copiar dados diretamente dos clusters Hadoop para o Azure Data Lake Storage Gen2 |[ADF suporta HDFS como fonte de dados](../../data-factory/connector-hdfs.md) |A ADF fornece suporte fora da caixa para HDFS e gestão e monitorização de ponta a ponta de primeira classe |Requer que o Portal de Gestão de Dados seja implantado no local ou no cluster IaaS |
| Utilize a Distcp para copiar dados de Hadoop para o Armazenamento Azure. Em seguida, copie os dados do Armazenamento Azure para data Lake Storage Gen2 utilizando o mecanismo apropriado. |Pode copiar dados do Armazenamento Azure para data Lake Storage Gen2 usando: <ul><li>[Fábrica de Dados Azure](../../data-factory/copy-activity-overview.md)</li><li>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp em execução em clusters HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Pode utilizar ferramentas de código aberto. |Processo em várias etapas que envolve múltiplas tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de dados realmente grandes

Para o upload de conjuntos de dados que variam em vários terabytes, usar os métodos acima descritos pode por vezes ser lento e dispendioso. Nestes casos, pode utilizar o Azure ExpressRoute.  

O Azure ExpressRoute permite criar ligações privadas entre centros de dados Azure e infraestruturas nas suas instalações. Isto fornece uma opção fiável para transferir grandes quantidades de dados. Para saber mais, consulte a [documentação do Azure ExpressRoute.](../../expressroute/expressroute-introduction.md)

## <a name="process-the-data"></a>Processar os dados

Uma vez que os dados estão disponíveis no Data Lake Storage Gen2, você pode executar análises sobre esses dados usando as aplicações de big data suportadas. 

![Analisar dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analisar dados no Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que pode usar para executar trabalhos de análise de dados em dados armazenados em Data Lake Storage Gen2.

|Ferramenta | Orientação |
|---|--|
|Azure HDInsight | [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) (Armazenamento do Azure Data Lake Gen2)<br><br>[Quickstart: Analise dados no Azure Data Lake Storage Gen2 utilizando tijolos de dados Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrair, transformar e carregar dados utilizando os Databricks Azure](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Ver os dados

Utilize o conector Power BI para criar representações visuais de dados armazenados em Data Lake Storage Gen2. Consulte [os dados do Azure Data Lake Storage Gen2 utilizando o Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Descarregue os dados

Também pode querer descarregar ou mover dados do Azure Data Lake Storage Gen2 para cenários como:

* Mova os dados para outros repositórios para interagir com os seus oleodutos de processamento de dados existentes. Por exemplo, é melhor transferir dados do Data Lake Storage Gen2 para a Base de Dados Azure SQL ou no local do Servidor SQL.

* Descarregue dados para o seu computador local para processamento em ambientes IDE enquanto constrói protótipos de aplicações.

![Dados da Egress do Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dados da Egress do Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que pode usar para descarregar dados do Data Lake Storage Gen2.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) (Atividade de Cópia no Azure Data Factory) |
|Apache DistCp | [Utilize o DistCp para copiar dados entre as Bolhas de Armazenamento de Azure e o Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Explorador do Storage do Azure|[Use o Azure Storage Explorer para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|Ferramenta AzCopy|[Transferir dados com armazenamento AzCopy e Blob](../common/storage-use-azcopy-blobs.md)|

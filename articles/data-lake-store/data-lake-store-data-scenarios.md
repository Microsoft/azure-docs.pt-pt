---
title: Cenários de dados envolvendo Data Lake Storage Gen1 [ Microsoft Docs
description: Compreenda os diferentes cenários e ferramentas utilizando quais dados podem ingerir, processar, descarregar e visualizar em Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: a8f9b66bf9a301888f2371fb1c58a4845c2232b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536161"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Utilização do Azure Data Lake Storage Gen1 para grandes necessidades de dados

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Existem quatro fases-chave no processamento de big data:

* Ingerir grandes quantidades de dados numa loja de dados, em tempo real ou em lotes
* Processamento dos dados
* Descarregar os dados
* Visualização dos dados

Neste artigo, olhamos para estas fases no que diz respeito ao Azure Data Lake Storage Gen1 para entender as opções e ferramentas disponíveis para atender às suas grandes necessidades de dados.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Ingerir dados no Data Lake Storage Gen1
Esta secção destaca as diferentes fontes de dados e as diferentes formas de os dados poderem ser ingeridos numa conta gen1 de armazenamento de data Lake.

![Ingerir dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Ingerir dados no Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Dados ad hoc
Isto representa conjuntos de dados mais pequenos que são usados para prototipagem de uma aplicação de big data. Existem diferentes formas de ingerir dados ad hoc dependendo da origem dos dados.

| Origem de Dados | Ingelhe-lo usando |
| --- | --- |
| Computador local |<ul> <li>[Portal do Azure](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Usando ferramentas de data lake para estúdio visual](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Blob de armazenamento azure |<ul> <li>[Fábrica de Dados Azure](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp em execução no cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dados transmitidos
Isto representa dados que podem ser gerados por várias fontes, tais como aplicações, dispositivos, sensores, etc. Estes dados podem ser ingeridos no Data Lake Storage Gen1 por uma variedade de ferramentas. Estas ferramentas geralmente capturarão e processarão os dados numa base evento-a-evento em tempo real, e depois escreverão os eventos em lotes em Data Lake Storage Gen1 para que possam ser processados.

Seguem-se as ferramentas que pode utilizar:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) - Eventos ingeridos em Hubs de Eventos podem ser escritos para Azure Data Lake Storage Gen1 usando uma saída de Armazenamento de Lago de Dados Azure Gen1.
* [Tempestade Azure HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md) - Pode escrever dados diretamente para Data Lake Storage Gen1 do aglomerado storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – Pode receber eventos de Event Hubs e depois escrevê-lo para Data Lake Storage Gen1 usando o [Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dados relacionais
Pode também obter dados a partir de bases de dados relacionais. Ao longo de um período de tempo, as bases de dados relacionais recolhem enormes quantidades de dados que podem fornecer insights fundamentais se processados através de um grande pipeline de dados. Pode utilizar as seguintes ferramentas para mover esses dados para data Lake Storage Gen1.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Fábrica de Dados Azure](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo do servidor web (upload usando aplicações personalizadas)
Este tipo de conjunto de dados é especificamente chamado porque a análise de dados de registo de servidores web é um caso de uso comum para aplicações de big data e requer grandes volumes de ficheiros de registo para ser enviado para Data Lake Storage Gen1. Pode utilizar qualquer uma das seguintes ferramentas para escrever os seus próprios scripts ou aplicações para fazer o upload desses dados.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Fábrica de Dados Azure](../data-factory/copy-activity-overview.md)

Para fazer o upload de dados de registo de servidores web, e também para o upload de outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem para escrever os seus próprios scripts/aplicações personalizadas porque lhe dá a flexibilidade para incluir o seu componente de carregamento de dados como parte da sua aplicação de big data maior. Em alguns casos, este código pode assumir a forma de um script ou de uma simples linha de comando. Noutros casos, o código pode ser utilizado para integrar o processamento de big data numa aplicação ou solução de negócio.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters Azure HDInsight
A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) suportam data lake storage Gen1 como um repositório de armazenamento de dados. Os clusters HDInsight acedem a dados de Blobs de Armazenamento Azure (WASB). Para um melhor desempenho, pode copiar os dados do WASB para uma conta Gen1 de Armazenamento de Data Lake associada ao cluster. Pode utilizar as seguintes ferramentas para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Fábrica de Dados Azure](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados em aglomerados de Hadoop no local ou iaaS Hadoop
Grandes quantidades de dados podem ser armazenadas em aglomerados hadoop existentes, localmente em máquinas que utilizam HDFS. Os aglomerados Hadoop podem estar numa implantação no local ou podem estar dentro de um aglomerado IaaS em Azure. Pode haver requisitos para copiar esses dados para o Azure Data Lake Storage Gen1 para uma abordagem única ou de forma recorrente. Existem várias opções que pode usar para o conseguir. Abaixo está uma lista de alternativas e as compensações associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Utilize a Azure Data Factory (ADF) para copiar dados diretamente dos clusters Hadoop para o Azure Data Lake Storage Gen1 |[ADF suporta HDFS como fonte de dados](../data-factory/connector-hdfs.md) |A ADF fornece suporte fora da caixa para HDFS e gestão e monitorização de ponta a ponta de primeira classe |Requer que o Portal de Gestão de Dados seja implantado no local ou no cluster IaaS |
| Exportar dados de Hadoop como ficheiros. Em seguida, copie os ficheiros para Azure Data Lake Storage Gen1 utilizando o mecanismo apropriado. |Pode copiar ficheiros para o Azure Data Lake Storage Gen1 utilizando: <ul><li>[Azure PowerShell para Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Aplicativo personalizado que utiliza qualquer Data Lake Storage Gen1 SDK</li></ul> |Rápido para começar. Pode fazer uploads personalizados |Processo em várias etapas que envolve múltiplas tecnologias. A gestão e monitorização tornar-se-ão um desafio ao longo do tempo dada a natureza personalizada das ferramentas |
| Utilize a Distcp para copiar dados de Hadoop para o Armazenamento Azure. Em seguida, copie os dados do Armazenamento Azure para data Lake Storage Gen1 utilizando o mecanismo apropriado. |Pode copiar dados do Armazenamento Azure para data Lake Storage Gen1 usando: <ul><li>[Fábrica de Dados Azure](../data-factory/copy-activity-overview.md)</li><li>[Ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp em execução em clusters HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Pode utilizar ferramentas de código aberto. |Processo em várias etapas que envolve múltiplas tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de dados realmente grandes
Para o upload de conjuntos de dados que variam em vários terabytes, usar os métodos acima descritos pode por vezes ser lento e dispendioso. Nesses casos, pode utilizar as opções abaixo.

* **Utilização da Azure ExpressRoute.** O Azure ExpressRoute permite criar ligações privadas entre centros de dados Azure e infraestruturas nas suas instalações. Isto fornece uma opção fiável para transferir grandes quantidades de dados. Para mais informações, consulte a [documentação do Azure ExpressRoute.](../expressroute/expressroute-introduction.md)
* **Upload de dados "offline".** Se utilizar o Azure ExpressRoute não for viável por qualquer motivo, pode utilizar o [serviço Azure Import/Export](../storage/common/storage-import-export-service.md) para enviar discos rígidos com os seus dados para um centro de dados Azure. Os seus dados são enviados primeiro para o Azure Storage Blobs. Em seguida, pode utilizar a ferramenta [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) ou [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar dados de Blobs de Armazenamento Azure para Data Lake Storage Gen1.

  > [!NOTE]
  > Ao utilizar o serviço de importação/exportação, os tamanhos dos ficheiros nos discos que enviam para o centro de dados Azure não devem ser superiores a 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Processar dados armazenados em Data Lake Storage Gen1
Uma vez que os dados estão disponíveis no Data Lake Storage Gen1 pode fazer análises sobre esses dados usando as aplicações de big data suportadas. Atualmente, pode utilizar o Azure HDInsight e o Azure Data Lake Analytics para executar trabalhos de análise de dados sobre os dados armazenados na Data Lake Storage Gen1.

![Analisar dados em Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analisar dados em Data Lake Storage Gen1")

Pode olhar para os seguintes exemplos.

* [Criar um cluster HDInsight com Data Lake Storage Gen1 como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Descarregue dados do Data Lake Storage Gen1
Também pode querer descarregar ou mover dados do Azure Data Lake Storage Gen1 para cenários como:

* Mova os dados para outros repositórios para interagir com os seus oleodutos de processamento de dados existentes. Por exemplo, é melhor transferir dados do Data Lake Storage Gen1 para a Base de Dados Azure SQL ou no local do Servidor SQL.
* Descarregue dados para o seu computador local para processamento em ambientes IDE enquanto constrói protótipos de aplicações.

![Dados da Egress do Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Dados da Egress do Data Lake Storage Gen1")

Nesses casos, pode utilizar qualquer uma das seguintes opções:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Fábrica de Dados Azure](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Também pode utilizar os seguintes métodos para escrever o seu próprio script/aplicação para descarregar dados do Data Lake Storage Gen1.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualizar dados em Data Lake Storage Gen1
Você pode usar uma mistura de serviços para criar representações visuais de dados armazenados em Data Lake Storage Gen1.

![Visualizar dados em Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Visualizar dados em Data Lake Storage Gen1")

* Pode começar por utilizar [a Azure Data Factory para transferir dados do Data Lake Storage Gen1 para o Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Depois disso, pode integrar o Power BI com o [Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) para criar representação visual dos dados.

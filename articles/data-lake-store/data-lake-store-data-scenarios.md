---
title: Cenários de dados envolvendo data lake storage Gen1 | Microsoft Docs
description: Compreenda os diferentes cenários e ferramentas que utilizam quais os dados que podem ingerir, processar, descarregar e visualizar na Data Lake Storage Gen1 (anteriormente conhecida como Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0bd895b0c19293a199b2a9b135915b7dc45b9e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702502"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Utilização do Azure Data Lake Storage Gen1 para grandes requisitos de dados

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Existem quatro etapas-chave no processamento de big data:

* Ingerir grandes quantidades de dados numa loja de dados, em tempo real ou em lotes
* Processamento dos dados
* Descarregar os dados
* Visualização dos dados

Neste artigo, olhamos para estas fases no que diz respeito ao Azure Data Lake Storage Gen1 para entender as opções e ferramentas disponíveis para atender às suas necessidades de grandes dados.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Ingerir dados na Data Lake Storage Gen1
Esta secção destaca as diferentes fontes de dados e as diferentes formas pelas quais esses dados podem ser ingeridos numa conta da Data Lake Storage Gen1.

![Ingerir dados na Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Ingerir dados na Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Dados ad hoc
Isto representa conjuntos de dados menores que são usados para prototipar uma aplicação de big data. Existem diferentes formas de ingerir dados ad hoc dependendo da origem dos dados.

| Origem de dados | Ingeri-lo usando |
| --- | --- |
| Computador local |<ul> <li>[Portal do Azure](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[CLI do Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Usando ferramentas do lago de dados para estúdio visual](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp em execução no cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dados transmitidos
Isto representa dados que podem ser gerados por várias fontes, tais como aplicações, dispositivos, sensores, etc. Estes dados podem ser ingeridos na Data Lake Storage Gen1 por uma variedade de ferramentas. Estas ferramentas geralmente capturam e processam os dados numa base de evento em tempo real e, em seguida, escreverão os eventos em lotes em Data Lake Storage Gen1 para que possam ser processados.

Seguem-se as ferramentas que pode utilizar:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md) - Os eventos ingeridos em Centros de Eventos podem ser escritos para Azure Data Lake Storage Gen1 usando uma saída da Azure Data Lake Storage Gen1.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) - Você pode escrever dados diretamente para Data Lake Storage Gen1 do cluster Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) – Você pode receber eventos de Event Hubs e depois escrevê-lo para data lake storage gen1 usando o [Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dados relacionais
Também pode obter dados a partir de bases de dados relacionais. Ao longo de um período de tempo, as bases de dados relacionais recolhem enormes quantidades de dados que podem fornecer informações chave se processadas através de um grande pipeline de dados. Pode utilizar as seguintes ferramentas para mover esses dados para a Data Lake Storage Gen1.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo de servidor web (upload usando aplicações personalizadas)
Este tipo de conjunto de dados é especificamente chamado porque a análise dos dados de registo de servidores web é um caso de uso comum para aplicações de big data e requer grandes volumes de ficheiros de registo para ser enviado para Data Lake Storage Gen1. Pode utilizar qualquer uma das seguintes ferramentas para escrever os seus próprios scripts ou aplicações para fazer o upload desses dados.

* [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Para o upload de dados de registo de servidores web, e também para o upload de outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem para escrever os seus próprios scripts/aplicações personalizadas porque lhe dá a flexibilidade para incluir o seu componente de upload de dados como parte da sua maior aplicação de big data. Em alguns casos, este código pode assumir a forma de um script ou de uma simples utilidade de linha de comando. Noutros casos, o código pode ser utilizado para integrar o processamento de big data numa aplicação ou solução de negócio.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters Azure HDInsight
A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) suportam a Data Lake Storage Gen1 como um repositório de armazenamento de dados. Os clusters HDInsight acedem aos dados de Azure Storage Blobs (WASB). Para um melhor desempenho, pode copiar os dados do WASB numa conta gen1 de armazenamento de dados associada ao cluster. Pode utilizar as seguintes ferramentas para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados em instalações ou aglomerados De Hadoop IaaS
Grandes quantidades de dados podem ser armazenados em clusters Hadoop existentes, localmente em máquinas que utilizem HDFS. Os aglomerados hadoop podem estar em uma implantação no local ou podem estar dentro de um aglomerado IaaS em Azure. Pode haver requisitos para copiar esses dados para a Azure Data Lake Storage Gen1 para uma abordagem única ou de forma recorrente. Existem várias opções que pode usar para o conseguir. Abaixo está uma lista de alternativas e as trocas comerciais associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Utilize a Azure Data Factory (ADF) para copiar dados diretamente dos clusters Hadoop para a Azure Data Lake Storage Gen1 |[ADF suporta o HDFS como fonte de dados](../data-factory/connector-hdfs.md) |A ADF fornece suporte fora da caixa para HDFS e gestão e monitorização de primeira classe |Exige que o Gateway de Gestão de Dados seja implantado no local ou no cluster IaaS |
| Dados de exportação de Hadoop como ficheiros. Em seguida, copie os ficheiros para a Azure Data Lake Storage Gen1 utilizando o mecanismo apropriado. |Pode copiar ficheiros para a Azure Data Lake Storage Gen1 utilizando: <ul><li>[Azure PowerShell para Windows OS](data-lake-store-get-started-powershell.md)</li><li>[CLI do Azure](data-lake-store-get-started-cli-2.0.md)</li><li>Aplicativo personalizado usando qualquer Data Lake Storage Gen1 SDK</li></ul> |Rápido para começar. Pode fazer uploads personalizados |Processo em várias etapas que envolve múltiplas tecnologias. A gestão e monitorização crescerão para ser um desafio ao longo do tempo dada a natureza personalizada das ferramentas |
| Utilize o Distcp para copiar dados de Hadoop para Azure Storage. Em seguida, copie os dados do Azure Storage para a Data Lake Storage Gen1 utilizando o mecanismo apropriado. |Pode copiar dados do Azure Storage para data lake storage gen1 utilizando: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp em execução em clusters HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Pode utilizar ferramentas de código aberto. |Processo em várias etapas que envolve múltiplas tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de dados realmente grandes
Para o upload de conjuntos de dados que variam em vários terabytes, usar os métodos acima descritos pode por vezes ser lento e dispendioso. Nesses casos, pode utilizar as opções abaixo.

* **Utilizando a Azure ExpressRoute**. O Azure ExpressRoute permite criar ligações privadas entre datacenters Azure e infraestruturas nas suas instalações. Isto fornece uma opção fiável para a transferência de grandes quantidades de dados. Para mais informações, consulte [a documentação do Azure ExpressRoute.](../expressroute/expressroute-introduction.md)
* **Upload "offline" de dados**. Se a utilização do Azure ExpressRoute não for viável por qualquer motivo, pode utilizar o [serviço Azure Import/Export](../import-export/storage-import-export-service.md) para enviar discos rígidos com os seus dados para um centro de dados Azure. Os seus dados são enviados pela primeira vez para a Azure Storage Blobs. Em seguida, pode utilizar [a ferramenta Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) ou [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar dados de Azure Storage Blobs para Data Lake Storage Gen1.

  > [!NOTE]
  > Ao utilizar o serviço De importação/exportação, os tamanhos dos ficheiros nos discos que envia para o centro de dados Azure não devem ser superiores a 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Dados do processo armazenados na Data Lake Storage Gen1
Uma vez que os dados estão disponíveis na Data Lake Storage Gen1, pode fazer análises sobre esses dados utilizando as aplicações de big data suportadas. Atualmente, você pode usar Azure HDInsight e Azure Data Lake Analytics para executar trabalhos de análise de dados sobre os dados armazenados na Data Lake Storage Gen1.

![Analisar dados em Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analisar dados em Data Lake Storage Gen1")

Pode olhar para os seguintes exemplos.

* [Crie um cluster HDInsight com data lake storage gen1 como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Baixar dados da Data Lake Storage Gen1
Também pode querer descarregar ou mover dados do Azure Data Lake Storage Gen1 para cenários como:

* Mova os dados para outros repositórios para interagir com os seus oleodutos de processamento de dados existentes. Por exemplo, é melhor mover dados da Data Lake Storage Gen1 para Azure SQL Database ou SQL Server.
* Faça o download de dados para o seu computador local para processamento em ambientes IDE enquanto constrói protótipos de aplicações.

![Dados da Egress da Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Dados da Egress da Data Lake Storage Gen1")

Nesses casos, pode utilizar qualquer uma das seguintes opções:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Também pode utilizar os seguintes métodos para escrever o seu próprio script/aplicação para descarregar dados da Data Lake Storage Gen1.

* [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualizar dados em Data Lake Storage Gen1
Você pode usar uma mistura de serviços para criar representações visuais de dados armazenados na Data Lake Storage Gen1.

![Visualizar dados em Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Visualizar dados em Data Lake Storage Gen1")

* Pode começar por utilizar [a Azure Data Factory para transferir dados da Data Lake Storage Gen1 para a Azure Synapse Analytics](../data-factory/copy-activity-overview.md)
* Depois disso, pode [integrar o Power BI com o Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) para criar representação visual dos dados.
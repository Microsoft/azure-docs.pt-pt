---
title: Cenários de dados que envolvem Gen1 de armazenamento do Data Lake | Documentos da Microsoft
description: Compreender os diferentes cenários e as ferramentas com que os dados podem ingeridos, processados, transferidas e visualizados no Data Lake Storage Gen1 (anteriormente conhecido como o Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0b16154edbda4bedfd4e9b680ba4311e7a235212
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879064"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Usando Gen1 de armazenamento do Azure Data Lake para requisitos de grandes volumes de dados

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Processamento de macrodados, existem quatro fases principais:

* Ingerir grandes quantidades de dados num arquivo de dados em tempo real ou em lotes
* Processar os dados
* Transferir os dados
* Visualizar os dados

Neste artigo, vamos ver estas fases em relação a Gen1 de armazenamento do Azure Data Lake para compreender as opções e ferramentas disponíveis para atender às suas necessidades de grandes volumes de dados.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Ingerir dados no Data Lake Storage Gen1
Esta secção destaca as diferentes origens de dados e as diferentes formas em que esses dados podem ser ingeridos numa conta de geração 1 de armazenamento do Data Lake.

![Ingerir dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "ingerir dados no Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Dados ad hoc
Isso representa mais pequenos conjuntos de dados que são utilizados para criação de protótipos, um aplicativo de grandes volumes de dados. Existem formas diferentes de ingerir dados ad hoc, dependendo da origem dos dados.

| Origem de Dados | São ingeridos com |
| --- | --- |
| Computador local |<ul> <li>[Portal do Azure](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[CLI do Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Com ferramentas do Data Lake para Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Blob de armazenamento do Azure |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp em execução no cluster do HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dados de transmissão em fluxo contínuo
Isso representa os dados que podem ser gerados por várias origens, como aplicações, dispositivos, sensores, etc. Estes dados podem ser ingeridos no Data Lake Storage Gen1 por uma variedade de ferramentas. Essas ferramentas normalmente irão capturar e processar os dados de uma forma de eventos por eventos em tempo real e, em seguida, escreva os eventos em lotes em Gen1 de armazenamento do Data Lake para que possam ser processadas ainda mais.

Seguem-se as ferramentas que pode utilizar:

* [O Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -eventos ingeridos nos Hubs de eventos podem ser escritos para geração 1 de armazenamento do Azure Data Lake com uma saída de geração 1 de armazenamento do Azure Data Lake.
* [O Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) -pode escrever dados diretamente para a geração 1 de armazenamento do Data Lake do cluster do Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – pode receber eventos dos Hubs de eventos e, em seguida, escreva-a geração 1 de armazenamento do Data Lake utilizando o [SDK de .NET do Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dados relacionais
Também pode da origem de dados de bases de dados relacionais. Durante um período de tempo, bases de dados relacionais recolhem enormes quantidades de dados que podem fornecer informações de chaves se processada através de um pipeline de grandes volumes de dados. Pode utilizar as seguintes ferramentas para mover esses dados para a geração 1 de armazenamento do Data Lake.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo do servidor Web (carregamento com aplicativos personalizados)
Este tipo de conjunto de dados é designado sobretudo porque a análise de dados de registo do servidor web é um caso de utilização comuns para aplicações de macrodados e requer grandes volumes de ficheiros de registo para ser carregado para a geração 1 de armazenamento do Data Lake. Pode utilizar qualquer uma das seguintes ferramentas para escrever seus próprios scripts ou aplicações para carregar esses dados.

* [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK de .NET do Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Para carregar dados de registo do servidor web e também para o carregamento de outros tipos de dados (por exemplo, dados de sentimentos social), é uma boa abordagem para escrever seus próprios scripts personalizadas/aplicações porque dá-lhe a flexibilidade para incluir os seus dados ao carregar o componente como parte do seu aplicativo de grandes volumes de dados maior. Em alguns casos, esse código pode tomar a forma de um script ou o utilitário de linha de comandos simples. Em outros casos, o código pode ser usado para integrar o processamento de macrodados num aplicativo de negócios ou solução.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados a clusters do HDInsight do Azure
A maioria dos tipos de cluster de HDInsight (Hadoop, HBase, Storm) suportam a geração 1 de armazenamento do Data Lake como um repositório de armazenamento de dados. Clusters do HDInsight aceder aos dados de Blobs de armazenamento do Azure (WASB). Para um melhor desempenho, pode copiar os dados de WASB para uma conta de geração 1 do Data Lake Storage associada ao cluster. Pode utilizar as seguintes ferramentas para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados no local ou IaaS Hadoop clusters
Grandes quantidades de dados podem ser armazenadas em clusters do Hadoop existentes, localmente em máquinas com HDFS. Os clusters do Hadoop podem estar numa implementação no local ou podem ser dentro de um cluster de IaaS no Azure. Pode haver requisitos para copiar esses dados para geração 1 de armazenamento do Azure Data Lake para uma abordagem pontual ou de maneira recorrente. Existem várias opções que pode utilizar para atingir esse objetivo. Segue-se uma lista de alternativas e compromissos associados.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Utilizar o Azure Data Factory (ADF) para copiar dados diretamente a partir de clusters do Hadoop para a geração 1 de armazenamento do Azure Data Lake |[ADF suporta HDFS, como uma origem de dados](../data-factory/connector-hdfs.md) |ADF fornece suporte de out-of-the-box para HDFS e de primeira categoria ponto-a-ponto gestão e monitorização |Requer o Data Management Gateway ser implementado no local ou no IaaS do cluster |
| Exporte dados do Hadoop como ficheiros. Em seguida, copie os ficheiros para a geração 1 de armazenamento do Azure Data Lake com o mecanismo apropriado. |Pode copiar ficheiros para a utilização de geração 1 de armazenamento do Azure Data Lake: <ul><li>[O Azure PowerShell para o sistema operacional do Windows](data-lake-store-get-started-powershell.md)</li><li>[CLI do Azure](data-lake-store-get-started-cli-2.0.md)</li><li>Aplicação personalizada com qualquer SDK de geração 1 do Data Lake armazenamento</li></ul> |Guia de introdução para começar a utilizar. Pode efetuar carregamentos personalizados |Processo de vários passo que envolve várias tecnologias. Gestão e monitorização irão aumentar para ser um desafio ao longo do tempo devido à natureza personalizada das ferramentas |
| Utilize o Distcp para copiar dados do Hadoop para o armazenamento do Azure. Em seguida, copie os dados do armazenamento do Azure para a geração 1 de armazenamento do Data Lake com o mecanismo apropriado. |Pode copiar dados do armazenamento do Azure para a utilização de geração 1 de armazenamento do Data Lake: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Ferramenta de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache em execução em clusters do HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Pode utilizar ferramentas open source. |Processo de vários passo que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Realmente grandes conjuntos de dados
Para carregar conjuntos de dados que variam em vários terabytes, usando os métodos descritos acima, às vezes, é possível lenta e dispendioso. Nesses casos, pode usar as opções abaixo.

* **Utilizar o Azure ExpressRoute**. O Azure ExpressRoute permite-lhe criar ligações privadas entre os datacenters do Azure e a infraestrutura no local. Isso fornece uma opção fiável para transferir grandes quantidades de dados. Para obter mais informações, consulte [documentação do Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Offline" carregamento de dados**. Se utilizar o Azure ExpressRoute não é viável por qualquer motivo, pode usar [serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) enviar unidades de disco rígido com os seus dados para um centro de dados do Azure. Os dados pela primeira vez são carregados para Blobs de armazenamento do Azure. Em seguida, pode utilizar [do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) ou [ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar dados dos Blobs de armazenamento do Azure para geração 1 de armazenamento do Data Lake.

  > [!NOTE]
  > Apesar de utilizar o serviço de importação/exportação, o tamanho de ficheiros nos discos que envie para o Centro de dados do Azure não deve ser superior a 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Processar dados armazenados no Data Lake Storage Gen1
Depois dos dados estão disponíveis na geração 1 de armazenamento do Data Lake pode executar a análise de nesses dados com as aplicações de grandes volumes de dados suportados. Atualmente, pode utilizar o Azure HDInsight e Azure Data Lake Analytics para executar tarefas de análises de dados em dados armazenados no Data Lake Storage Gen1.

![Analise dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "analisar dados de geração 1 de armazenamento do Data Lake")

Pode ver os exemplos seguintes.

* [Criar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Transferir dados da geração 1 de armazenamento do Data Lake
Pode também querer transferir ou mover dados de geração 1 de armazenamento do Azure Data Lake para cenários, tais como:

* Mova dados para outros repositórios a interface com os pipelines de processamento de dados existente. Por exemplo, pode querer mover dados de geração 1 de armazenamento do Data Lake para a base de dados do Azure SQL ou SQL Server no local.
* Transferir dados para o computador local para processamento nos ambientes de IDE durante a criação de protótipos de aplicação.

![Saída de dados de geração 1 de armazenamento do Data Lake](./media/data-lake-store-data-scenarios/egress-data.png "saída de dados de geração 1 de armazenamento do Data Lake")

Nesses casos, pode usar qualquer uma das seguintes opções:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Também pode utilizar os seguintes métodos para escrever seu próprio script/aplicação transferir dados da geração 1 de armazenamento do Data Lake.

* [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK de .NET do Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualizar dados no Data Lake Storage Gen1
Pode utilizar uma combinação de serviços para criar representações visuais dos dados armazenados no Data Lake Storage Gen1.

![Visualizar dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "visualizar dados no Data Lake Storage Gen1")

* Pode iniciar usando [Azure Data Factory para mover dados de geração 1 de armazenamento do Data Lake para o Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Depois disso, pode [integrar o Power BI com o Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) para criar uma representação visual dos dados.

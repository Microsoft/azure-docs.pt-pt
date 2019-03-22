---
title: Extrair, transformar e carregar (ETL) à escala - Azure HDInsight
description: Saiba como o ETL é utilizada no HDInsight com o Apache Hadoop.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: c200ca98f2a5ea32886ec12d3e732af6598254f7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337617"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extração, transformação e carregamento (ETL) em escala

Extração, transformação e carregamento (ETL) é o processo pelo qual dados são adquiridos a partir de várias origens, coletados num local padrão, limpos e processados e, por fim, são carregados num arquivo de dados a partir da qual pode ser consultado. Os processos ETL herdados importar dados, limpá-lo no local e, em seguida, armazená-los num mecanismo de dados relacionais. Com o HDInsight, uma grande variedade de componentes do ecossistema Apache Hadoop suporta a realização de ETL em escala. 

A utilização do HDInsight no processo de ETL pode ser resumida por este pipeline:

![Descrição geral de ETL do HDInsight](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

As secções seguintes exploram cada uma das fases de ETL e seus componentes associados.

## <a name="orchestration"></a>Orquestração

Orquestração abrange todas as fases do pipeline ETL. Tarefas ETL no HDInsight freqüentemente envolvem vários produtos diferentes de trabalhar em conjunto entre si.  Pode utilizar o Hive para limpar uma parte dos dados, enquanto o Pig limpa outra parte.  Poderá utilizar o Azure Data Factory para carregar dados para o Azure SQL Database do Azure Data Lake Store.

Orquestração é necessário para executar a tarefa de adequado no momento adequado.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gere as tarefas do Hadoop. Oozie é executado dentro de um cluster do HDInsight e está integrada com a pilha do Hadoop. Oozie suporta tarefas do Hadoop para MapReduce do Apache Hadoop, Apache Pig, Apache Hive e Apache Sqoop. Oozie também pode ser utilizado para agendar tarefas que são específicas para um sistema, como programas de Java ou scripts de shell.

Para obter mais informações, consulte [utilizar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](../hdinsight-use-oozie-linux-mac.md) para uma descrição aprofundada, que mostra como utilizar o Oozie para acionar um pipeline ponto a ponto, consulte [Operacionalizar o Pipeline de dados](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

O Azure Data Factory fornece recursos de orquestração na forma de plataforma-como-serviço. É um serviço de integração de dados com base na cloud que permite-lhe criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento de dados e transformação de dados. 

Utilizar o Azure Data Factory, pode:

1. Criar e agendar condicionada por dados fluxos de trabalho (denominados pipelines) que ingerir dados de arquivos de dados diferentes.
2. Processe e transforme os dados com serviços como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch e do Azure Machine Learning de computação.
3. Publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para consumo das aplicações de business intelligence (BI).

Para obter mais informações sobre o Azure Data Factory, consulte a [documentação](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Armazenamento de ficheiros e armazenamento de resultado de ingestão

Ficheiros de origem de dados normalmente são carregados para uma localização no armazenamento do Azure ou o armazenamento do Azure Data Lake. Ficheiros podem estar em qualquer formato, mas normalmente são arquivos simples, como CSVs. 

### <a name="azure-storage"></a>Storage do Azure 

[O armazenamento do Azure](https://azure.microsoft.com/services/storage/blobs/) tem [destinos de escalabilidade específico](../../storage/common/storage-scalability-targets.md).  Para mais de analíticas nós, armazenamento do Azure dimensiona melhor, ao lidar com muitos ficheiros mais pequenos.  O armazenamento do Azure garante o mesmo desempenho, não importa quantos ficheiros ou tamanho do ficheiros (desde que está dentro dos seus limites).  Isso significa que pode armazenar terabytes de dados e ainda obter um desempenho consistente, quer esteja a utilizar um subconjunto dos dados ou todos os dados.

O armazenamento do Azure tem vários tipos diferentes de blobs.  Uma *dos BLOBs de acréscimo* é uma excelente opção para armazenar registos web ou dados de sensor.  

Vários blobs podem ser distribuídos por vários servidores para aumentar horizontalmente o acesso aos mesmos, mas um blob individual só pode ser servido por um único servidor. Enquanto os blobs podem ser agrupados logicamente em contentores de BLOBs, não há nenhum implicações de criação de partições deste agrupamento.

O armazenamento do Azure também tem uma camada de API de WebHDFS para o armazenamento de Blobs.  Todos os serviços no HDInsight podem aceder a ficheiros no armazenamento de Blobs do Azure para limpeza de dados e processamento de dados, da mesma forma para como esses serviços usaria Hadoop Distributed ficheiros System (HDFS).

Dados normalmente são ingeridos no armazenamento do Azure com o PowerShell, o SDK de armazenamento do Azure ou AZCopy.

### <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

O Azure Data Lake Storage (ADLS) é um repositório de hiperescala gerido, de dados de análise que é compatíveis com HDFS.  ADLS usa um paradigma de design que é semelhante ao HDFS e que oferece escalabilidade ilimitada em termos de capacidade total e o tamanho dos ficheiros individuais. ADLS é muito bom ao trabalhar com ficheiros grandes, uma vez que um arquivo grande pode ser armazenado em vários nós.  A criação de partições de dados no ADLS é feita em segundo plano.  Desta forma, fornece um débito maciço para executar tarefas de análise com milhares de executores simultâneos que leem e escrevem centenas de terabytes de dados de forma eficaz.

Dados normalmente são ingeridos no ADLS através do Azure Data Factory, SDKs do ADLS, serviço AdlCopy, Apache DistCp ou Apache Sqoop.  Que estes serviços em grande parte a utilizar depende de onde os dados são.  Se os dados estão atualmente num cluster de Hadoop existente, poderá utilizar o Apache DistCp, o serviço de AdlCopy ou o Azure Data Factory.  Se estiver no armazenamento de Blobs do Azure, poderá utilizar o SDK de .NET de armazenamento do Azure Data Lake, o Azure PowerShell ou o Azure Data Factory.

ADLS também está otimizado para ingestão de eventos com o Hub de eventos do Azure ou o Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para carregar conjuntos de dados no intervalo na casa dos terabytes, latência de rede pode ser um grande problema, especialmente se os dados é proveniente de uma localização no local.  Nesses casos, pode usar as opções abaixo:

* O Azure ExpressRoute:  O Azure ExpressRoute permite-lhe criar ligações privadas entre os datacenters do Azure e a sua infraestrutura no local. Estas ligações fornecem uma opção fiável para transferir grandes quantidades de dados. Para obter mais informações, consulte [documentação do Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* "Offline" carregamento de dados. Pode usar [serviço importar/exportar do Azure](../../storage/common/storage-import-export-service.md) enviar unidades de disco rígido com os seus dados para um centro de dados do Azure. Os dados pela primeira vez são carregados para Blobs de armazenamento do Azure. Em seguida, pode utilizar [do Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) ou o [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) ferramenta para copiar dados dos blobs de armazenamento do Azure para armazenamento do Data Lake.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Armazém de dados SQL do Azure é uma ótima opção para armazenar limpos e preparado resultados para análise futura.  O Azure HDInsight pode ser utilizado para executar esses serviços para o Azure SQL DW.

O Azure SQL Data Warehouse (SQL DW) é um arquivo de banco de dados relacional otimizado para cargas de trabalho de análise.  Armazém de dados SQL do Azure dimensiona com base em tabelas particionadas.  Tabelas podem ser particionadas em vários nós.  Nós de armazém de dados SQL do Azure estão selecionadas no momento da criação.  Após o fato de podem ser dimensionado, mas o que é um processo ativo que poderão necessitar de movimento de dados. Ver [SQL Data Warehouse - gerir a computação](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) para obter mais informações.

### <a name="apache-hbase"></a>Apache HBase

O Apache HBase é um arquivo de chave-valor, disponível no Azure HDInsight.  O Apache HBase é uma base de dados NoSQL open source baseada no Hadoop e modelada segundo o Google BigTable. HBase fornece acesso aleatório de alto desempenho e consistência segura para grandes quantidades de dados não estruturados e semiestruturados numa base de dados sem esquema, organizada por famílias de colunas.

Os dados são armazenados nas linhas de uma tabela e os dados de uma linha são agrupados por família de colunas. O HBase é uma base de dados sem esquema uma vez que não é preciso definir as colunas nem os tipos de dados nelas armazenados antes de os utilizar. O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. HBase pode contar com redundância de dados, processamento em lotes e outras funcionalidades que são fornecidas por aplicações distribuídas no ecossistema do Hadoop.   

HBase é um destino excelente para os dados de sensor e de registo para análise futura.

Escalabilidade de HBase é dependente do número de nós do cluster do HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Base de dados SQL do Azure e base de dados do Azure

O Azure oferece três diferentes bases de dados relacionais, como plataforma-como-serviço (PAAS).

* [Base de dados SQL do Azure](../../sql-database/sql-database-technical-overview.md) é uma implementação do Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte [ajuste de desempenho na base de dados do Azure SQL](../../sql-database/sql-database-performance-guidance.md).
* [Base de dados do Azure para MySQL](../../mysql/overview.md) é uma implementação do Oracle MySQL.
* [Base de dados do Azure para PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do PostgreSQL.

Esses produtos se expandir, que significa que eles são dimensionados adicionando mais CPU e memória.  Também pode optar por utilizar os discos premium com os produtos para um melhor desempenho de e/s.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

O Azure Analysis Services (AAS) é um motor de dados analíticos utilizado no apoio à decisão e análise de negócio, fornecendo os dados de análises para relatórios de negócios e aplicações de cliente como o Power BI, Excel, relatórios do Reporting Services e outros dados ferramentas de visualização.

Cubos do Analysis podem Dimensionar ao alterar as camadas para cada cubo individual.  Para obter mais informações, consulte [preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrair e carregar

Depois que os dados de existem no Azure, pode utilizar vários serviços para extrair e carregá-los para outros produtos.  HDInsight suporta Sqoop e Flume. 

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop é uma ferramenta projetada para transferência de dados com eficiência entre origens de dados estruturados, semiestruturados e não estruturados. 

Sqoop utiliza o MapReduce para importar e exportar os dados, para fornecer a operação simultânea e tolerância a falhas.

### <a name="apache-flume"></a>Apache Flume

Apache Flume é um serviço de distribuída, confiável e disponível para com eficiência a recolher, Agregar e mover grandes quantidades de dados de registo. Flume tem uma arquitetura de simple e flexível com base nos fluxos de dados de transmissão em fluxo. Flume é robusto e tolerante a falhas com mecanismos de fiabilidade ajustável e vários mecanismos de ativação pós-falha e recuperação. Flume usa um modelo de dados extensível simples que permite a aplicação de análise online.

Apache Flume não pode ser utilizado com o Azure HDInsight.  Uma instalação de Hadoop no local pode utilizar o Flume para enviar dados para o Azure Storage Blobs ou para o armazenamento do Azure Data Lake.  Para obter mais informações, consulte [utilizando o Apache Flume com HDInsight](https://web.archive.org/web/20190217104751/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformação

Depois de dados existem na localização escolhida, terá de limpá-lo, combiná-la ou prepará-lo para um padrão de utilização específicos.  Hive, Pig e Spark SQL são todas as boas opções para esse tipo de trabalho.  Eles são todos suportados no HDInsight. 

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o Apache Hive como uma ferramenta ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

---
title: Extrair, transformar e carregar (ETL) em Escala - Azure HDInsight
description: Saiba como extrair, transformar e carregar é usado no HDInsight com Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 8a4205002a98a5b9670839b0de7b53d81e0221a6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271935"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrair, transformar e carregar (ETL) em escala

Extrair, transformar e carregar (ETL) é o processo pelo qual os dados são adquiridos a partir de várias fontes, recolhidos num local padrão, limpos e processados, e que, em última análise, são carregados numa loja de dados a partir da qual podem ser consultados. A Legacy ETL processa os dados de importação, limpa-os no lugar e, em seguida, armazena-os num motor de dados relacional. Com o HDInsight, uma grande variedade de componentes do ecossistema Apache Hadoop suportam a realização de ETL em escala.

A utilização do HDInsight no processo ETL pode ser resumida por este gasoduto:

![HDInsight ETL na visão geral da escala](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

As seguintes secções exploram cada uma das fases ETL e seus componentes associados.

## <a name="orchestration"></a>Orquestração

A orquestração estende-se por todas as fases do oleoduto ETL. Os trabalhos da ETL na HDInsight envolvem frequentemente vários produtos diferentes que trabalham em conjunto entre si.  Você pode usar a Colmeia para limpar alguma parte dos dados, enquanto o Porco limpa outra porção.  Pode utilizar a Azure Data Factory para carregar dados na Base de Dados Azure SQL da Azure Data Lake Store.

A orquestração é necessária para executar o trabalho adequado no momento apropriado.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de coordenação de fluxos de trabalho que gere os empregos de Hadoop. Oozie funciona dentro de um cluster HDInsight e está integrado com a pilha Hadoop. Oozie apoia os trabalhos de Hadoop para Apache Hadoop MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Oozie também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de conchas.

Para obter mais informações, consulte [Oozie De utilizar Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](../hdinsight-use-oozie-linux-mac.md) Para um mergulho profundo que mostre como usar o Oozie para conduzir um oleoduto de ponta a ponta, consulte [Operacionalize o Pipeline de Dados](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory fornece capacidades de orquestração sob a forma de plataforma-as-a-service. É um serviço de integração de dados baseado na nuvem que permite criar fluxos de trabalho baseados em dados na nuvem para orquestrar e automatizar o movimento de dados e a transformação de dados.

Utilizando a Azure Data Factory, pode:

1. Criar e programar fluxos de trabalho orientados para dados (chamados oleodutos) que ingebem dados de lojas de dados díspares.
2. Processe e transforme os dados utilizando serviços de computação como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch e Azure Machine Learning.
3. Publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para consumo das aplicações de business intelligence (BI).

Para mais informações sobre a Azure Data Factory, consulte a [documentação](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Armazenamento de ficheiros e armazenamento de resultados

Os ficheiros de dados de origem são normalmente carregados num local em Armazenamento Azure ou Armazenamento de Lagos De Dados Azure. Os ficheiros podem estar em qualquer formato, mas normalmente são ficheiros planos como CSVs.

### <a name="azure-storage"></a>Storage do Azure

[O Armazenamento Azure](https://azure.microsoft.com/services/storage/blobs/) tem alvos específicos de escalabilidade. Para obter mais informações, consulte [a escalabilidade e os objetivos](../../storage/blobs/scalability-targets.md)de desempenho para o armazenamento blob . Para a maioria dos nós analíticos, o Armazenamento Azure é o melhor ao lidar com muitos ficheiros menores.  O Azure Storage garante o mesmo desempenho, não importa quantos ficheiros ou quão grandes os ficheiros (desde que esteja dentro dos seus limites).  Isto significa que pode armazenar terabytes de dados e ainda obter um desempenho consistente, quer esteja a usar um subconjunto dos dados ou todos os dados.

O Azure Storage tem vários tipos diferentes de bolhas.  Uma bolha de *apêndice* é uma ótima opção para armazenar registos web ou dados de sensores.  

Várias bolhas podem ser distribuídas por muitos servidores para escalar o acesso aos mesmos, mas uma única bolha só pode ser servida por um único servidor. Embora as bolhas possam ser agrunadas logicamente em recipientes de bolhas, não há implicações de divisão deste agrupamento.

O Azure Storage também tem uma camada WebHDFS API para o armazenamento de bolhas.  Todos os serviços no HDInsight podem aceder a ficheiros no Armazenamento De Blob Azure para limpeza de dados e processamento de dados, à semelhança de como esses serviços utilizariam o Sistema de Ficheiros Distribuídos Hadoop (HDFS).

Os dados são normalmente ingeridos no Armazenamento Azure utilizando o PowerShell, o Azure Storage SDK ou o AZCopy.

### <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

O Armazenamento de Lagos De Dados Azure (ADLS) é um repositório gerido e de hiperescala para dados de análise compatíveis com hDFS.  O ADLS usa um paradigma de design semelhante ao HDFS, e oferece escalabilidade ilimitada em termos de capacidade total e tamanho de ficheiros individuais. O ADLS é muito bom quando se trabalha com ficheiros grandes, uma vez que um ficheiro grande pode ser armazenado em vários nós.  Os dados de partilha no ADLS são feitos nos bastidores.  Obtém-se uma enorme entrada para executar trabalhos analíticos com milhares de executores simultâneos que lêem e escrevem eficientemente centenas de terabytes de dados.

Os dados são normalmente ingeridos em ADLS utilizando a Azure Data Factory, ADLS SDKs, AdlCopy Service, Apache DistCp ou Apache Sqoop.  Qual destes serviços para usar depende em grande parte de onde estão os dados.  Se os dados estiverem atualmente num cluster Hadoop existente, poderá utilizar o Apache DistCp, o AdlCopy Service ou a Azure Data Factory.  Se estiver no Armazenamento De Blob Azure, poderá utilizar o Armazenamento de Lagos De Dados Azure .NET SDK, Azure PowerShell ou Azure Data Factory.

O ADLS também está otimizado para a ingestão de eventos usando o Azure Event Hub ou a Tempestade Apache.

#### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para o upload de conjuntos de dados na gama terabyte, a latência da rede pode ser um grande problema, especialmente se os dados vierem de um local no local.  Nesses casos, pode utilizar as opções abaixo:

* Azure ExpressRoute: Azure ExpressRoute permite criar ligações privadas entre centros de dados Azure e a sua infraestrutura no local. Estas ligações fornecem uma opção fiável para a transferência de grandes quantidades de dados. Para mais informações, consulte a [documentação do Azure ExpressRoute.](../../expressroute/expressroute-introduction.md)

* Upload de dados "offline". Pode utilizar o [serviço Azure Import/Export para](../../storage/common/storage-import-export-service.md) enviar discos rígidos com os seus dados para um centro de dados Azure. Os seus dados são enviados primeiro para o Azure Storage Blobs. Em seguida, pode utilizar a [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) ou a ferramenta [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) para copiar dados de blobs de armazenamento de Azure para armazenamento de data lake.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

O Azure SQL DW é uma ótima escolha para armazenar resultados limpos e preparados para análisefutura.  O Azure HDInsight pode ser utilizado para realizar esses serviços para o Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) é uma loja de bases de dados relacional otimizada para cargas de trabalho analíticas.  Balanças de DW Azure SQL baseadas em mesas divididas.  As mesas podem ser divididas em vários nódosos.  Os nódosos Azure SQL DW são selecionados no momento da criação.  Podem escalar depois do facto, mas é um processo ativo que pode exigir movimento de dados. Para mais informações, consulte [SQL Data Warehouse - Manage Compute](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase é uma loja de valor-chave disponível no Azure HDInsight.  O Apache HBase é uma base de dados NoSQL open source baseada no Hadoop e modelada segundo o Google BigTable. A HBase fornece acesso aleatório performante e forte consistência para grandes quantidades de dados não estruturados e semiestruturados numa base de dados sem esquemas organizada por famílias de colunas.

Os dados são armazenados nas linhas de uma tabela e os dados de uma linha são agrupados por família de colunas. O HBase é uma base de dados sem esquema uma vez que não é preciso definir as colunas nem os tipos de dados nelas armazenados antes de os utilizar. O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. A HBase pode contar com a redundância de dados, processamento de lotes e outras funcionalidades que são fornecidas por aplicações distribuídas no ecossistema Hadoop.

O HBase é um excelente destino para dados de sensores e registos para análise futura.

A escalabilidade hBase depende do número de nós no cluster HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Base de Dados Azure SQL e Base de Dados Azure

O Azure oferece três bases de dados relacionais diferentes como plataforma-as-a-service (PAAS).

* [A Base de Dados Azure SQL](../../sql-database/sql-database-technical-overview.md) é uma implementação do Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte [O Desempenho de Afinação na Base de Dados SQL Azure](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) é uma implementação da Oracle MySQL.
* [Base de Dados Azure para PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do PostgreSQL.

Estes produtos escalam-se, o que significa que são dimensionados adicionando mais CPU e memória.  Também pode optar por utilizar discos premium com os produtos para um melhor desempenho em I/S.

## <a name="azure-analysis-services"></a>Azure Analysis Services

O Azure Analysis Services (AAS) é um motor de dados analítico utilizado no suporte à decisão e na análise de negócios, fornecendo os dados analíticos para relatórios de negócios e aplicações de clientes como Power BI, Excel, Reporting Services, e outros dados ferramentas de visualização.

Os cubos de análise podem escalar alterando camadas para cada cubo individual.  Para mais informações, consulte o [Preço dos Serviços](https://azure.microsoft.com/pricing/details/analysis-services/)de Análise azure .

## <a name="extract-and-load"></a>Extrato e Carga

Uma vez que os dados existam no Azure, pode utilizar muitos serviços para extraí-lo e carregá-lo em outros produtos.  HDInsight suporta Sqoop e Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

O Apache Sqoop é uma ferramenta concebida para transferir eficientemente dados entre fontes de dados estruturadas, semi-estruturadas e não estruturadas.

A Sqoop utiliza o MapReduce para importar e exportar os dados, para fornecer uma operação paralela e tolerância à falha.

### <a name="apache-flume"></a>Apache Flume

O Apache Flume é um serviço distribuído, fiável e disponível para recolher, agregar e mover quantidades grandes de dados de registo. A Flume tem uma arquitetura simples e flexível baseada em fluxos de dados de streaming. O flume é robusto e tolerante a falhas com mecanismos de fiabilidade incapazes e muitos mecanismos de failover e recuperação. Flume usa um modelo de dados extensível simples que permite aplicação analítica on-line.

O Apache Flume não pode ser usado com o Azure HDInsight.  Uma instalação hadoop no local pode usar flume para enviar dados para Azure Storage Blobs ou Azure Data Lake Storage.  Para mais informações, consulte [A Utilização do Flume Apache com HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformar

Uma vez que os dados existam no local escolhido, é necessário limpá-lo, combiná-lo ou prepará-lo para um padrão de utilização específico.  Colmeia, Porco e Spark SQL são boas escolhas para este tipo de trabalho.  São todos apoiados no HDInsight.

## <a name="next-steps"></a>Passos seguintes

* [Usando a Colmeia Apache como uma ferramenta ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Mova dados da base de dados Azure SQL para a tabela Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)

---
title: ETL (extração, transformação e carregamento) em escala – Azure HDInsight
description: Saiba como extrair, transformar e carregar é usado no HDInsight com Apache Hadoop.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: ashishth
ms.openlocfilehash: ceafee2d3356d37e74039789c8243ace41c141b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435782"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extração, transformação e carregamento (ETL) em escala

Extração, transformação e carregamento (ETL) é o processo pelo qual os dados são adquiridos de várias fontes, coletados em um local padrão, limpos e processados e, por fim, carregados em um repositório de dados do qual ele pode ser consultado. Os processos ETL herdados importam dados, limpam-no local e, em seguida, os armazenam em um mecanismo de dados relacional. Com o HDInsight, uma ampla variedade de componentes Apache Hadoop ecossistema dá suporte à execução de ETL em escala.

O uso do HDInsight no processo de ETL pode ser resumido por este pipeline:

![Visão geral do ETL do HDInsight em escala](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

As seções a seguir exploram cada uma das fases de ETL e seus componentes associados.

## <a name="orchestration"></a>Orquestração

A orquestração se estende em todas as fases do pipeline de ETL. Os trabalhos de ETL no HDInsight geralmente envolvem vários produtos diferentes que trabalham em conjunto entre si.  Você pode usar o hive para limpar uma parte dos dados, enquanto o Pig limpa outra parte.  Você pode usar Azure Data Factory para carregar dados para o Azure SQL Database do Azure Data Lake Store.

A orquestração é necessária para executar o trabalho apropriado no momento apropriado.

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie é um sistema de coordenação de fluxo de trabalho que gerencia trabalhos do Hadoop. O Oozie é executado em um cluster HDInsight e é integrado à pilha do Hadoop. O Oozie dá suporte a trabalhos do Hadoop para Apache Hadoop MapReduce, Apache Pig, Apache Hive e Apache Sqoop. O Oozie também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de Shell.

Para obter mais informações, consulte [usar o Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](../hdinsight-use-oozie-linux-mac.md) para uma análise aprofundada mostrando como usar o Oozie para direcionar um pipeline de ponta a ponta, consulte [operacionalize o pipeline de dados](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory fornece recursos de orquestração na forma de plataforma como serviço. É um serviço de integração de dados baseado em nuvem que permite criar fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação de dados e a transformação de dados.

Usando Azure Data Factory, você pode:

1. Crie e agende fluxos de trabalho orientados a dados (chamados de pipelines) que ingerirão dados de armazenamentos de dados diferentes.
2. Processe e transforme os dados usando serviços de computação, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, lote do Azure e Azure Machine Learning.
3. Publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para consumo das aplicações de business intelligence (BI).

Para obter mais informações sobre Azure Data Factory, consulte a [documentação](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingerir armazenamento de arquivos e armazenamento de resultados

Normalmente, os arquivos de dados de origem são carregados em um local no armazenamento do Azure ou Azure Data Lake Storage. Os arquivos podem estar em qualquer formato, mas normalmente são arquivos simples, como CSVs.

### <a name="azure-storage"></a>Armazenamento do Azure

O [armazenamento do Azure](https://azure.microsoft.com/services/storage/blobs/) tem metas de escalabilidade específicas. Para obter mais informações, consulte [escalabilidade e metas de desempenho para o armazenamento de BLOBs](../../storage/blobs/scalability-targets.md). Para a maioria dos nós analíticos, o armazenamento do Azure é mais bem dimensionado ao lidar com muitos arquivos menores.  O armazenamento do Azure garante o mesmo desempenho, independentemente de quantos arquivos ou do tamanho dos arquivos (desde que você esteja dentro dos limites).  Isso significa que você pode armazenar terabytes de dados e ainda obter um desempenho consistente, esteja você usando um subconjunto dos dados ou todos os dados.

O armazenamento do Azure tem vários tipos diferentes de BLOBs.  Um *blob de acréscimo* é uma ótima opção para armazenar logs da Web ou dados de sensor.  

Vários BLOBs podem ser distribuídos em vários servidores para expandir o acesso a eles, mas um único blob só pode ser servido por um único servidor. Embora os BLOBs possam ser logicamente agrupados em contêineres de BLOB, não há implicações de particionamento desse Agrupamento.

O armazenamento do Azure também tem uma camada de API WebHDFS para o armazenamento de BLOBs.  Todos os serviços no HDInsight podem acessar arquivos no armazenamento de BLOBs do Azure para limpeza de dados e processamento de dados, da mesma forma como esses serviços usariam o HDFS (sistema de arquivos distribuído do Hadoop).

Normalmente, os dados são ingeridos no armazenamento do Azure usando o PowerShell, o SDK do armazenamento do Azure ou o AZCopy.

### <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

O Azure Data Lake Storage (ADLS) é um repositório gerenciado e de hiperescala para dados de análise compatíveis com o HDFS.  O ADLS usa um paradigma de design semelhante ao HDFS e oferece escalabilidade ilimitada em termos da capacidade total e do tamanho de arquivos individuais. O ADLS é muito bom ao trabalhar com arquivos grandes, já que um arquivo grande pode ser armazenado em vários nós.  O particionamento de dados no ADLS é feito nos bastidores.  Desta forma, fornece um débito maciço para executar tarefas de análise com milhares de executores simultâneos que leem e escrevem centenas de terabytes de dados de forma eficaz.

Os dados normalmente são ingeridos em ADLS usando Azure Data Factory, SDKs de ADLS, AdlCopy Service, Apache DistCp ou Apache Sqoop.  Quais desses serviços usar em grande parte dependem de onde estão os dados.  Se os dados estiverem atualmente em um cluster Hadoop existente, você poderá usar o Apache DistCp, o serviço AdlCopy ou o Azure Data Factory.  Se ele estiver no armazenamento de BLOBs do Azure, você poderá usar Azure Data Lake Storage SDK do .NET, Azure PowerShell ou Azure Data Factory.

O ADLS também é otimizado para ingestão de eventos usando o Hub de eventos do Azure ou Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para carregar conjuntos de dados no intervalo de terabytes, a latência de rede pode ser um grande problema, especialmente se os dados forem provenientes de um local.  Nesses casos, você pode usar as opções abaixo:

* Azure ExpressRoute: o Azure ExpressRoute permite criar conexões privadas entre data centers do Azure e sua infraestrutura local. Essas conexões fornecem uma opção confiável para transferir grandes quantidades de dados. Para obter mais informações, consulte a [documentação do Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* Carregamento de dados "offline". Você pode usar o [serviço de importação/exportação do Azure](../../storage/common/storage-import-export-service.md) para enviar unidades de disco rígido com seus dados para um data center do Azure. Seus dados são carregados primeiro nos blobs de armazenamento do Azure. Em seguida, você pode usar [Azure data Factory](../../data-factory/connector-azure-data-lake-store.md) ou a ferramenta [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) para copiar dados de blobs de armazenamento do Azure para data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Armazém de Dados SQL do Azure

O SQL DW do Azure é uma ótima opção para armazenar resultados limpos e preparados para análises futuras.  O Azure HDInsight pode ser usado para executar esses serviços para o SQL DW do Azure.

O Azure SQL Data Warehouse (SQL DW) é um repositório de banco de dados relacional otimizado para cargas de trabalho analíticas.  O SQL DW do Azure é dimensionado com base em tabelas particionadas.  As tabelas podem ser particionadas em vários nós.  Os nós do SQL DW do Azure são selecionados no momento da criação.  Eles podem ser dimensionados após o fato, mas esse é um processo ativo que pode exigir a movimentação de dados. Consulte [SQL data warehouse-gerenciar computação](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) para obter mais informações.

### <a name="apache-hbase"></a>Apache HBase

O Apache HBase é um repositório de chave-valor disponível no Azure HDInsight.  O Apache HBase é uma base de dados NoSQL open source baseada no Hadoop e modelada segundo o Google BigTable. O HBase fornece acesso aleatório de alto desempenho e consistência forte para grandes quantidades de dados não estruturados e semiestruturados em um banco de dado sem esquema organizado por famílias de colunas.

Os dados são armazenados nas linhas de uma tabela e os dados de uma linha são agrupados por família de colunas. O HBase é uma base de dados sem esquema uma vez que não é preciso definir as colunas nem os tipos de dados nelas armazenados antes de os utilizar. O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. O HBase pode contar com redundância de dados, processamento em lotes e outros recursos que são fornecidos por aplicativos distribuídos no ecossistema do Hadoop.   

O HBase é um excelente destino para dados de sensor e de log para análise futura.

A escalabilidade do HBase depende do número de nós no cluster HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Banco de dados SQL do Azure e banco de dados do Azure

O Azure oferece três bancos de dados relacionais diferentes como plataforma como serviço (PAAS).

* O [banco de dados SQL do Azure](../../sql-database/sql-database-technical-overview.md) é uma implementação de Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte [ajustando o desempenho no banco de dados SQL do Azure](../../sql-database/sql-database-performance-guidance.md).
* O [banco de dados do Azure para MySQL](../../mysql/overview.md) é uma implementação do Oracle MySQL.
* O [banco de dados do Azure para PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do PostgreSQL.

Esses produtos são escalados verticalmente, o que significa que eles são dimensionados adicionando mais CPU e memória.  Você também pode optar por usar discos Premium com os produtos para melhorar o desempenho de e/s.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

A Azure Analysis Services (AAS) é um mecanismo de dados analíticos usado em suporte a decisões e análise de negócios, fornecendo os dados analíticos para relatórios de negócios e aplicativos cliente, como Power BI, Excel, relatórios de Reporting Services e outros dados ferramentas de visualização.

Os cubos de análise podem ser dimensionados alterando-se as camadas para cada cubo individual.  Para obter mais informações, consulte [preços de Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrair e carregar

Depois que os dados existirem no Azure, você poderá usar muitos serviços para extraí-los e carregá-los em outros produtos.  O HDInsight dá suporte a Sqoop e Flume. 

### <a name="apache-sqoop"></a>Apache Sqoop

O Apache Sqoop é uma ferramenta projetada para transferir dados com eficiência entre fontes de dados estruturadas, semiestruturadas e não estruturadas. 

O Sqoop usa o MapReduce para importar e exportar os dados, para fornecer uma operação paralela e tolerância a falhas.

### <a name="apache-flume"></a>Apache Flume

O Apache Flume é um serviço distribuído, confiável e disponível para coletar, agregar e mover grandes quantidades de dados de log com eficiência. O Flume tem uma arquitetura simples e flexível com base em fluxos de dados de streaming. O Flume é robusto e tolerante a falhas com mecanismos de confiabilidade ajustáveis e muitos mecanismos de failover e recuperação. O Flume usa um modelo de dados extensível simples que permite o aplicativo analítico online.

O Apache Flume não pode ser usado com o Azure HDInsight.  Uma instalação local do Hadoop pode usar o Flume para enviar dados para os BLOBs do armazenamento do Azure ou Azure Data Lake Storage.  Para obter mais informações, consulte [usando o Apache Flume com o HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformação

Depois que os dados existirem no local escolhido, você precisa limpá-los, combiná-los ou prepará-los para um padrão de uso específico.  Hive, Pig e Spark SQL são todas boas opções para esse tipo de trabalho.  Todos eles têm suporte no HDInsight. 

## <a name="next-steps"></a>Passos seguintes

* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usando Apache Hive como uma ferramenta ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

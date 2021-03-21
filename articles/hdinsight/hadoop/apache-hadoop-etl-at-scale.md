---
title: Extrato, transformação e carga (ETL) em escala - Azure HDInsight
description: Saiba como extrair, transformar e carregar é usado em HDInsight com Apache Hadoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: fc488cecb762ff13bcb2999bb06521ebb70b44fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946577"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrair, transformar e carregar (ETL) em escala

Extrato, transformação e carga (ETL) é o processo pelo qual os dados são adquiridos a partir de várias fontes. Os dados são recolhidos num local padrão, limpos e processados. Em última análise, os dados são carregados numa data-loja a partir da qual podem ser consultados. Legacy ETL processa dados de importação, limpa-os no lugar e, em seguida, armazená-lo em um motor de dados relacional. Com Azure HDInsight, uma grande variedade de componentes ambientais Apache Hadoop suportam ETL em escala.

A utilização de HDInsight no processo ETL é resumida por este pipeline:

![HDInsight ETL na escala geral](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

As seguintes secções exploram cada uma das fases ETL e os seus componentes associados.

## <a name="orchestration"></a>Orquestração

A orquestração estende-se por todas as fases do oleoduto ETL. Os trabalhos da ETL em HDInsight envolvem frequentemente vários produtos diferentes que trabalham em conjunto uns com os outros. Por exemplo:

- Podes usar a Colmeia Apache para limpar uma parte dos dados, e o Porco Apache para limpar outra porção.
- Pode utilizar a Azure Data Factory para carregar dados na Base de Dados Azure SQL da Azure Data Lake Store.

A orquestração é necessária para executar o trabalho adequado no momento apropriado.

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie é um sistema de coordenação de fluxos de trabalho que gere as tarefas do Hadoop. Oozie funciona dentro de um cluster HDInsight e está integrado com a pilha Hadoop. Oozie apoia os empregos de Hadoop para Apache Hadoop MapReduce, Pig, Hive e Sqoop. Você pode usar Oozie para agendar trabalhos específicos de um sistema, como programas Java ou scripts de concha.

Para obter mais informações, consulte [Use Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho em HDInsight](../hdinsight-use-oozie-linux-mac.md). Consulte também, [Operacionalizar o pipeline de dados.](../hdinsight-operationalize-data-pipeline.md)

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory fornece capacidades de orquestração sob a forma de plataforma como um serviço (PaaS). O Azure Data Factory é um serviço de integração de dados baseado na cloud. Permite criar fluxos de trabalho orientados por dados para orquestrar e automatizar o movimento de dados e a transformação de dados.

Utilize a Fábrica de Dados Azure para:

1. Criar e agendar fluxos de trabalho baseados em dados. Estes oleodutos ingerem dados de lojas de dados diferentes.
1. Processe e transforme os dados utilizando serviços de computação como HDInsight ou Hadoop. Você também pode usar Spark, Azure Data Lake Analytics, Azure Batch ou Azure Machine Learning para este passo.
1. Publique dados de saída em lojas de dados, como a Azure Synapse Analytics, para aplicações de BI consumir.

Para obter mais informações sobre a Azure Data Factory, consulte a [documentação.](../../data-factory/introduction.md)

## <a name="ingest-file-storage-and-result-storage"></a>Ingerir armazenamento de ficheiros e armazenamento de resultados

Os ficheiros de dados de origem são normalmente carregados num local no Azure Storage ou no Azure Data Lake Storage. Os ficheiros costumam estar num formato plano, como o CSV. Mas podem estar em qualquer formato.

### <a name="azure-storage"></a>Armazenamento do Azure

O Azure Storage tem alvos de adaptabilidade específicos. Consulte [os objetivos de escalabilidade e desempenho para armazenamento blob](../../storage/blobs/scalability-targets.md) para obter mais informações. Para a maioria dos nós analíticos, o Azure Storage escala melhor quando lida com muitos ficheiros menores. Enquanto estiver dentro dos limites da sua conta, o Azure Storage garante o mesmo desempenho, não importa quão grandes sejam os ficheiros. Pode armazenar terabytes de dados e ainda obter um desempenho consistente. Esta afirmação é verdadeira quer esteja a usar um subconjunto ou todos os dados.

O Azure Storage tem vários tipos de bolhas. Uma *bolha de apêndice* é uma ótima opção para armazenar registos web ou dados de sensores.

Várias bolhas podem ser distribuídas por muitos servidores para aumentar o acesso aos mesmos. Mas uma única bolha só é servida por um único servidor. Embora as bolhas possam ser logicamente agrupadas em recipientes de bolhas, não há implicações de partição deste agrupamento.

O Azure Storage tem uma camada API WebHDFS para o armazenamento de bolhas. Todos os serviços HDInsight podem aceder a ficheiros no armazenamento do Azure Blob para limpeza de dados e processamento de dados. Isto é semelhante à forma como esses serviços usariam o Sistema de Ficheiros Distribuídos Hadoop (HDFS).

Os dados são normalmente ingeridos no Azure Storage através do PowerShell, o Azure Storage SDK ou AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

O Azure Data Lake Storage é um repositório gerido e de hiperescala para dados de análise. É compatível e usa um paradigma de design semelhante ao HDFS. O Data Lake Storage oferece uma adaptabilidade ilimitada para a capacidade total e o tamanho dos ficheiros individuais. É uma boa escolha quando se trabalha com ficheiros grandes, porque podem ser armazenados em vários nós. Os dados de partilha no Armazenamento do Lago de Dados são feitos nos bastidores. Obtém-se uma produção massiva para executar empregos analíticos com milhares de executores simultâneos que lêem e escrevem centenas de terabytes de dados.

Os dados são geralmente ingeridos no armazenamento do Data Lake através da Azure Data Factory. Também pode utilizar SDKs de armazenamento de data lake, o serviço AdlCopy, Apache DistCp ou Apache Sqoop. O serviço que escolher depende de onde estão os dados. Se estiver num cluster Hadoop existente, poderá utilizar o Apache DistCp, o serviço AdlCopy ou a Azure Data Factory. Para obter dados no armazenamento Azure Blob, poderá utilizar o Azure Data Lake Storage .NET SDK, Azure PowerShell ou Azure Data Factory.

O Armazenamento do Lago de Dados é otimizado para a ingestão de eventos através de Azure Event Hubs ou Apache Storm.

### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para o upload de conjuntos de dados na gama terabyte, a latência da rede pode ser um grande problema. Isto é particularmente verdade se os dados são provenientes de um local no local. Nesses casos, pode utilizar estas opções:

- **Azure ExpressRoute:** Crie ligações privadas entre datacenters Azure e a sua infraestrutura no local. Estas ligações fornecem uma opção fiável para a transferência de grandes quantidades de dados. Para mais informações, consulte [a documentação do Azure ExpressRoute.](../../expressroute/expressroute-introduction.md)

- **Upload de dados a partir de discos rígidos:** Pode utilizar [o serviço Azure Import/Export](../../import-export/storage-import-export-service.md) para enviar discos rígidos com os seus dados para um datacenter Azure. Os seus dados são enviados pela primeira vez para o armazenamento da Azure Blob. Em seguida, pode utilizar a Azure Data Factory ou a ferramenta AdlCopy para copiar dados do armazenamento de Azure Blob para o Armazenamento do Lago de Dados.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

A Azure Synapse Analytics é uma escolha apropriada para armazenar resultados preparados. Você pode usar Azure HDInsight para executar esses serviços para Azure Synapse Analytics.

A Azure Synapse Analytics é uma loja de dados relacional otimizada para cargas de trabalho analíticas. Escamas baseadas em mesas divididas. As mesas podem ser divididas através de múltiplos nós. Os nós são selecionados no momento da criação. Podem escalar depois do facto, mas é um processo ativo que pode requerer movimento de dados. Para obter mais informações, consulte [Manage compute in Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase é uma loja chave/valor disponível em Azure HDInsight. É uma base de dados NoSQL de código aberto que é construída em Hadoop e modelada depois do Google BigTable. O HBase proporciona acesso aleatório performante e uma forte consistência para grandes quantidades de dados não estruturados e semi-estruturados.

Como o HBase é uma base de dados sem esquemas, não precisa de definir colunas e tipos de dados antes de as utilizar. Os dados são armazenados nas filas de uma tabela, e são agrupados pela família da coluna.

O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. A HBase baseia-se na redundância de dados, no processamento de lotes e noutras funcionalidades que são fornecidas por aplicações distribuídas no ambiente Hadoop.

O HBase é um bom destino para sensores e dados de registo para análise futura.

A adaptabilidade da base h depende do número de nós no cluster HDInsight.

### <a name="azure-sql-databases"></a>Bases de dados Azure SQL

A Azure oferece três bases de dados relacionais PaaS:

* [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) é uma implementação do Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte [a Afinação do Desempenho na Base de Dados Azure SQL](../../azure-sql/database/performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md)  é uma implementação da Oracle MySQL.
* [A Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do PostgreSQL.

Adicione mais CPU e memória para escalar estes produtos.  Também pode optar por utilizar discos premium com os produtos para um melhor desempenho de I/S.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services é um motor de dados analítico utilizado no suporte à decisão e na análise de negócios. Fornece os dados analíticos para relatórios de negócios e aplicações de clientes, como o Power BI. Os dados analíticos também funcionam com relatórios de Excel, SQL Server Reporting Services e outras ferramentas de visualização de dados.

Dimensione os cubos de análise alterando os níveis para cada cubo individual. Para mais informações, consulte [os preços dos Serviços de Análise Azure.](https://azure.microsoft.com/pricing/details/analysis-services/)

## <a name="extract-and-load"></a>Extrato e carga

Depois de existirem dados no Azure, pode utilizar muitos serviços para extraí-lo e carregá-lo em outros produtos. HDInsight suporta Sqoop e Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop é uma ferramenta projetada para transferir eficientemente dados entre fontes de dados estruturadas, semi-estruturadas e não estruturadas.

A Sqoop utiliza o MapReduce para importar e exportar os dados, para fornecer operação paralela e tolerância a falhas.

### <a name="apache-flume"></a>Gripe Apache

Apache Flume é um serviço distribuído, fiável e disponível para recolher, agregar e mover grandes quantidades de dados de registo. A sua arquitetura flexível baseia-se nos fluxos de dados de streaming. O flume é robusto e tolerante com mecanismos de fiabilidade incapazes. Tem muitos mecanismos de failover e recuperação. Flume usa um modelo de dados extensível simples que permite aplicação online e analítica.

Apache Flume não pode ser usado com Azure HDInsight. Mas, uma instalação hadoop no local pode usar flume para enviar dados para o armazenamento de Azure Blob ou para O Armazenamento do Lago de Dados Azure. Para obter mais informações, consulte [Usando o Apache Flume com HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformação

Depois de existirem dados no local escolhido, é necessário limpá-lo, combiná-lo ou prepará-lo para um padrão de utilização específico. Colmeia, Porco e Spark SQL são boas escolhas para este tipo de trabalho. São todos apoiados no HDInsight.

## <a name="next-steps"></a>Passos seguintes

- [Usando a Colmeia Apache como uma ferramenta ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Mover dados da Base de Dados Azure SQL para uma tabela de Colmeia Apache](./apache-hadoop-use-sqoop-mac-linux.md)

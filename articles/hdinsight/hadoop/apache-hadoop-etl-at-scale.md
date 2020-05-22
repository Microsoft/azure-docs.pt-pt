---
title: Extrair, transformar e carregar (ETL) à escala - Azure HDInsight
description: Saiba como extrair, transformar e carregar é usado no HDInsight com Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 5d6d4a41deb1ef96789a48eefba306be4dfb20eb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745137"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrair, transformar e carregar (ETL) em escala

Extrair, transformar e carregar (ETL) é o processo pelo qual os dados são adquiridos a partir de várias fontes. Os dados são recolhidos num local padrão, limpos e processados. Em última análise, os dados são carregados numa loja de dados a partir da qual podem ser consultados. A Legacy ETL processa os dados de importação, limpa-os no lugar e, em seguida, armazena-os num motor de dados relacional. Com o Azure HDInsight, uma grande variedade de componentes ambientais Apache Hadoop suportam a ETL em escala.

A utilização do HDInsight no processo ETL é resumida por este gasoduto:

![HDInsight ETL na visão geral da escala](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

As seguintes secções exploram cada uma das fases ETL e seus componentes associados.

## <a name="orchestration"></a>Orquestração

A orquestração estende-se por todas as fases do oleoduto ETL. Os trabalhos da ETL na HDInsight envolvem frequentemente vários produtos diferentes que trabalham em conjunto entre si. Por exemplo:

- Pode usar a Colmeia Apache para limpar uma parte dos dados, e o Apache Pig para limpar outra porção.
- Pode utilizar a Azure Data Factory para carregar dados na Base de Dados Azure SQL da Azure Data Lake Store.

A orquestração é necessária para executar o trabalho adequado no momento apropriado.

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie é um sistema de coordenação de fluxos de trabalho que gere as tarefas do Hadoop. Oozie funciona dentro de um cluster HDInsight e está integrado com a pilha Hadoop. Oozie apoia os trabalhos de Hadoop para Apache Hadoop MapReduce, Pig, Hive e Sqoop. Você pode usar Oozie para agendar trabalhos específicos para um sistema, como programas Java ou scripts de conchas.

Para mais informações, consulte [Oozie Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](../hdinsight-use-oozie-linux-mac.md). Ver também, [Operacionalizar o pipeline de dados.](../hdinsight-operationalize-data-pipeline.md)

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory fornece capacidades de orquestração sob a forma de plataforma como serviço (PaaS). O Azure Data Factory é um serviço de integração de dados baseado na cloud. Permite-lhe criar fluxos de trabalho baseados em dados para orquestrar e automatizar o movimento de dados e a transformação de dados.

Utilize a Fábrica de Dados Azure para:

1. Criar e programar fluxos de trabalho orientados para dados. Estes oleodutos ingebem dados de lojas de dados díspares.
1. Processe e transforme os dados utilizando serviços de computação como HDInsight ou Hadoop. Também pode utilizar Spark, Azure Data Lake Analytics, Azure Batch ou Azure Machine Learning para este passo.
1. Publique dados de saída em lojas de dados, como o Azure SQL Data Warehouse, para aplicações bi para consumir.

Para mais informações sobre a Azure Data Factory, consulte a [documentação](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Armazenamento de ficheiros e armazenamento de resultados

Os ficheiros de dados de origem são normalmente carregados num local no Armazenamento de Dados Azure ou no Armazenamento do Lago Azure Data. Os ficheiros estão geralmente num formato plano, como o CSV. Mas podem estar em qualquer formato.

### <a name="azure-storage"></a>Armazenamento do Azure

O Armazenamento Azure tem metas específicas de adaptabilidade. Consulte [a escalabilidade e os alvos de desempenho para](../../storage/blobs/scalability-targets.md) o armazenamento blob para obter mais informações. Para a maioria dos nós analíticos, o Armazenamento Azure é o melhor ao lidar com muitos ficheiros menores. Desde que esteja dentro dos limites da sua conta, o Azure Storage garante o mesmo desempenho, não importa o tamanho dos ficheiros. Pode armazenar terabytes de dados e ainda obter um desempenho consistente. Esta afirmação é verdadeira quer esteja a usar um subconjunto ou todos os dados.

O Azure Storage tem vários tipos de bolhas. Uma bolha de *apêndice* é uma ótima opção para armazenar registos web ou dados de sensores.

Várias bolhas podem ser distribuídas por muitos servidores para escalar o acesso aos mesmos. Mas uma única bolha é servida apenas por um único servidor. Embora as bolhas possam ser agrunadas logicamente em recipientes de bolhas, não há implicações de divisão deste agrupamento.

O Azure Storage tem uma camada WebHDFS API para o armazenamento de bolhas. Todos os serviços HDInsight podem aceder a ficheiros no armazenamento do Azure Blob para limpeza de dados e processamento de dados. Isto é semelhante ao modo como esses serviços usariam o Sistema de Ficheiros Distribuídos Hadoop (HDFS).

Os dados são normalmente ingeridos no Armazenamento Azure através da PowerShell, do Azure Storage SDK ou do AZCopy.

### <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

O Armazenamento do Lago Azure Data é um repositório gerido e hiperescala para dados de análise. É compatível com e usa um paradigma de design semelhante ao HDFS. Data Lake Storage oferece adaptabilidade ilimitada para a capacidade total e o tamanho de ficheiros individuais. É uma boa escolha quando se trabalha com ficheiros grandes, porque podem ser armazenados em vários nós. Os dados de divisão no Armazenamento de Data Lake são feitos nos bastidores. Obtém-se uma enorme entrada para executar trabalhos analíticos com milhares de executores simultâneos que lêem e escrevem eficientemente centenas de terabytes de dados.

Os dados são normalmente ingeridos no Armazenamento de Data Lake através da Azure Data Factory. Também pode utilizar SDKs de Armazenamento de Data Lake, o serviço AdlCopy, Apache DistCp ou Apache Sqoop. O serviço que escolher depende de onde estão os dados. Se estiver num aglomerado hadoop existente, poderá utilizar o Apache DistCp, o serviço AdlCopy ou a Azure Data Factory. Para obter dados no armazenamento da Blob Azure, poderá utilizar o Armazenamento de Lagos De Dados Azure .NET SDK, Azure PowerShell ou Azure Data Factory.

Data Lake Storage está otimizado para a ingestão de eventos através de Hubs de Eventos Azure ou Tempestade Apache.

### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para o upload de conjuntos de dados na gama terabyte, a latência da rede pode ser um grande problema. Isto é particularmente verdade se os dados vierem de um local no local. Nesses casos, pode utilizar estas opções:

- **Azure ExpressRoute:** Crie ligações privadas entre os datacenters azure e a sua infraestrutura no local. Estas ligações fornecem uma opção fiável para a transferência de grandes quantidades de dados. Para mais informações, consulte a [documentação do Azure ExpressRoute.](../../expressroute/expressroute-introduction.md)

- **Envio de dados a partir de discos rígidos:** Pode utilizar o [serviço Azure Import/Export para](../../storage/common/storage-import-export-service.md) enviar discos rígidos com os seus dados para um datacenter Azure. Os seus dados são enviados primeiro para o armazenamento da Blob Azure. Em seguida, pode utilizar a Azure Data Factory ou a ferramenta AdlCopy para copiar dados do armazenamento do Azure Blob para o Armazenamento de Data Lake.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

O Azure SQL Data Warehouse é uma escolha adequada para armazenar resultados preparados. Pode utilizar o Azure HDInsight para realizar esses serviços para o SQL Data Warehouse.

O Azure SQL Data Warehouse é uma loja de bases de dados relacional otimizada para cargas de trabalho analíticas. Escala com base em mesas divididas. As mesas podem ser divididas em vários nódosos. Os nódosos são selecionados no momento da criação. Podem escalar depois do facto, mas é um processo ativo que pode exigir movimento de dados. Para mais informações, consulte Gerir a computação no Armazém de [Dados SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase é uma loja chave/valor disponível no Azure HDInsight. É uma base de dados noSQL de código aberto que é construída em Hadoop e modelada após o Google BigTable. A HBase fornece acesso aleatório performante e uma forte consistência para grandes quantidades de dados não estruturados e semi-estruturados.

Como o HBase é uma base de dados sem esquemas, não é necessário definir colunas e tipos de dados antes de os utilizar. Os dados são armazenados nas filas de uma mesa, e são agrupados pela família das colunas.

O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. A HBase baseia-se na redundância de dados, processamento de lotes e outras funcionalidades que são fornecidas por aplicações distribuídas no ambiente Hadoop.

HBase é um bom destino para dados de sensores e registos para análise futura.

A adaptabilidade da Base HBase depende do número de nós no cluster HDInsight.

### <a name="azure-sql-databases"></a>Bases de dados Azure SQL

O Azure oferece três bases de dados relacionais PaaS:

- [A Base de Dados Azure SQL](../../sql-database/sql-database-technical-overview.md) é uma implementação do Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte o desempenho de [afinação na Base de Dados Azure SQL](../../sql-database/sql-database-performance-guidance.md).
- [Azure Database for MySQL](../../mysql/overview.md) é uma implementação da Oracle MySQL.
- [Base de Dados Azure para PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do PostgreSQL.

Adicione mais CPU e memória para aumentar estes produtos.  Também pode optar por utilizar discos premium com os produtos para um melhor desempenho em I/S.

## <a name="azure-analysis-services"></a>Azure Analysis Services

O Azure Analysis Services é um motor de dados analítico utilizado no suporte à decisão e na análise de negócios. Fornece os dados analíticos para relatórios empresariais e aplicações de clientes, como o Power BI. Os dados analíticos também funcionam com relatórios excel, SQL Server Reporting Services e outras ferramentas de visualização de dados.

Dimensione os cubos de análise alterando os níveis para cada cubo individual. Para mais informações, consulte os preços dos Serviços de [Análise de Azure.](https://azure.microsoft.com/pricing/details/analysis-services/)

## <a name="extract-and-load"></a>Extrair e carregar

Depois de existirem dados no Azure, pode utilizar muitos serviços para extraí-lo e carregá-lo noutros produtos. HDInsight suporta Sqoop e Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

O Apache Sqoop é uma ferramenta concebida para transferir eficientemente dados entre fontes de dados estruturadas, semi-estruturadas e não estruturadas.

A Sqoop utiliza o MapReduce para importar e exportar os dados, para fornecer uma operação paralela e tolerância à falha.

### <a name="apache-flume"></a>Apache Flume

O Apache Flume é um serviço distribuído, fiável e disponível para recolher, agregar e mover quantidades grandes de dados de registo. A sua arquitetura flexível baseia-se no streaming de fluxos de dados. O flume é robusto e tolerante a falhas com mecanismos de fiabilidade incapazes. Tem muitos mecanismos de falha e recuperação. O Flume usa um modelo de dados extensíveis simples que permite aplicação online e analítica.

O Apache Flume não pode ser usado com o Azure HDInsight. Mas, uma instalação hadoop no local pode usar flume para enviar dados para armazenamento Azure Blob ou Azure Data Lake Storage. Para mais informações, consulte [A Utilização do Flume Apache com HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformação

Depois de existirem dados no local escolhido, é necessário limpá-lo, combiná-lo ou prepará-lo para um padrão de utilização específico. Colmeia, Porco e Spark SQL são boas escolhas para este tipo de trabalho. São todos apoiados no HDInsight.

## <a name="next-steps"></a>Passos seguintes

- [Usando a Colmeia Apache como uma ferramenta ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Mova dados da Base de Dados Azure SQL para uma tabela Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)

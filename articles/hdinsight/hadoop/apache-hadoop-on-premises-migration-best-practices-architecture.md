---
title: 'Arquitetura: No local Apache Hadoop a Azure HDInsight'
description: Aprenda as melhores práticas de arquitetura para migrar no local os clusters Hadoop para Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 519dc53f6373ae1a9c8853d3fa90d137e9fa934b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435414"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrar nos locais apaches aglomerados de Hadoop para Azure HDInsight - melhores práticas de arquitetura

Este artigo dá recomendações para a arquitetura dos sistemas Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas Apache Hadoop para Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Utilize vários clusters otimizados pela carga de trabalho

Muitas implementações no local Apache Hadoop consistem de um único grande aglomerado que suporta muitas cargas de trabalho. Este cluster único pode ser complexo e pode exigir compromissos com os serviços individuais para que tudo funcione em conjunto. Migrar no local os clusters Hadoop para Azure HDInsight requer uma mudança de abordagem.

Os clusters Azure HDInsight são projetados para um tipo específico de utilização do cálculo. Como o armazenamento pode ser partilhado em vários clusters, é possível criar múltiplos clusters de computação otimizados para atender às necessidades de diferentes empregos. Cada tipo de cluster tem a configuração ideal para essa carga de trabalho específica. A tabela que se segue lista os tipos de cluster suportados em HDInsight e as cargas de trabalho correspondentes.

|Carga de trabalho|Tipo hdInsight Cluster|
|---|---|
|Processamento de lote (ETL / ELT)|Hadoop|
|Armazenamento de dados|Hadoop, Spark, Consulta Interativa|
|IoT / Streaming|Kafka, Tempestade, Faísca|
|Processamento de transações noSQL|HBase|
|Consultas interativas e mais rápidas com caching na memória|Interactive Query|
|Ciência dos Dados|Serviços ML, Faísca|

A tabela a seguir mostra os diferentes métodos que podem ser usados para criar um cluster HDInsight.

|Ferramenta|Baseado em navegador|Linha de Comandos|API REST|SDK|
|---|---|---|---|---|
|[Portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[SDK do .NET](/dotnet/api/overview/azure/hdinsight)||||X|
|[Python SDK](/python/api/overview/azure/hdinsight)||||X|
|[SDK Java](/java/api/overview/azure/hdinsight)||||X|
|[Modelos do Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Para obter mais informações, consulte os tipos de cluster de [artigos em HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Utilize clusters transitórios a pedido

Os clusters HDInsight podem não ser reutilizados por longos períodos de tempo. Para ajudar a economizar nos custos de recursos, o HDInsight suporta clusters transitórios a pedido, que podem ser eliminados uma vez que a carga de trabalho tenha sido concluída com sucesso.

Quando elimina um cluster, a conta de armazenamento associada e os metadados externos não são removidos. O cluster pode ser recriada mais tarde usando as mesmas contas de armazenamento e meta-lojas.

A Azure Data Factory pode ser usada para agendar a criação de clusters HDInsight a pedido. Para obter mais informações, consulte o artigo [Criar clusters Apache Hadoop a pedido em HDInsight utilizando a Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Desconsefeção do armazenamento do cálculo

As implementações típicas de Hadoop utilizam o mesmo conjunto de máquinas para armazenamento de dados e processamento de dados. Porque estão em 2000, a computação e o armazenamento devem ser dimensionados em conjunto.

Nos clusters HDInsight, o armazenamento não precisa de ser colocado com computação e pode estar no armazenamento do Azure, no Azure Data Lake Storage ou em ambos. A dissociação do armazenamento do cálculo tem os seguintes benefícios:

- Partilha de dados em clusters.
- O uso de aglomerados transitórios, uma vez que os dados não dependem do cluster.
- Custo de armazenamento reduzido.
- Dimensionamento de armazenamento e cálculo separadamente.
- Replicação de dados em todas as regiões.

Os clusters computativo são criados perto dos recursos da conta de armazenamento numa região de Azure para mitigar o custo de desempenho da separação do cálculo e armazenamento. As redes de alta velocidade tornam-no eficiente para os nós de computação acederem aos dados dentro do armazenamento do Azure.

## <a name="use-external-metadata-stores"></a>Utilizar arquivos de metadados externos

Existem duas principais metastões que funcionam com clusters HDInsight: [Apache Hive](https://hive.apache.org/) e [Apache Oozie.](https://oozie.apache.org/) A metástore de Colmeia é o repositório de esquemas central que pode ser usado por motores de processamento de dados, incluindo Hadoop, Spark, LLAP, Presto e Apache Pig. A metástaseta Oozie armazena detalhes sobre o agendamento e o estado em curso e os trabalhos de Hadoop concluídos.

HDInsight utiliza base de dados Azure SQL para metastões de Colmeia e Oozie. Existem duas formas de criar uma meta-loja em clusters HDInsight:

1. Metastlo padrão

    - Sem custo adicional.
    - A metastore é eliminada quando o cluster é eliminado.
    - A metastore não pode ser partilhada entre diferentes aglomerados.
    - Utiliza O Azure SQL DB básico, que tem um limite de cinco DTU.

1. Metástase externa personalizada

    - Especifique uma base de dados Azure SQL externa como a metastore.
    - Os clusters podem ser criados e eliminados sem perder metadados, incluindo detalhes de trabalho do esquema de Hive Oozie.
    - O db de metástasia única pode ser partilhado com diferentes tipos de clusters.
    - A metastore pode ser dimensionada conforme necessário.
    - Para obter mais informações, consulte [utilizar lojas de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Melhores práticas para a Hive Metastore

Algumas boas práticas de metástasias hdInsight hive são as seguintes:

- Utilize uma metásta externa personalizada para separar recursos de computação e metadados.
- Comece com uma instância Azure SQL de nível S2, que fornece 50 DTU e 250 GB de armazenamento. Se vir um estrangulamento, pode escalar a base de dados.
- Não partilhe a meta-loja criada para uma versão hdInsight cluster com clusters de uma versão diferente. Diferentes versões de Hive usam esquemas diferentes. Por exemplo, uma meta-loja não pode ser partilhada com os aglomerados hive 1.2 e Hive 2.1.
- Apoie periodicamente a metastore personalizada.
- Mantenha a meta-loja e o cluster HDInsight na mesma região.
- Monitorize a meta-loja para desempenho e disponibilidade utilizando ferramentas de monitorização da base de dados Azure SQL, como o portal Azure ou os registos do Azure Monitor.
- Execute o `ANALYZE TABLE` comando conforme necessário para gerar estatísticas para tabelas e colunas. Por exemplo, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Melhores práticas para diferentes cargas de trabalho

- Considere usar o cluster LLAP para consultas interativas de Hive com tempo de resposta melhorado [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) é uma nova funcionalidade na Hive 2.0 que permite o caching in-memory de consultas. LLAP torna as consultas de Hive muito mais rápidas, até [26x mais rápido que a Hive 1.x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Considere usar trabalhos de faísca no lugar dos trabalhos de Colmeia.
- Considere substituir consultas baseadas em impala por consultas LLAP.
- Considere substituir os empregos da MapReduce por empregos de Spark.
- Considere substituir os trabalhos de lote spark de baixa latência usando trabalhos de streaming estruturados de faíscas.
- Considere usar a Azure Data Factory (ADF) 2.0 para orquestração de dados.
- Considere Ambari para Gestão de Clusters.
- Altere o armazenamento de dados de HDFS no local para WASB ou ADLS ou ADFS para o processamento de scripts.
- Considere usar ranger RBAC em mesas de colmeia e auditoria.
- Considere usar o CosmosDB no lugar de MongoDB ou Cassandra.

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [Melhores práticas de infraestrutura para migração de Hadoop Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
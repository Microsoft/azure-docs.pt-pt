---
title: Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de arquitetura
description: Aprenda as práticas recomendadas de arquitetura para migrar clusters do Hadoop no local para Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: f1e2b9dfc329e67d94fba998a01d593b992ba90f
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886813"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de arquitetura

Este artigo fornece recomendações para a arquitetura de sistemas do Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar a migrar sistemas de Apache Hadoop no local para Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Utilizar vários clusters com otimização de carga de trabalho

Muitas implementações de Apache Hadoop no local são compostos por um único cluster grande que suporta muitas cargas de trabalho. Este cluster único pode ser complexo e pode exigir comprometimentos para os serviços individuais para fazer tudo funcionar em conjunto. Migração no local para Azure HDInsight clusters do Hadoop requer uma mudança na abordagem.

Clusters do HDInsight do Azure foram concebidos para um tipo específico de utilização de computação. Como o armazenamento pode ser compartilhado entre vários clusters, é possível criar vários clusters de computação com otimização de carga de trabalho para satisfazer as necessidades de diferentes tarefas. Cada tipo de cluster tem a configuração ideal para essa carga de trabalho específica. A tabela seguinte lista os tipos de cluster suportadas no HDInsight e as cargas de trabalho correspondentes.

|**Carga de trabalho**|**Tipo de Cluster do HDInsight**|
|---|---|
|Processamento em lotes (ETL / ELT)|Hadoop, Spark|
|Armazenamento de dados|Uma consulta interativa do Hadoop, Spark,|
|IoT / transmissão em fluxo|Kafka, Storm, Spark|
|Processamento de transações do NoSQL|HBase|
|Consultas interativo e mais rápidas com colocação em cache na memória|Interactive Query|
|Ciência de dados|Serviços de ML, Spark|

A tabela seguinte mostra os diferentes métodos que podem ser utilizados para criar um cluster do HDInsight.

|**Ferramenta**|**Baseadas no browser**|**Linha de comandos**|**API REST**|**SDK**|
|---|---|---|---|---|
|[Portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[CLI do Azure (vidor 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[SDK do .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[SDK Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Modelos Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Para obter mais informações, consulte o artigo [tipos no HDInsight do Cluster](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Utilizar clusters do transitórios sob demanda

Clusters do HDInsight podem ir não utilizados por longos períodos de tempo. Para ajudar a poupar nos custos de recursos, o HDInsight suporta clusters transitórios de sob demanda, o que podem ser eliminados depois da carga de trabalho foi concluída com êxito.

Quando eliminar um cluster, a conta de armazenamento associado e externo de metadados não são removidas. O cluster pode posteriormente voltar a criar com o mesmo contas de armazenamento e os arquivos de metadados.

O Azure Data Factory pode ser utilizado para agendar a criação de clusters de HDInsight a pedido. Para obter mais informações, consulte o artigo [criar clusters do Apache Hadoop a pedido no HDInsight com o Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Desassociar o armazenamento da computação

Implementações de Hadoop do típico no local utilizam o mesmo conjunto de máquinas para armazenamento de dados e processamento de dados. Como são colocalizados, computação e armazenamento devem ser dimensionados em conjunto.

Nos clusters do HDInsight, armazenamento não tem de ser foi colocalizado com a computação e o poderá no armazenamento do Azure, armazenamento do Azure Data Lake ou ambos. Desacoplamento armazenamento da computação tem as seguintes vantagens:

- Partilha de dados em clusters.
- Utilização de clusters transitórios, uma vez que os dados não são dependentes de cluster.
- Redução no custo de armazenamento.
- Dimensionar o armazenamento e computação em separado.
- Replicação de dados em várias regiões.

Computação clusters são criados perto dos recursos da conta de armazenamento numa região do Azure para reduzir o custo de desempenho de separar a computação e armazenamento. Redes de alta velocidade torná-lo no eficiente para os nós de computação aceder a dados de armazenamento do Azure.

## <a name="use-external-metadata-stores"></a>Utilizar arquivos de metadados externos


Existem dois metastores principais que funcionam com clusters do HDInsight: [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). O metastore Hive é o repositório do esquema central que pode ser utilizado por motores de processamento de dados, incluindo o Hadoop, Spark, o LLAP, Presto e o Apache Pig. O metastore Oozie armazena os detalhes sobre o agendamento e o estado de em curso e concluídas tarefas do Hadoop.


HDInsight utiliza a base de dados do Azure SQL para Hive e Oozie metastores. Existem duas formas de configurar um metastore em clusters do HDInsight:

1. Metastore predefinida

    - Sem custos adicionais.
    - Metastore será eliminado quando o cluster é eliminado.
    - Metastore não pode ser partilhado entre clusters diferentes.
    - Utiliza básica BD SQL do Azure, que tem um limite DTU cinco.

1. Metastore externo personalizado

    - Especifique uma base de dados de SQL do Azure externos como o metastore.
    - Clusters podem ser criados e eliminados sem perder a metadados, incluindo detalhes da tarefa do Hive do esquema Oozie.
    - Db de metastore único pode ser partilhado com diferentes tipos de clusters.
    - Metastore pode ser aumentado verticalmente, conforme necessário.
    - Para obter mais informações, consulte [utilizar arquivos de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Melhores práticas para Hive Metastore

Seguem-se algumas práticas recomendadas de metastore Hive do HDInsight:

- Utilize um metastore externo personalizado para recursos de computação separada e metadados.
- Começar com uma instância de SQL do Azure de escalão S2, que fornece 50 DTUS e 250 GB de armazenamento. Se vir um estrangulamento, pode dimensionar a base de dados.
- Não partilhe o metastore criado para uma versão de cluster do HDInsight com clusters de uma versão diferente. Diferentes versões de Hive usam esquemas diferentes. Por exemplo, um metastore não pode ser partilhado com clusters do Hive 1.2 e 2.1 do Hive.
- Criar cópias de segurança o metastore personalizado periodicamente.
- Mantenha o metastore e o cluster do HDInsight na mesma região.
- Monitorize o metastore para desempenho e disponibilidade usando ferramentas de monitorização de base de dados SQL do Azure, como o portal do Azure ou registos do Azure Monitor.
- Executar o **analisar tabela** comando conforme necessário para gerar estatísticas de tabelas e colunas. Por exemplo, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Melhores práticas para diferentes cargas de trabalho

- Considere a utilização de LLAP cluster para consultas interativas do Hive com o tempo de resposta melhoradas [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) é uma funcionalidade nova no 2.0 Hive, que permite a colocação em cache de memória de consultas. LLAP torna muito mais rápidas, até de consultas do Hive [26 x mais rapidamente do que o Hive 1.x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Considere a utilização de tarefas do Spark no lugar de tarefas do Hive.
- Considere substituir consultas baseadas em impala consultas LLAP.
- Considere a substituição de tarefas de MapReduce com tarefas do Spark.
- Considere a substituição de tarefas de lote de Spark de baixa latência através de tarefas de transmissão estruturada do Spark.
- Considere utilizar o Azure Data Factory (ADF) 2.0 para orquestração de dados.
- Considere o Ambari para gerenciamento de Cluster.
- Altere o armazenamento de dados do HDFS no local para WASB ou ADLS ou AD FS para o processamento de scripts.
- Considere utilizar o Ranger RBAC em tabelas de ramo de registo e auditoria.
- Considere a utilização do cosmos DB em vez do MongoDB ou o Cassandra.

## <a name="next-steps"></a>Passos Seguintes

Leia o artigo seguinte nesta série:

- [Práticas recomendadas de infraestrutura no local para a migração do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)

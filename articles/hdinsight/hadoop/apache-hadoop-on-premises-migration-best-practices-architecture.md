---
title: 'Arquitetura: Apache Hadoop local para o Azure HDInsight'
description: Aprenda as práticas recomendadas de arquitetura para migrar clusters Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9f532e7bbf9e24e431341344b3172c988f69bfc3
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951535"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrar clusters de Apache Hadoop locais para o Azure HDInsight-práticas recomendadas de arquitetura

Este artigo fornece recomendações para a arquitetura dos sistemas do Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar na migração de sistemas locais Apache Hadoop para o Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Usar vários clusters com otimização de carga de trabalho

Muitas implantações Apache Hadoop locais consistem em um único cluster grande que dá suporte a várias cargas de trabalho. Esse único cluster pode ser complexo e pode exigir comprometimentos para os serviços individuais para fazer tudo funcionar juntos. Migrar clusters Hadoop locais para o Azure HDInsight requer uma alteração na abordagem.

Os clusters do Azure HDInsight são projetados para um tipo específico de uso de computação. Como o armazenamento pode ser compartilhado entre vários clusters, é possível criar vários clusters de computação com otimização de carga de trabalho para atender às necessidades de diferentes trabalhos. Cada tipo de cluster tem a configuração ideal para essa carga de trabalho específica. A tabela a seguir lista os tipos de cluster com suporte no HDInsight e as cargas de trabalho correspondentes.

|Carga de trabalho|Tipo de cluster HDInsight|
|---|---|
|Processamento em lotes (ETL/ELT)|Hadoop, Spark|
|Data warehousing|Hadoop, Spark, consulta interativa|
|IoT/streaming|Kafka, Storm, Spark|
|Processamento transacional NoSQL|HBase|
|Consultas interativas e mais rápidas com cache na memória|Interactive Query|
|Ciência de Dados|Serviços de ML, Spark|

A tabela a seguir mostra os diferentes métodos que podem ser usados para criar um cluster HDInsight.

|Ferramenta|Baseado em navegador|Linha de Comandos|API REST|SDK|
|---|---|---|---|---|
|[Portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[CLI do Azure (ver 1,0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[O Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[SDK do .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[SDK Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Modelos de Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Para obter mais informações, consulte o artigo [tipos de cluster no HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Usar clusters temporários sob demanda

Os clusters HDInsight podem ficar inutilizados por longos períodos de tempo. Para ajudar a economizar em custos de recursos, o HDInsight dá suporte a clusters transitórios sob demanda, que podem ser excluídos depois que a carga de trabalho for concluída com êxito.

Quando você exclui um cluster, a conta de armazenamento associada e os metadados externos não são removidos. O cluster pode ser recriado posteriormente usando as mesmas contas de armazenamento e metaarmazenamentos.

Azure Data Factory pode ser usado para agendar a criação de clusters HDInsight sob demanda. Para obter mais informações, consulte o artigo [criar Apache Hadoop clusters sob demanda no HDInsight usando Azure data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Dissociar o armazenamento da computação

Implantações típicas do Hadoop locais usam o mesmo conjunto de computadores para armazenamento de dados e processamento de dados. Como eles são colocados, a computação e o armazenamento devem ser dimensionados juntos.

Em clusters HDInsight, o armazenamento não precisa estar colocalizado com computação e pode estar no armazenamento do Azure, Azure Data Lake Storage ou ambos. O desacoplamento do armazenamento da computação tem os seguintes benefícios:

- Compartilhamento de dados entre clusters.
- Uso de clusters transitórios, pois os dados não dependem do cluster.
- Custo de armazenamento reduzido.
- Dimensionando o armazenamento e a computação separadamente.
- Replicação de dados entre regiões.

Os clusters de computação são criados próximos aos recursos da conta de armazenamento em uma região do Azure para reduzir o custo de desempenho da separação da computação e do armazenamento. Redes de alta velocidade tornam eficiente para os nós de computação acessar os dados no armazenamento do Azure.

## <a name="use-external-metadata-stores"></a>Utilizar arquivos de metadados externos

Há dois metarepositórios principais que funcionam com clusters HDInsight: [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). O metastore do Hive é o repositório de esquema central que pode ser usado por mecanismos de processamento de dados, incluindo Hadoop, Spark, LLAP, presto e Apache Pig. O metastore do Oozie armazena detalhes sobre o agendamento e o status em andamento e trabalhos do Hadoop concluídos.

O HDInsight usa o banco de dados SQL do Azure para metastores do hive e do Oozie. Há duas maneiras de configurar um metastore em clusters HDInsight:

1. Metastore padrão

    - Sem custo adicional.
    - O metastore é excluído quando o cluster é excluído.
    - O metastore não pode ser compartilhado entre clusters diferentes.
    - Usa o banco de BD SQL básico do Azure, que tem um limite de cinco DTU.

1. Metastore externo personalizado

    - Especifique um banco de dados SQL do Azure externo como o metastore.
    - Os clusters podem ser criados e excluídos sem perder metadados, incluindo detalhes do trabalho do esquema do hive Oozie.
    - O BD de metastore único pode ser compartilhado com diferentes tipos de clusters.
    - O metastore pode ser escalado verticalmente conforme necessário.
    - Para obter mais informações, consulte [usar repositórios de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Práticas recomendadas para metastore do hive

Algumas práticas recomendadas do HDInsight metastore do Hive são as seguintes:

- Use um metastore externo personalizado para separar os recursos de computação e os metadados.
- Comece com uma instância do SQL do Azure de camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá dimensionar o banco de dados.
- Não compartilhe o metastore criado para uma versão de cluster do HDInsight com clusters de uma versão diferente. Versões diferentes do hive usam esquemas diferentes. Por exemplo, um metastore não pode ser compartilhado com os clusters Hive 1,2 e Hive 2,1.
- Faça backup do metastore personalizado periodicamente.
- Mantenha o metastore e o cluster HDInsight na mesma região.
- Monitore o metastore para obter desempenho e disponibilidade usando as ferramentas de monitoramento do banco de dados SQL do Azure, como portal do Azure ou Azure Monitor logs.
- Execute o comando `ANALYZE TABLE` conforme necessário para gerar estatísticas para tabelas e colunas. Por exemplo, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Práticas recomendadas para cargas de trabalho diferentes

- Considere usar o cluster LLAP para consultas interativas do hive com tempo de resposta melhorado [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) é um novo recurso do hive 2,0 que permite o cache em memória de consultas. O LLAP torna as consultas de Hive muito mais rápidas, até [26x mais rápidas do que o hive 1. x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Considere o uso de trabalhos do Spark em vez de trabalhos do hive.
- Considere a substituição de consultas baseadas em Impala por consultas LLAP.
- Considere a possibilidade de substituir trabalhos MapReduce por trabalhos do Spark.
- Considere a possibilidade de substituir trabalhos em lotes do Spark de baixa latência usando trabalhos de streaming estruturado do Spark.
- Considere o uso de Azure Data Factory (ADF) 2,0 para orquestração de dados.
- Considere Ambari para o gerenciamento de cluster.
- Altere o armazenamento de dados do HDFS local para WASB ou ADLS ou ADFS para processamento de scripts.
- Considere o uso do Ranger RBAC em tabelas e auditoria do hive.
- Considere o uso de CosmosDB no lugar do MongoDB ou do Cassandra.

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [Práticas recomendadas de infraestrutura para migração local para Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)

---
title: 'Arquitetura: No local Apache Hadoop para Azure HDInsight'
description: Aprenda as melhores práticas de arquitetura para migrar no local aglomerados Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934678"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrar no local Apache Hadoop clusters para Azure HDInsight - boas práticas de arquitetura

Este artigo dá recomendações para a arquitetura dos sistemas Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar na migração dos sistemas Apache Hadoop para o Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Utilize vários clusters otimizados para a carga de trabalho

Muitas implantações no local Apache Hadoop consistem de um único grande aglomerado que suporta muitas cargas de trabalho. Este único cluster pode ser complexo e pode exigir compromissos com os serviços individuais para que tudo funcione em conjunto. Migrar no local os clusters Hadoop para o Azure HDInsight requer uma mudança de abordagem.

Os clusters Azure HDInsight são projetados para um tipo específico de utilização da computação. Como o armazenamento pode ser partilhado em vários clusters, é possível criar múltiplos clusters computacionais otimizados para atender às necessidades de diferentes trabalhos. Cada tipo de cluster tem a configuração ideal para essa carga de trabalho específica. A tabela seguinte lista os tipos de cluster suportados no HDInsight e as cargas de trabalho correspondentes.

|Carga de trabalho|Tipo de cluster HDInsight|
|---|---|
|Processamento de lotes (ETL / ELT)|Hadoop|
|Armazenamento de dados|Hadoop, Spark, Consulta Interativa|
|IoT / Streaming|Kafka, Tempestade, Faísca|
|Processamento transacional NoSQL|HBase|
|Consultas interativas e mais rápidas com cache na memória|Interactive Query|
|Ciência dos Dados|Serviços ML, Faísca|

A tabela que se segue mostra os diferentes métodos que podem ser usados para criar um cluster HDInsight.

|Ferramenta|Baseado no navegador|Linha de Comandos|API REST|SDK|
|---|---|---|---|---|
|[Portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Fábrica de Dados Azure](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Modelos do Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Para mais informações, consulte os tipos de cluster do artigo [em HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Utilize aglomerados transitórios a pedido

Os clusters HDInsight podem ficar sem uso por longos períodos de tempo. Para ajudar a economizar nos custos de recursos, o HDInsight suporta clusters transitórios a pedido, que podem ser eliminados uma vez que a carga de trabalho tenha sido concluída com sucesso.

Quando elimina um cluster, a conta de armazenamento associada e os metadados externos não são removidos. O cluster pode ser posteriormente recriado usando as mesmas contas de armazenamento e meta-lojas.

A Azure Data Factory pode ser usada para agendar a criação de clusters HDInsight a pedido. Para mais informações, consulte o artigo [Criar aglomerados Apache Hadoop a pedido no HDInsight utilizando](../hdinsight-hadoop-create-linux-clusters-adf.md)a Azure Data Factory .

## <a name="decouple-storage-from-compute"></a>Dissociar o armazenamento da computação

As implementações típicas de Hadoop no local utilizam o mesmo conjunto de máquinas para armazenamento de dados e processamento de dados. Porque estão colocalizados, a computação e o armazenamento devem ser dimensionados em conjunto.

Nos clusters HDInsight, o armazenamento não precisa de ser colocalizado com computação e pode estar no armazenamento Azure, Azure Data Lake Storage ou ambos. A dissociação do armazenamento da computação tem os seguintes benefícios:

- Partilha de dados entre clusters.
- O uso de aglomerados transitórios, uma vez que os dados não dependem do cluster.
- Custo reduzido de armazenamento.
- Armazenamento e cálculo de escala separadamente.
- Replicação de dados em regiões.

Os clusters computacionais são criados perto dos recursos da conta de armazenamento numa região do Azure para mitigar o custo de desempenho da comparação da computação e armazenamento. As redes de alta velocidade tornam eficiente para os nódosos de computação aceder aos dados dentro do armazenamento azure.

## <a name="use-external-metadata-stores"></a>Utilizar arquivos de metadados externos

Existem duas principais metalojas que funcionam com clusters HDInsight: [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). A metaloja da Hive é o repositório de esquemacentral central que pode ser usado por motores de processamento de dados, incluindo Hadoop, Spark, LLAP, Presto e Apache Pig. A metaloja Oozie armazena detalhes sobre o agendamento e o estado do curso e completou os trabalhos de Hadoop.

O HDInsight utiliza a Base de Dados Azure SQL para as metastores da Hive e Oozie. Existem duas formas de configurar uma metaloja em clusters HDInsight:

1. Metaloja padrão

    - Sem custos adicionais.
    - A metaloja é eliminada quando o cluster é eliminado.
    - A metaloja não pode ser partilhada entre diferentes clusters.
    - Utiliza o Azure SQL DB básico, que tem um limite de cinco DTU.

1. Metaloja externa personalizada

    - Especifique uma base de dados Azure SQL externa como metaloja.
    - Os clusters podem ser criados e eliminados sem perder metadados, incluindo detalhes do trabalho da Hive schema Oozie.
    - A metastore db única pode ser partilhada com diferentes tipos de clusters.
    - A metaloja pode ser dimensionada conforme necessário.
    - Para mais informações, consulte [Utilize lojas de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Boas práticas para a Hive Metastore

Algumas boas práticas de metaloja HDInsight Hive são as seguintes:

- Utilize uma metaloja externa personalizada para separar recursos e metadados de cálculo.
- Comece com uma instância S2 tier Azure SQL, que fornece 50 DTU e 250 GB de armazenamento. Se vir um estrangulamento, pode aumentar a base de dados.
- Não partilhe a metaloja criada para uma versão de cluster HDInsight com clusters de uma versão diferente. Diferentes versões da Hive usam diferentes esquemas. Por exemplo, uma metaloja não pode ser partilhada com os clusters Hive 1.2 e Hive 2.1.
- Volte periodicamente à metaloja personalizada.
- Mantenha o cluster metastore e HDInsight na mesma região.
- Monitorize a metaloja para o desempenho e disponibilidade utilizando ferramentas de monitorização de bases de dados Azure SQL, como o portal Azure ou os registos do Monitor Azure.
- Execute `ANALYZE TABLE` o comando conforme necessário para gerar estatísticas para tabelas e colunas. Por exemplo, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Boas práticas para diferentes cargas de trabalho

- Considere utilizar o cluster LLAP para consultas interativas da Hive com tempo de resposta melhorado [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) é uma nova funcionalidade na Hive 2.0 que permite o cache na memória de consultas. Llap faz as consultas da Hive muito mais rápidas, até [26x mais rápido que a Hive 1.x em alguns casos.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)
- Considere usar os trabalhos de Spark no lugar dos trabalhos da Colmeia.
- Considere substituir consultas baseadas em impala por consultas LLAP.
- Considere substituir mapReduce empregos por spark jobs.
- Considere substituir os trabalhos de lote de faíscas de baixa latência utilizando trabalhos de streaming estruturados de faíscas.
- Considere utilizar a Azure Data Factory (ADF) 2.0 para orquestração de dados.
- Considere Ambari para gestão de clusters.
- Alterar o armazenamento de dados de HDFS no local para WASB ou ADLS ou ADFS para o processamento de scripts.
- Considere usar o Ranger RBAC em mesas de Colmeia e auditoria.
- Considere usar cosmosDB no lugar de MongoDB ou Cassandra.

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [Infraestruturas boas práticas para a migração de Hadoop Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)

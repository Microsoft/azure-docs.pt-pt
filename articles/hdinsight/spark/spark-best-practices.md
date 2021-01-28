---
title: Diretrizes apache spark em Azure HDInsight
description: Aprenda as diretrizes para a utilização do Apache Spark em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938805"
---
# <a name="apache-spark-guidelines"></a>Diretrizes apache spark

Este artigo fornece várias diretrizes para a utilização do Apache Spark no Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Como dirijo ou submeto empregos de Spark?

| Opção | Documentos |
|---|---|
| VSCode | [Use ferramentas de colmeia spark & para código de estúdio visual](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Tutorial: Use o Azure Toolkit para o IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Tutorial: Criar uma aplicação Scala Maven para Apache Spark em HDInsight usando IntelliJ](./apache-spark-create-standalone-application.md) |
| Blocos de notas do Zeppelin | [Utilizar blocos de notas do Apache Zeppelin com o cluster do Apache Spark no Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Submissão de trabalho remoto com Livy | [Utilizar a API REST do Apache Spark para submeter trabalhos remotos para um cluster do HDInsight Spark](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie é um sistema de trabalho e coordenação que gere os empregos da Hadoop.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Você pode usar Livy para executar conchas de faíscas interativas ou enviar trabalhos de lote para ser executado em Spark.|
|[Fábrica de dados Azure para Faísca Apache](../../data-factory/transform-data-using-spark.md)|A atividade Spark num oleoduto da Data Factory executa um programa Spark por conta própria ou [cluster HDInsight on-demand.|
|[Fábrica de Dados Azure para Colmeia Apache](../../data-factory/transform-data-using-hadoop-hive.md)|A atividade da Colmeia HDInsight num oleoduto da Data Factory executa consultas de Hive no seu próprio cluster HDInsight ou a pedido.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Como monitorizo e depuro os trabalhos da Spark?

| Opção | Documentos |
|---|---|
| Azure Toolkit for IntelliJ | [Falha no trabalho de depuração com Azure Toolkit para IntelliJ (pré-visualização)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit para IntelliJ através de SSH | [Depurar aplicações Apache Spark localmente ou remotamente num cluster HDInsight com o Azure Toolkit for IntelliJ através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit para IntelliJ através da VPN | [Utilize o Azure Toolkit para o IntelliJ para depurar as aplicações Apache Spark remotamente em HDInsight através da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Gráfico de trabalho no Servidor de Histórico de Faíscas apaches | [Utilizar o Servidor do Histórico do Apache Spark expandido para depurar e diagnosticar aplicações Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Como faço com que os meus trabalhos de Spark funcionam de forma mais eficiente?

| Opção | Documentos |
|---|---|
| IO Cache | [Melhorar o desempenho das cargas de trabalho da Apache Spark utilizando a cache IO IO do Azure HDInsight (Preview)](./apache-spark-improve-performance-iocache.md) |
| Opções de configuração | [Otimizar tarefas do Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Como me posso ligar a outros Serviços Azure?

| Opção | Documentos |
|---|---|
| Colmeia Apache em HDInsight | [Integre a Faísca Apache e a Colmeia Apache com o Conector do Armazém da Colmeia](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase no HDInsight | [Utilizar o Apache Spark para ler e escrever dados do Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka em HDInsight | [Tutorial: Utilizar a Transmissão em Fluxo Estruturada do Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Synapse Link para o Azure Cosmos DB](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>Quais são as minhas opções de armazenamento?

| Opção | Documentos |
|---|---|
| Armazenamento do Azure Data Lake Ger2 | [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Armazenamento do Azure Data Lake Ger1 | [Use Azure Data Lake Storage Gen1 com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Armazenamento de Blobs do Azure | [Utilizar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Próximos passos

* [Configurar as definições do Apache Spark](apache-spark-settings.md)
* [Otimizar os empregos da Apache Spark em HDInsight](apache-spark-perf.md)

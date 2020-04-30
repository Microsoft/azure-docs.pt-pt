---
title: Diretrizes da Apache Spark sobre Azure HDInsight
description: Aprenda as diretrizes para utilizar a Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 424a0cfd02cfce9fb87bc3e21d7b067740df8218
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509419"
---
# <a name="apache-spark-guidelines"></a>Diretrizes da Faísca Apache

Este artigo fornece várias diretrizes para a utilização de Apache Spark no Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Como posso executar ou submeter empregos de Spark?

| Opção | Documentos |
|---|---|
| VSCode | [Use ferramentas de colmeia de faíscas & para código de estúdio visual](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Tutorial: Utilize o Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Tutorial: Criar uma aplicação Scala Maven para Apache Spark em HDInsight usando intelliJ](./apache-spark-create-standalone-application.md) |
| Blocos de notas do Zeppelin | [Utilizar blocos de notas do Apache Zeppelin com o cluster do Apache Spark no Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Submissão de trabalho remoto com Livy | [Utilizar a API REST do Apache Spark para submeter trabalhos remotos para um cluster do HDInsight Spark](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie é um sistema de fluxo de trabalho e coordenação que gere os empregos de Hadoop.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Você pode usar Livy para executar conchas de faísca interativaou submeter trabalhos de lote para ser executado em Spark.|
|[Fábrica de Dados Azure para Apache Spark](/../data-factory/transform-data-using-spark.md)|A atividade spark em um pipeline Data Factory executa um programa Spark por conta própria ou [cluster HDInsight on demand.|
|[Fábrica de Dados Azure para A Colmeia Apache](/../data-factory/transform-data-using-hadoop-hive.md)|A atividade da Hive HDInsight num pipeline data factory executa consultas de Hive por conta própria ou por um cluster HDInsight a pedido.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Como posso monitorizar e depurar empregos de Spark?

| Opção | Documentos |
|---|---|
| Azure Toolkit for IntelliJ | [Falha no trabalho de faísca com Kit de Ferramentas Azure para IntelliJ (pré-visualização)](apache-spark-intellij-tool-failure-debug.md) |
| Kit de ferramentas Azure para IntelliJ através de SSH | [Depurar aplicações Apache Spark localmente ou remotamente num cluster HDInsight com o Azure Toolkit for IntelliJ através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Kit de ferramentas Azure para IntelliJ através da VPN | [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações Apache Spark remotamente no HDInsight através de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Gráfico de trabalho no Apache Spark History Server | [Utilizar o Servidor do Histórico do Apache Spark expandido para depurar e diagnosticar aplicações Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Como faço com que os meus trabalhos de Spark corram mais eficientemente?

| Opção | Documentos |
|---|---|
| IO Cache | [Melhorar o desempenho das cargas de trabalho da Apache Spark utilizando o Cache Azure HDInsight IO (Pré-visualização)](./apache-spark-improve-performance-iocache.md) |
| Opções de configuração | [Otimizar tarefas do Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Como me ligo a outros Serviços Azure?

| Opção | Documentos |
|---|---|
| Colmeia Apache no HDInsight | [Integre a Apache Spark e a Apache Hive com o Conector de Armazém da Colmeia](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase no HDInsight | [Utilizar o Apache Spark para ler e escrever dados do Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka no HDInsight | [Tutorial: Utilizar a Transmissão em Fluxo Estruturada do Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementar uma arquitetura lambda na plataforma Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Quais são as minhas opções de armazenamento?

| Opção | Documentos |
|---|---|
| Armazenamento do Data Lake Ger2 | [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Armazenamento do Data Lake Ger1 | [Utilize data lake storage Gen1 com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Armazenamento de Blobs do Azure | [Utilizar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Passos seguintes

* [Configurar as definições do Apache Spark](apache-spark-settings.md)
* [Otimizar os empregos da Apache Spark no HDInsight](apache-spark-perf.md)

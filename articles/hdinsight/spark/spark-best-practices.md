---
title: Apache Spark boas práticas no Azure HDInsight
description: Aprenda as melhores práticas para utilizar a Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71106128"
---
# <a name="apache-spark-best-practices"></a>Boas práticas de Apache Spark

Este artigo fornece várias boas práticas para a utilização de Apache Spark no Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Como posso executar ou submeter empregos de Spark?

| Opção | Documentos |
|---|---|
| VSCode | [Use ferramentas de colmeia de faíscas & para código de estúdio visual](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Tutorial: Utilize o Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Tutorial: Criar uma aplicação Scala Maven para Apache Spark em HDInsight usando intelliJ](./apache-spark-create-standalone-application.md) |
| Blocos de notas do Zeppelin | [Utilizar blocos de notas do Apache Zeppelin com o cluster do Apache Spark no Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Submissão de trabalho remoto com Livy | [Utilizar a API REST do Apache Spark para submeter trabalhos remotos para um cluster do HDInsight Spark](./apache-spark-livy-rest-interface.md) |

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

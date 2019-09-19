---
title: Apache Spark práticas recomendadas no Azure HDInsight
description: Conheça as práticas recomendadas para usar Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106128"
---
# <a name="apache-spark-best-practices"></a>Práticas recomendadas de Apache Spark

Este artigo fornece várias práticas recomendadas para o uso de Apache Spark no Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Como fazer executar ou enviar trabalhos do Spark?

| Opção | Documentos |
|---|---|
| VSCode | [Use as ferramentas do Spark & Hive para Visual Studio Code](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Tutorial: Carregar dados e executar consultas em um cluster Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Tutorial: Usar Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para um cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Tutorial: Criar um aplicativo de escala de aplicativos do Maven para Apache Spark no HDInsight usando IntelliJ](./apache-spark-create-standalone-application.md) |
| Blocos de anotações do Zeppelin | [Utilizar blocos de notas do Apache Zeppelin com o cluster do Apache Spark no Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Envio de trabalho remoto com Livy | [Utilizar a API REST do Apache Spark para submeter trabalhos remotos para um cluster do HDInsight Spark](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Como fazer monitorar e depurar trabalhos do Spark?

| Opção | Documentos |
|---|---|
| Azure Toolkit for IntelliJ | [Falha na depuração do trabalho Spark com Azure Toolkit for IntelliJ (versão prévia)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ por meio de SSH | [Depurar aplicações Apache Spark localmente ou remotamente num cluster HDInsight com o Azure Toolkit for IntelliJ através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ por meio de VPN | [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente no HDInsight por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Grafo de trabalho no servidor de histórico de Apache Spark | [Utilizar o Servidor do Histórico do Apache Spark expandido para depurar e diagnosticar aplicações Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Como fazer fazer com que meus trabalhos do Spark sejam executados com mais eficiência?

| Opção | Documentos |
|---|---|
| Cache de e/s | [Melhorar o desempenho de cargas de trabalho de Apache Spark usando o cache de e/s do Azure HDInsight (versão prévia)](./apache-spark-improve-performance-iocache.md) |
| Opções de configuração | [Otimizar Apache Spark trabalhos](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Como fazer conectar-se a outros serviços do Azure?

| Opção | Documentos |
|---|---|
| Apache Hive no HDInsight | [Integrar Apache Spark e Apache Hive com o conector do depósito do hive](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase no HDInsight | [Usar Apache Spark para ler e gravar dados do Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka no HDInsight | [Tutorial: Usar Apache Spark streaming estruturado com Apache Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementar uma arquitetura lambda na plataforma Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Quais são minhas opções de armazenamento?

| Opção | Documentos |
|---|---|
| Armazenamento do Data Lake Ger2 | [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Armazenamento do Data Lake Ger1 | [Usar Data Lake Storage Gen1 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Armazenamento de Blobs do Azure | [Utilizar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Passos seguintes

* [Definir configurações de Apache Spark](apache-spark-settings.md)
* [Otimizar Apache Spark trabalhos no HDInsight](apache-spark-perf.md)

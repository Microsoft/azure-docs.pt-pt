---
title: Otimizar os empregos da Spark para o desempenho - Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho dos clusters Apache Spark em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 567fc2637538408d9727d07d3185a5b0e3cf20c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929932"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Otimizar os empregos da Apache Spark em HDInsight

Este artigo fornece uma visão geral de estratégias para otimizar os empregos da Apache Spark no Azure HDInsight.

## <a name="overview"></a>Descrição geral

O desempenho dos seus trabalhos apache spark depende de vários fatores. Estes fatores de desempenho incluem: como os seus dados são armazenados, como o cluster é configurado, e as operações que são usadas no processamento dos dados.

Os desafios comuns que poderá enfrentar incluem: restrições de memória devido a executores de tamanho indevido, operações de longa duração e tarefas que resultam em operações cartesianas.

Existem também muitas otimizações que podem ajudá-lo a superar estes desafios, como o caching, e permitir distorcer dados.

Em cada um dos seguintes artigos, pode encontrar informações sobre diferentes aspetos da otimização do Spark.

* [Otimizar o armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimizar o processamento de dados para Apache Spark](optimize-data-processing.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster HDInsight para o Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Passos seguintes

* [Depurar trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerir recursos para um cluster Apache Spark em HDInsight](apache-spark-resource-manager.md)
* [Configurar as definições do Apache Spark](apache-spark-settings.md)

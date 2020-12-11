---
title: Otimizar os empregos da Spark para o desempenho - Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho dos clusters Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: d808339dd842e88e74efce5d56d12bc5250eb238
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029299"
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

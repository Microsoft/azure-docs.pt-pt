---
title: Otimize os trabalhos da Spark para o desempenho - Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho dos clusters Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780113"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Otimizar os empregos da Apache Spark no HDInsight

Este artigo fornece uma visão geral das estratégias para otimizar os empregos da Apache Spark no Azure HDInsight.

## <a name="overview"></a>Descrição geral

O desempenho dos seus trabalhos apache spark depende de múltiplos fatores. Estes fatores de desempenho incluem: como os seus dados são armazenados, como o cluster é configurado, e as operações que são usadas no processamento dos dados.

Os desafios comuns que pode enfrentar incluem restrições de memória devido a executores de tamanho impróprio, operações de longa duração e tarefas que resultam em operações cartesianas.

Existem também várias estratégias que podem ajudá-lo a superar estes desafios, como o caching, e permitir a distorção de dados.

Em cada um dos seguintes artigos, você pode encontrar desafios e soluções comuns para um aspeto diferente da otimização da faísca.

* [Otimizar o armazenamento de dados](optimize-data-storage.md)
* [Otimizar o processamento de dados](optimize-data-processing.md)
* [Otimizar o uso da memória](optimize-memory-usage.md)
* [Otimizar a configuração do cluster](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Passos seguintes

* [Depurar trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerir recursos para um cluster Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Configurar as definições do Apache Spark](apache-spark-settings.md)

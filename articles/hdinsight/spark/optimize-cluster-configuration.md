---
title: Otimizar a configuração do cluster Apache Spark - Azure HDInsight
description: Aprenda a configurar o seu cluster Apache Spark para maximizar a produção no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 80abc052bc8fa731a1cea0d3ecb523aedb46cf05
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031786"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Otimização de configuração de cluster para Apache Spark

Este artigo discute como otimizar a configuração do seu cluster Apache Spark para melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Descrição geral

Dependendo da carga de trabalho do seu cluster Spark, pode determinar que uma configuração de faísca não padrão resultaria numa execução de trabalho mais otimizada do Spark.  Faça testes de benchmark com cargas de trabalho de amostra para validar quaisquer configurações de cluster não padrão.

Aqui estão alguns parâmetros comuns que pode ajustar:

|Parâmetro |Descrição |
|---|---|
|--executores num-|Define o número adequado de executores.|
|--executor-núcleos|Define o número de núcleos para cada executor. Normalmente, deve ter executores de tamanho médio, já que outros processos consomem parte da memória disponível.|
|--executor-memória|Define o tamanho da memória para cada executor, que controla o tamanho da pilha em YARN. Deixe alguma memória para a execução.|

## <a name="select-the-correct-executor-size"></a>Selecione o tamanho correto do executor

Ao decidir a configuração do executor, considere a recolha de lixo Java (GC) por cima.

* Fatores para reduzir o tamanho do executor:
    1. Reduza o tamanho da pilha abaixo de 32 GB para manter a carga de GC < 10%.
    2. Reduza o número de núcleos para manter a GC < 10%.

* Fatores para aumentar o tamanho do executor:
    1. Reduzir a sobrecarga de comunicação entre os executores.
    2. Reduza o número de ligações abertas entre executores (N2) em agrupamentos maiores (>100 executores).
    3. Aumente o tamanho da pilha para acomodar para tarefas intensivas de memória.
    4. Opcional: Reduza a memória por executor.
    5. Opcional: Aumentar a utilização e a concordância por subscrição excessiva da CPU.

Regra geral, ao selecionar o tamanho do executor:

1. Comece com 30 GB por executor e distribua os núcleos de máquina disponíveis.
2. Aumente o número de núcleos executor para agrupamentos maiores (> 100 executores).
3. Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como sobrecargas de GC.

Ao executar consultas simultâneas, considere:

1. Comece com 30 GB por executor e todos os núcleos de máquina.
2. Crie múltiplas aplicações paralelas de Spark por subscrição excessiva de CPU (melhoria de latência de cerca de 30%).
3. Distribuir consultas através de aplicações paralelas.
4. Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como sobrecargas de GC.

Para obter mais informações sobre a utilização de Ambari para configurar executores, consulte [as definições de Apache Spark - Executores de faíscas](apache-spark-settings.md#configuring-spark-executors).

Monitorize o desempenho da consulta para outliers ou outros problemas de desempenho, olhando para a visão da linha do tempo. Também gráfico SQL, estatísticas de emprego, e assim por diante. Para obter informações sobre a depuração de empregos spark usando o YARN e o servidor Spark History, consulte [os empregos de Debug Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md). Para obter dicas sobre a utilização do Servidor de Linha do Tempo YARN, consulte [os registos de aplicações do YarN do Access Apache Hadoop](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Às vezes, um ou alguns dos executores são mais lentos do que os outros, e as tarefas demoram muito mais tempo a ser executadas. Esta lentidão acontece frequentemente em aglomerados maiores (> 30 nosdes). Neste caso, divida o trabalho num maior número de tarefas para que o programador possa compensar tarefas lentas. Por exemplo, ter pelo menos o dobro das tarefas que o número de núcleos executor na aplicação. Também pode permitir a execução especulativa de tarefas com `conf: spark.speculation = true` .

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o processamento de dados para Apache Spark](optimize-cluster-configuration.md)
* [Otimizar o armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)

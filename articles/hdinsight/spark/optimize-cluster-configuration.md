---
title: Otimizar a configuração do cluster Apache Spark - Azure HDInsight
description: Aprenda a configurar o seu cluster Apache Spark para maximizar a entrada no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 4227f80b9ac153aee72c518bf6f93efdce7234d2
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791086"
---
# <a name="cluster-configuration-optimization"></a>Otimização da configuração do cluster

Este artigo discute como otimizar a configuração do seu cluster Apache Spark para melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Descrição geral

Dependendo da carga de trabalho do cluster Spark, poderá determinar que uma configuração spark não predefinida resultaria numa execução de trabalho spark mais otimizada.  Faça testes de benchmark com cargas de trabalho da amostra para validar quaisquer configurações de cluster não predefinidas.

Aqui estão alguns parâmetros comuns que pode ajustar:

|Parâmetro |Descrição |
|---|---|
|--executores num-num|Define o número adequado de executores.|
|--executor-núcleos|Define o número de núcleos para cada executor. Normalmente deve ter executores de tamanho médio, uma vez que outros processos consomem parte da memória disponível.|
|-- executor-memória|Define o tamanho da memória para cada executor, que controla o tamanho da pilha no ARN. Deixe um pouco de memória para execução.|

## <a name="select-the-correct-executor-size"></a>Selecione o tamanho correto do executor

Ao decidir a configuração do seu executor, considere a sobrecarga da coleção de lixo Java (GC).

* Fatores para reduzir o tamanho do executor:
    1. Reduza o tamanho da pilha abaixo de 32 GB para manter a sobrecarga de GC < 10%.
    2. Reduza o número de núcleos para manter a sobrecarga de GC < 10%.

* Fatores para aumentar o tamanho do executor:
    1. Reduza a sobrecarga de comunicação entre executores.
    2. Reduzir o número de ligações abertas entre executores (N2) em clusters maiores (>100 executores).
    3. Aumente o tamanho da pilha para acomodar tarefas intensivas de memória.
    4. Opcional: Reduzir a sobrecarga da memória por executor.
    5. Opcional: Aumentar a utilização e a conmoeda através da subscrição excessiva do CPU.

Regra geral, ao selecionar o tamanho do executor:

1. Comece com 30 GB por executor e distribua os núcleos disponíveis da máquina.
2. Aumentar o número de núcleos de executor estoque para clusters maiores (> 100 executores).
3. Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como a sobrecarga de GC.

Ao executar consultas simultâneas, considere:

1. Comece com 30 GB por executor e todos os núcleos de máquinas.
2. Crie múltiplas aplicações paralelas spark ao subscrever CPU (cerca de 30% de melhoria da latência).
3. Distribua consultas através de aplicações paralelas.
4. Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como a sobrecarga de GC.

Para obter mais informações sobre a utilização de Ambari para configurar executores, consulte [as definições de Apache Spark - Executores de faíscas](apache-spark-settings.md#configuring-spark-executors).

Monitorize o desempenho da consulta para fora-da-lei ou outros problemas de desempenho, olhando para a visão da linha do tempo. Também gráfico SQL, estatísticas de emprego, e assim por diante. Para obter informações sobre depuração de trabalhos spark usando O Fio e o servidor Spark History, consulte [os trabalhos de Debug Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md). Para obter dicas sobre a utilização do Servidor de Linha do Tempo Do Fio YARN, consulte os registos de [aplicações do Access Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Às vezes, um ou alguns dos executores são mais lentos que os outros, e as tarefas demoram muito mais tempo a executar. Esta lentidão acontece frequentemente em aglomerados maiores (> 30 nós). Neste caso, divida o trabalho num maior número de tarefas para que o programador possa compensar tarefas lentas. Por exemplo, ter pelo menos o dobro das tarefas do executor na aplicação. Também pode permitir a execução especulativa de tarefas com `conf: spark.speculation = true` .

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o processamento de dados para a Apache Spark](optimize-cluster-configuration.md)
* [Otimizar o armazenamento de dados para a Apache Spark](optimize-data-storage.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)
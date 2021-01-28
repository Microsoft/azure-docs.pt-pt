---
title: Otimizar o processamento de dados para Apache Spark - Azure HDInsight
description: Saiba como escolher as operações mais eficientes para processar os seus dados sobre Apache Spark com Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944725"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Otimização de processamento de dados para Apache Spark

Este artigo discute como otimizar a configuração do seu cluster Apache Spark para melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Descrição geral

Se tiver trabalhos lentos num Join ou Shuffle, a causa é provavelmente *distorcer dados.* O distorce de dados é assimetria nos dados do seu trabalho. Por exemplo, um trabalho de mapa pode demorar 20 segundos. Mas gerir um trabalho onde os dados são unidos ou baralhados leva horas. Para corrigir o distorcer de dados, deve salgar toda a tecla ou utilizar um *sal isolado* para apenas alguns subconjuntos de teclas. Se estiver a utilizar um sal isolado, deve filtrar ainda mais o subconjunto de teclas salgadas em juntas de mapa. Outra opção é introduzir uma coluna de balde e pré-agregar primeiro em baldes.

Outro fator que causa a junção lenta pode ser o tipo de junção. Por predefinição, a Spark utiliza o `SortMerge` tipo de junção. Este tipo de junção é mais adequado para grandes conjuntos de dados. Mas é de outra forma computacionalmente caro porque deve primeiro separar os lados esquerdo e direito dos dados antes de os fundir.

Uma `Broadcast` junção é mais adequada para conjuntos de dados menores, ou onde um lado da junta é muito menor do que o outro lado. Este tipo de junção transmite um lado para todos os executores, e assim requer mais memória para transmissões em geral.

Pode alterar o tipo de junção na sua configuração definindo `spark.sql.autoBroadcastJoinThreshold` , ou pode definir uma sugestão de junção utilizando as APIs dataframe `dataframe.join(broadcast(df2))` ().

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Se estiver a usar mesas de balde, então tem um terceiro tipo de junção, a `Merge` junção. Um conjunto de dados pré-dividido e pré-classificado corretamente irá saltar a fase de classificação cara a partir de uma `SortMerge` junção.

A ordem de junção importa, particularmente em consultas mais complexas. Comece com as juntas mais seletivas. Além disso, mova-se junta-se que aumente o número de filas após agregações, quando possível.

Para gerir o paralelismo para a cartesia, pode adicionar estruturas aninhadas, janelas e talvez saltar um ou mais passos no seu Spark Job.

## <a name="optimize-job-execution"></a>Otimizar a execução de tarefas

* Cache, se necessário, por exemplo, se utilizar os dados duas vezes, em seguida, cache-os.
* Variáveis de transmissão a todos os executores. As variáveis só são serializadas uma vez, resultando em procuras mais rápidas.
* Utilize a piscina de rosca no controlador, o que resulta numa operação mais rápida para muitas tarefas.

Monitorize regularmente os seus trabalhos de funcionamento para problemas de desempenho. Se precisar de mais informações sobre determinadas questões, considere uma das seguintes ferramentas de perfis de desempenho:

* [A Ferramenta Intel PAL](https://github.com/intel-hadoop/PAT) monitoriza o uso de CPU, armazenamento e largura de banda de rede.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) perfis Spark e código executor.

A chave para o desempenho da consulta Spark 2.x é o motor Tungsten, que depende da geração de códigos em todo o estágio. Em alguns casos, a geração de códigos em todo o estágio pode ser desativada. Por exemplo, se utilizar um tipo não mutável `string` () na expressão de agregação, `SortAggregate` aparece em vez de `HashAggregate` . Por exemplo, para um melhor desempenho, experimente o seguinte e, em seguida, reeguir a geração de código:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Próximos passos

* [Otimizar o armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)

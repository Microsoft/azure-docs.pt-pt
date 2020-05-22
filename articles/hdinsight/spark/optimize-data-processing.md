---
title: Otimizar o processamento de dados para Apache Spark - Azure HDInsight
description: Saiba como escolher as operações mais eficientes para processar os seus dados no Apache Spark com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1e48573c2b73c10f10f665b5b91759d54d79acdd
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791065"
---
# <a name="data-processing-optimization"></a>Otimização do processamento de dados

Este artigo discute como otimizar a configuração do seu cluster Apache Spark para melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Descrição geral

Se tiver empregos lentos num Join or Shuffle, a causa é provavelmente *desvirtuada*por dados . A distorção de dados é assimetria nos dados do seu trabalho. Por exemplo, um trabalho de mapa pode levar 20 segundos. Mas gerir um trabalho onde os dados são unidos ou baralhados leva horas. Para corrigir o enviesamento dos dados, deve salgar toda a chave ou utilizar um *sal isolado* para apenas um subconjunto de teclas. Se estiver a utilizar um sal isolado, deve filtrar ainda mais para isolar o seu subconjunto de chaves salgadas no mapa. Outra opção é introduzir uma coluna de balde e pré-agregar em baldes primeiro.

Outro fator que causa a juntas lentas pode ser o tipo de união. Por padrão, a Spark utiliza o tipo de `SortMerge` união. Este tipo de adesão é mais adequado para grandes conjuntos de dados. Mas, de outra forma, é computacionalmente caro porque primeiro deve separar os lados esquerdo e direito dos dados antes de os fundir.

Uma `Broadcast` adesão é mais adequada para conjuntos de dados menores, ou onde um lado da junta é muito menor do que o outro lado. Este tipo de união transmite um lado para todos os executores, e assim requer mais memória para transmissões em geral.

Pode alterar o tipo de união na sua configuração definindo, ou pode definir uma dica de `spark.sql.autoBroadcastJoinThreshold` união utilizando as APIs dataFrame `dataframe.join(broadcast(df2))` ().

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

Se estáa usando mesas baldes, então tem um terceiro tipo de adesão, o `Merge` juntamo. Um conjunto de dados pré-dividido e pré-ordenado salta a fase de classificação cara de uma `SortMerge` adesão.

A ordem de adesão importa, particularmente em consultas mais complexas. Comece com as juntas mais seletivas. Além disso, move-se que aumenta o número de linhas após agregações quando possível.

Para gerir o paralelismo para uniões cartesianas, pode adicionar estruturas aninhadas, janelas e talvez saltar um ou mais passos no seu Spark Job.

## <a name="optimize-job-execution"></a>Otimizar a execução de emprego

* Cache conforme necessário, por exemplo, se utilizar os dados duas vezes, em seguida, cache-os.
* Transmita variáveis a todos os executores. As variáveis só são serializadas uma vez, resultando em procurações mais rápidas.
* Utilize a piscina de rosca no condutor, o que resulta numa operação mais rápida para muitas tarefas.

Monitorize regularmente os seus trabalhos de funcionamento para problemas de desempenho. Se precisar de mais informações sobre certas questões, considere uma das seguintes ferramentas de perfis de desempenho:

* [A Ferramenta Intel PAL](https://github.com/intel-hadoop/PAT) monitoriza o CPU, o armazenamento e a largura de banda da rede.
* [Oracle Java 8 Perfis de Controlo](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) de Missão Spark e código executor.

A chave para o desempenho da consulta Spark 2.x é o motor Tungsten, que depende da geração de códigos em toda a fase. Em alguns casos, a geração de códigos em fase inteira pode ser desativada. Por exemplo, se utilizar um tipo não mutável () na expressão de `string` agregação, `SortAggregate` aparece em vez de `HashAggregate` . Por exemplo, para um melhor desempenho, tente o seguinte e, em seguida, reativar a geração de códigos:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o armazenamento de dados para a Apache Spark](optimize-data-storage.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)
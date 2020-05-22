---
title: Otimizar o uso da memória em Apache Spark - Azure HDInsight
description: Aprenda a otimizar o uso da memória em Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 6992359bbef743bffba0ccbb7f5db3f865e7993b
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791037"
---
# <a name="memory-usage-optimization"></a>Otimização do uso da memória

Este artigo discute como otimizar a gestão da memória do seu cluster Apache Spark para melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Descrição geral

A faísca funciona colocando dados na memória. Assim, gerir os recursos de memória é um aspeto fundamental da otimização da execução de empregos spark.  Existem várias técnicas que pode aplicar para usar a memória do seu cluster de forma eficiente.

* Prefira divisórias de dados mais pequenas e contabilize o tamanho, tipos e distribuição de dados na sua estratégia de partilha.
* Considere a mais recente, mais [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) eficiente, em vez da serialização padrão de Java.
* Prefira usar o ARN, uma vez que se separa `spark-submit` por lote.
* Monitore e sintonize as definições de configuração spark.

Para a sua referência, a estrutura da memória Spark e alguns parâmetros de memória do executor chave são mostrados na imagem seguinte.

## <a name="spark-memory-considerations"></a>Considerações de memória de faísca

Se estiver a usar o YARN Apache Hadoop, então o YARN controla a memória utilizada por todos os recipientes em cada nó de Faísca.  O diagrama que se segue mostra os objetos-chave e as suas relações.

![Gestão da memória de faísca de fios](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Para endereçar mensagens "fora de memória", tente:

* Reveja a GEStão DO DAG Shuffles. Reduza por dados de origem redutor do lado do mapa, pré-partição (ou bucketize), maximize as baralhadas individuais e reduza a quantidade de dados enviados.
* Prefira `ReduceByKey` com o seu limite de memória fixo para , que proporciona `GroupByKey` agregações, janelas e outras funções, mas tem um limite de memória ilimitado.
* Prefere, `TreeReduce` que trabalha mais nos executores ou divisórias, `Reduce` para, o que faz todo o trabalho no motorista.
* Utilize dataFrames em vez dos objetos RDD de nível inferior.
* Criar Tipos Complexos que encapsulam ações, como "Top N", várias agregações ou operações de janela.

Para obter passos adicionais de resolução de problemas, consulte [exceções OutOfMemoryError para Apache Spark em Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o processamento de dados para a Apache Spark](optimize-cluster-configuration.md)
* [Otimizar o armazenamento de dados para a Apache Spark](optimize-data-storage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)
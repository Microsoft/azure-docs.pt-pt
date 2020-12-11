---
title: Otimizar o uso da memória em Apache Spark - Azure HDInsight
description: Aprenda a otimizar o uso da memória em Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: ed10648fb115ae0dc12300f3e48db165fcb4c215
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028160"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Otimização do uso da memória para Apache Spark

Este artigo discute como otimizar a gestão de memória do seu cluster Apache Spark para melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Descrição geral

A faísca funciona colocando dados na memória. Assim, gerir os recursos de memória é um aspeto fundamental para otimizar a execução de empregos spark.  Existem várias técnicas que pode aplicar para usar a memória do seu cluster de forma eficiente.

* Prefere divisórias de dados mais pequenas e responsável pelo tamanho dos dados, tipos e distribuição na sua estratégia de partição.
* Considere o mais recente, mais eficiente [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) , em vez da serialização padrão de Java.
* Prefere usar YARN, pois separa-se `spark-submit` por lote.
* Monitorize e afina as definições de configuração do Spark.

Para a sua referência, a estrutura da memória Spark e alguns parâmetros de memória do executor chave são mostrados na imagem seguinte.

## <a name="spark-memory-considerations"></a>Considerações de memória de faísca

Se estiver a utilizar o Apache Hadoop YARN, então o YARN controla a memória utilizada por todos os recipientes em cada nó de faísca.  O diagrama que se segue mostra os objetos-chave e as suas relações.

![Gestão da memória de faíscas de YARN](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Para endereçar mensagens 'fora da memória', tente:

* Reveja o DAG Management Shuffles. Reduzir por redução do lado do mapa, pré-partição (ou baldear) dados de origem, maximizar as baralhadas únicas e reduzir a quantidade de dados enviados.
* Prefere `ReduceByKey` com o seu limite de memória fixa a , que fornece `GroupByKey` agregações, janelas e outras funções, mas tem um limite de memória sem limites.
* Prefere, `TreeReduce` que trabalha mais nos executores ou divisórias, para , que faz todo o trabalho no `Reduce` motorista.
* Utilize DataFrames em vez dos objetos RDD de nível inferior.
* Criar ComplexTypes que englobam ações, como "Top N", várias agregações ou operações de janela.

Para obter etapas adicionais de resolução de [problemas, consulte exceções outOfMemoryError para Apache Spark em Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o processamento de dados para Apache Spark](optimize-cluster-configuration.md)
* [Otimizar o armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)

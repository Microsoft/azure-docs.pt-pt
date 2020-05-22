---
title: Otimizar o armazenamento de dados para Apache Spark - Azure HDInsight
description: Saiba como otimizar o armazenamento de dados para uso com a Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 5728a8e254074cd96ae1f13cb053220f347e3983
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791044"
---
# <a name="data-storage-optimization"></a>Otimização do armazenamento de dados

Este artigo discute estratégias para otimizar o armazenamento de dados para uma execução eficiente do trabalho da Apache Spark no Azure HDInsight.

## <a name="overview"></a>Descrição geral

A faísca suporta muitos formatos, tais como csv, json, xml, parquet, orc e avro. A faísca pode ser estendida para suportar muitos mais formatos com fontes de dados externas - para mais informações, consulte [os pacotes Apache Spark](https://spark-packages.org).

O melhor formato para o desempenho é o parquet com *compressão snappy*, que é o padrão em Spark 2.x. Parquet armazena dados em formato colunaar, e é altamente otimizado em Spark.

## <a name="choose-data-abstraction"></a>Escolha a abstração de dados

As versões anteriores da Spark usam RDDs para dados abstratos, Spark 1.3 e 1.6 introduziram DataFrames e DataSets, respectivamente. Considere os seguintes méritos relativos:

* **DataFrames**
    * A melhor escolha na maioria das situações.
    * Fornece otimização de consulta através do Catalyst.
    * Geração de códigos em palco inteiro.
    * Acesso direto à memória.
    * Baixa recolha de lixo (GC) por cima.
    * Não tão amigável para o desenvolvedor como DataSets, uma vez que não existem verificações de tempo de compilação ou programação de objetos de domínio.
* **Conjuntos de Dados**
    * Bom em oleodutos ETL complexos onde o impacto de desempenho é aceitável.
    * Não é bom em agregações onde o impacto do desempenho pode ser considerável.
    * Fornece otimização de consulta através do Catalyst.
    * Amigável para o desenvolvedor, fornecendo programação de objetos de domínio e compilação de verificações de tempo.
    * Adiciona a sobrecarga de serialização/desserialização.
    * Altas despesas de GC.
    * Quebra a geração de códigos em palco inteiro.
* **RdDs**
    * Não precisa de usar RDDs, a não ser que precise de construir um novo RDD personalizado.
    * Sem otimização de consultas através do Catalyst.
    * Nenhuma geração de códigos em estágio inteiro.
    * Altas despesas de GC.
    * Deve usar AFIs legado Spark 1.x.

## <a name="select-default-storage"></a>Selecione armazenamento por defeito

Quando criar um novo cluster Spark, pode selecionar o Armazenamento de Blob Azure ou o Armazenamento do Lago De dados Azure como armazenamento padrão do seu cluster. Ambas as opções dão-lhe o benefício do armazenamento a longo prazo para clusters transitórios. Assim, os seus dados não são automaticamente apagados quando elimina o seu cluster. Pode recriar um cluster transitório e ainda aceder aos seus dados.

| Store Type | Sistema de Ficheiros | Velocidade | Transitória | Casos de Utilização |
| --- | --- | --- | --- | --- |
| Armazenamento de Blobs do Azure | **wasb:**//url/ | **Standard** | Sim | Aglomerado transitório |
| Armazenamento De Blob Azure (seguro) | **wasbs:**//url/ | **Standard** | Sim | Aglomerado transitório |
| Azure Data Lake Storage Gen2| **abfs:**//url/ | **Mais rápido** | Sim | Aglomerado transitório |
| Armazenamento de lagos azure data gen 1| **adl:**//url/ | **Mais rápido** | Sim | Aglomerado transitório |
| HDFS locais | **Hdfs:**//url/ | **Mais rápido** | Não | Cluster interativo 24/7 |

Para obter uma descrição completa das opções de armazenamento, consulte Compare opções de [armazenamento para utilização com clusters Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Use a cache

A faísca fornece os seus próprios mecanismos nativos de cache, que podem ser usados através de diferentes métodos, tais `.persist()` `.cache()` como, e `CACHE TABLE` . Este cache nativo é eficaz com pequenos conjuntos de dados e em oleodutos ETL onde você precisa cache resultados intermédios. No entanto, o caching nativo spark atualmente não funciona bem com a divisão, uma vez que uma mesa em cache não mantém os dados de divisão. Uma técnica de cache mais genérica e fiável é o cache da camada de *armazenamento.*

* Caching de faísca nativa (não recomendado)
    * Bom para pequenos conjuntos de dados.
    * Não funciona com a partilha, o que pode mudar nos futuros lançamentos da Spark.

* Cacheching nível de armazenamento (recomendado)
    * Pode ser implementado no HDInsight utilizando a função [IO Cache.](apache-spark-improve-performance-iocache.md)
    * Usa na memória e caching SSD.

* HDFS local (recomendado)
    * `hdfs://mycluster`caminho.
    * Usa o cache de SSD.
    * Os dados em cache perder-se-ão quando eliminar o cluster, exigindo uma reconstrução em cache.

## <a name="optimize-data-serialization"></a>Otimizar a serialização de dados

Os trabalhos de faísca são distribuídos, por isso a serialização adequada dos dados é importante para o melhor desempenho.  Existem duas opções de serialização para a Spark:

* A serialização de Java é o padrão.
* `Kryo`serialização é um formato mais recente e pode resultar em serialização mais rápida e compacta do que Java.  `Kryo`requer que registe as aulas no seu programa, e ainda não suporta todos os tipos serlizáveis.

## <a name="use-bucketing"></a>Use baldes

Baldeé semelhante à divisão de dados. Mas cada balde pode conter um conjunto de valores de coluna em vez de apenas um. Este método funciona bem para a divisão em grandes números (nos milhões ou mais) de valores, tais como identificadores de produto. Um balde é determinado por hashing a chave do balde da fila. As mesas em balde oferecem otimizações únicas porque armazenam metadados sobre como foram baldeados e classificados.

Algumas características avançadas de balde são:

* Otimização de consulta com base na meta-informação de balde.
* Agregações otimizadas.
* Juntas otimizadas.

Pode usar divisórias e baldes ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o processamento de dados para a Apache Spark](optimize-cluster-configuration.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)
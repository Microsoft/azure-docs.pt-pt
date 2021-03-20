---
title: Otimizar o armazenamento de dados para Apache Spark - Azure HDInsight
description: Aprenda a otimizar o armazenamento de dados para uso com Apache Spark em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98926979"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Otimização de armazenamento de dados para Apache Spark

Este artigo discute estratégias para otimizar o armazenamento de dados para uma eficiente execução de emprego apache spark em Azure HDInsight.

## <a name="overview"></a>Descrição geral

A faísca suporta muitos formatos, tais como csv, json, xml, parquet, orc e avro. A faísca pode ser estendida para suportar muitos mais formatos com fontes de dados externas - para mais informações, consulte [os pacotes Apache Spark](https://spark-packages.org).

O melhor formato para desempenho é o parquet com *compressão snappy,* que é o padrão em Spark 2.x. O Parquet armazena dados em formato colunar, e está altamente otimizado em Spark.

## <a name="choose-data-abstraction"></a>Escolha a abstração de dados

As versões Spark anteriores usam RDDs para dados abstratos, Spark 1.3 e 1.6 introduzidos DataFrames e DataSets, respectivamente. Considere os seguintes méritos relativos:

* **DataFrames**
    * A melhor escolha na maioria das situações.
    * Fornece otimização de consulta através do Catalyst.
    * Geração de códigos em todo o estágio.
    * Acesso direto à memória.
    * Baixa recolha de lixo (GC) por cima.
    * Não tão amigável como os DataSets, uma vez que não existem verificações de tempo de compilação ou programação de objetos de domínio.
* **Conjuntos de Dados**
    * Bom em oleodutos ETL complexos onde o impacto de desempenho é aceitável.
    * Não é bom em agregações onde o impacto no desempenho pode ser considerável.
    * Fornece otimização de consulta através do Catalyst.
    * Amiga do programador, fornecendo programação de objetos de domínio e verificações de tempo de compilação.
    * Adiciona serialização/deserialização por cima.
    * Alta GC sobrecarga.
    * Quebra a geração de códigos de todo o estágio.
* **RDDs**
    * Não precisa de usar RDDs, a não ser que precise de construir um novo RDD personalizado.
    * Sem otimização de consulta através do Catalyst.
    * Nenhuma geração de código de todo o estágio.
    * Alta GC sobrecarga.
    * Deve utilizar as APIs do legado Spark 1.x.

## <a name="select-default-storage"></a>Selecione o armazenamento predefinido

Quando criar um novo cluster Spark, pode selecionar o Azure Blob Storage ou o Azure Data Lake Storage como o armazenamento padrão do seu cluster. Ambas as opções dão-lhe o benefício do armazenamento a longo prazo para clusters transitórios. Para que os seus dados não sejam automaticamente eliminados quando elimina o seu cluster. Pode recriar um cluster transitório e ainda aceder aos seus dados.

| Store Type | Sistema de Ficheiros | Velocidade | Transitório | Casos de Utilização |
| --- | --- | --- | --- | --- |
| Armazenamento de Blobs do Azure | **wasb:**//url/ | **Standard** | Yes | Aglomerado transitório |
| Armazenamento Azure Blob (seguro) | **wasbs:**//url/ | **Standard** | Yes | Aglomerado transitório |
| Azure Data Lake Storage Gen2| **abfs:**//url/ | **Mais rápido** | Yes | Aglomerado transitório |
| Azure Data Lake Storage Gen 1| **adl:**//url/ | **Mais rápido** | Yes | Aglomerado transitório |
| HDFs locais | **hdfs:**//url/ | **Mais rápido** | No | Cluster interativo 24/7 |

Para obter uma descrição completa das opções de armazenamento, consulte opções de [armazenamento compare para utilização com clusters Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Utilizar a cache

A faísca fornece os seus próprios mecanismos nativos de caching, que podem ser usados através de diferentes métodos, tais `.persist()` `.cache()` como, e `CACHE TABLE` . Este cache nativo é eficaz com pequenos conjuntos de dados e em oleodutos ETL onde você precisa cache resultados intermédios. No entanto, o caching nativo de Spark atualmente não funciona bem com a partição, uma vez que uma mesa em cache não mantém os dados de partição. Uma técnica de caching mais genérica e fiável é *o caching da camada de armazenamento*.

* Caching de faíscas nativas (não recomendado)
    * Bom para pequenos conjuntos de dados.
    * Não funciona com partição, o que pode mudar em futuros lançamentos de Spark.

* Caching nível de armazenamento (recomendado)
    * Pode ser implementado em HDInsight utilizando a função [Cache IO.](apache-spark-improve-performance-iocache.md)
    * Usa a memória e o caching SSD.

* HDFS local (recomendado)
    * `hdfs://mycluster` caminho.
    * Usa caching SSD.
    * Os dados em cache serão perdidos quando eliminar o cluster, exigindo uma reconstrução de cache.

## <a name="optimize-data-serialization"></a>Otimizar a serialização de dados

Os trabalhos de faísca são distribuídos, por isso a serialização adequada dos dados é importante para o melhor desempenho.  Existem duas opções de serialização para a Spark:

* A serialização de Java é o padrão.
* `Kryo` a serialização é um formato mais recente e pode resultar numa serialização mais rápida e compacta do que java.  `Kryo` requer que registe as aulas no seu programa, e ainda não suporta todos os tipos serializáveis.

## <a name="use-bucketing"></a>Utilizar os registos

O balde é semelhante à partição de dados. Mas cada balde pode segurar um conjunto de valores de coluna em vez de apenas um. Este método funciona bem para a divisão em grandes (milhões ou mais) números de valores, tais como identificadores de produtos. Um balde é determinado por hashing a chave do balde da linha. As mesas baldeadas oferecem otimizações únicas porque armazenam metadados sobre como foram baldes e classificados.

Algumas características avançadas de balde são:

* Otimização de consultas com base em meta-informação de balde.
* Agregações otimizadas.
* Juntas otimizadas.

Pode usar divisórias e baldes ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o processamento de dados para Apache Spark](optimize-cluster-configuration.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)

---
title: O que é a pilha de tecnologia de Apache Hadoop? -Azure HDInsight
description: Uma introdução ao HDInsight e os componentes e a pilha de tecnologia Apache Hadoop.
keywords: hadoop azure, azure hadoop, introdução ao hadoop, intro ao hadoop, pilha de tecnologia do hadoop, intro do hadoop, introdução do hadoop, o que é um cluster do hadoop, o que é o cluster do hadoop, para que é utilizado o hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 08/15/2019
ms.openlocfilehash: d6127d4fba3d2255dee28cd41179f16394545328
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207223"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>O que é Apache Hadoop no Azure HDInsight?

O [Apache Hadoop](https://hadoop.apache.org/) era a arquitetura de código aberto original para processamento distribuído e análise de conjuntos de macrodados em clusters. O ecossistema do Hadoop inclui software e utilitários relacionados, incluindo Apache Hive, Apache HBase, Spark, Kafka e muitos outros.

O Azure HDInsight é um serviço de análise de código aberto totalmente gerenciado e completo na nuvem para empresas. O tipo de cluster Apache Hadoop no Azure HDInsight permite que você use o HDFS, o gerenciamento de recursos YARN e um modelo de programação MapReduce simples para processar e analisar dados em lotes em paralelo.

Para ver os componentes disponíveis da pilha de tecnologia do Hadoop no HDInsight, consulte [componentes e versões disponíveis com o hdinsight](../hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, veja a [página de funcionalidades do Azure para o HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a id="whatis"></a>O que é o MapReduce

Apache Hadoop MapReduce é uma estrutura de software para escrever trabalhos que processam grandes quantidades de dados. Os dados de entrada são divididos em partes independentes. Cada parte é processada em paralelo entre os nós no cluster. Um trabalho MapReduce consiste em duas funções:

* **Mapeador**: Consome dados de entrada, analisa-os (geralmente com operações de filtro e classificação) e emite tuplas (pares chave-valor)

* **Redutor**: Consome tuplas emitidas pelo mapeador e executa uma operação de resumo que cria um resultado menor e combinado dos dados do mapeador

Um exemplo de trabalho de MapReduce de contagem de palavras básica é ilustrado no diagrama a seguir:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

A saída desse trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapeador leva cada linha do texto de entrada como uma entrada e a divide em palavras. Ele emite um par de chave/valor cada vez que uma palavra ocorre da palavra é seguida por um 1. A saída é classificada antes de enviá-la para redutor.
* O redutor soma essas contagens individuais para cada palavra e emite um único par de chave/valor que contém a palavra seguida pela soma de suas ocorrências.

O MapReduce pode ser implementado em vários idiomas. O Java é a implementação mais comum e é usado para fins de demonstração neste documento.

## <a name="development-languages"></a>Linguagens de desenvolvimento

Linguagens ou estruturas baseadas em Java e a Máquina Virtual Java podem ser executadas diretamente como um trabalho MapReduce. O exemplo usado neste documento é um aplicativo MapReduce Java. Linguagens não Java, como C#, Python ou executáveis autônomos, devem usar o **streaming do Hadoop**.

O streaming do Hadoop se comunica com o mapeador e redutor sobre STDIN e STDOUT. O mapeador e o redutor lêem dados uma linha por vez do STDIN e gravam a saída em STDOUT. Cada linha lida ou emitida pelo mapeador e redutor deve estar no formato de um par chave/valor, delimitado por um caractere de tabulação:

    [key]/t[value]

Para obter mais informações, consulte [Hadoop streaming](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obter exemplos de como usar o streaming do Hadoop com o HDInsight, consulte o seguinte documento:

* [Desenvolver C# trabalhos MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Passos Seguintes

* [Criar Apache Hadoop cluster no HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

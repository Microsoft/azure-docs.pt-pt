---
title: O que é a pilha de tecnologia Apache Hadoop? - Azure HDInsight
description: Uma introdução ao HDInsight, e à pilha e componentes da tecnologia Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: e98a11d9aee1c4f76453dc1716f92514b021444f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917837"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>O que é Apache Hadoop em Azure HDInsight?

O [Apache Hadoop](https://hadoop.apache.org/) era a arquitetura de código aberto original para processamento distribuído e análise de conjuntos de macrodados em clusters. O ecossistema Hadoop inclui software e utilitários relacionados, incluindo Apache Hive, Apache HBase, Spark, Kafka, entre muitos outros.

O Azure HDInsight é um serviço de análise de código aberto totalmente gerido, de todo o espectro, na nuvem para as empresas. O tipo de cluster Apache Hadoop em Azure HDInsight permite-lhe utilizar hDFS, gestão de recursos YARN e um modelo de programação simples MapReduce para processar e analisar dados de lote em paralelo.

Para ver os componentes disponíveis da pilha de tecnologia Hadoop no HDInsight, consulte [Componentes e versões disponíveis com o HDInsight](../hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, veja a [página de funcionalidades do Azure para o HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>O que é MapReduce

Apache Hadoop MapReduce é um quadro de software para escrever trabalhos que processam grandes quantidades de dados. Os dados de entrada são divididos em pedaços independentes. Cada pedaço é processado paralelamente através dos nós do seu aglomerado. Um trabalho mapReduce consiste em duas funções:

* **Mapper**: Consome dados de entrada, analisa-os (geralmente com operações de filtragem e triagem) e emite tuples (pares de valor-chave)

* **Redutor**: Consome tuples emitidos pelo Mapper e realiza uma operação sumária que cria um resultado menor e combinado a partir dos dados mapper

Uma contagem de palavras básica MapReduce exemplo de trabalho é ilustrado no seguinte diagrama:

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapper toma cada linha do texto de entrada como entrada e quebra-a em palavras. Emite um par chave/valor cada vez que uma palavra ocorre da palavra é seguida por um 1. A saída é ordenada antes de enviá-la para redutor.
* O redutor soma estes individuais conta para cada palavra e emite um único par chave/valor que contém a palavra seguida pela soma das suas ocorrências.

MapReduce pode ser implementado em várias línguas. Java é a implementação mais comum, e é usada para fins de demonstração neste documento.

## <a name="development-languages"></a>Línguas de desenvolvimento

As línguas ou quadros que são baseados em Java e na Máquina Virtual Java podem ser dirigidos diretamente como um trabalho MapReduce. O exemplo usado neste documento é uma aplicação Java MapReduce. As línguas não java, tais como, C#Python, ou executáveis autónomos, devem utilizar o streaming **Hadoop**.

O streaming de hadoop comunica com o mapper e redutor sobre STDIN e STDOUT. O mapper e o redutor lêm dados de uma linha de cada vez a partir de STDIN, e escrevem a saída para o STDOUT. Cada linha lida ou emitida pelo mapeador e redutor deve estar no formato de um par chave/valor, delimitado por um caracteres de separador:

    [key]/t[value]

Para mais informações, consulte [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Por exemplo, utilizando o streaming Hadoop com o HDInsight, consulte o seguinte documento:

* [Desenvolver C# mapReduzir empregos](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar o cluster Apache Hadoop no HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)

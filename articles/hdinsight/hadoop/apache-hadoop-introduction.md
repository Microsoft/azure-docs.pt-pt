---
title: O que são Apache Hadoop e MapReduce - Azure HDInsight
description: Uma introdução ao HDInsight, e a pilha de tecnologia Apache Hadoop e componentes.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 5e5f02b1684e56496778ab677aa9dc46e7dcd9aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87086528"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>O que é Apache Hadoop em Azure HDInsight?

O [Apache Hadoop](https://hadoop.apache.org/) era a arquitetura de código aberto original para processamento distribuído e análise de conjuntos de macrodados em clusters. O ecossistema Hadoop inclui software e utilitários relacionados, incluindo Apache Hive, Apache HBase, Spark, Kafka, e muitos outros.

Azure HDInsight é um serviço de análise totalmente gerido, de espectro completo e de código aberto na nuvem para empresas. O tipo de cluster Apache Hadoop em Azure HDInsight permite-lhe utilizar HDFS, gestão de recursos YARN e um modelo de programação mapReduce simples para processar e analisar dados de lote em paralelo.

Para ver os componentes da pilha tecnológica do Hadoop disponíveis no HDInsight, veja [Componentes e versões disponíveis com o HDInsight](../hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, veja a [página de funcionalidades do Azure para o HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>O que é MapReduce

Apache Hadoop MapReduce é um quadro de software para escrever trabalhos que processam grandes quantidades de dados. Os dados de entrada são divididos em pedaços independentes. Cada pedaço é processado em paralelo através dos nós do seu aglomerado. Um trabalho mapreduce consiste em duas funções:

* **Mapper**: Consome dados de entrada, analisa-os (geralmente com operações de filtragem e triagem) e emite tuples (pares de valores-chave)

* **Redutor**: Consome tuples emitidos pelo Mapper e executa uma operação sumária que cria um resultado mais pequeno e combinado a partir dos dados do Mapper

Um exemplo de trabalho de contagem de palavras básica MapReduce é ilustrado no seguinte diagrama:

 ![O HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapper toma cada linha a partir do texto de entrada como uma entrada e quebra-a em palavras. Emite um par chave/valor cada vez que uma palavra ocorre da palavra é seguida por um 1. A saída é ordenada antes de enviá-la para redutor.
* O redutor soma que estes indivíduos contam para cada palavra e emite um único par chave/valor que contém a palavra seguida pela soma das suas ocorrências.

MapReduce pode ser implementado em várias línguas. Java é a implementação mais comum, e é usada para fins de demonstração neste documento.

## <a name="development-languages"></a>Línguas de desenvolvimento

Línguas ou quadros baseados em Java e na Máquina Virtual java podem ser dirigidos diretamente como um trabalho MapReduce. O exemplo usado neste documento é uma aplicação Java MapReduce. As línguas não-Java, tais como C#, Python ou executáveis autónomos, devem utilizar **o streaming hadoop**.

O streaming hadoop comunica com o mapper e redutor sobre STDIN e STDOUT. O mapper e o redutor lê dados uma linha de cada vez a partir de STDIN, e escrever a saída para STDOUT. Cada linha lida ou emitida pelo mapper e pelo redutor deve estar no formato de um par chave/valor, delimitado por um caractere de separador:

`[key]\t[value]`

Para mais informações, consulte [o Hadoop Streaming.](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html)

Para exemplos de utilização do streaming hadoop com HDInsight, consulte o seguinte documento:

* [Desenvolver trabalhos de M90](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar aglomerado Apache Hadoop em HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)

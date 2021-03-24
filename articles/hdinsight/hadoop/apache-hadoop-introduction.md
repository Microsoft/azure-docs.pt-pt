---
title: O que é Apache Hadoop e MapReduce - Azure HDInsight
description: Uma introdução ao HDInsight, e a pilha de tecnologia Apache Hadoop e componentes.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: ad1f7422919f224889db84a2599ad3c1c48efcc5
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863815"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>O que é Apache Hadoop em Azure HDInsight?

O [Apache Hadoop](https://hadoop.apache.org/) era a arquitetura de código aberto original para processamento distribuído e análise de conjuntos de macrodados em clusters. O ecossistema Hadoop inclui software e utilitários relacionados, incluindo Apache Hive, Apache HBase, Spark, Kafka, e muitos outros.

Azure HDInsight é um serviço de análise totalmente gerido, de espectro completo e de código aberto na nuvem para empresas. O tipo de cluster Apache Hadoop em Azure HDInsight permite-lhe utilizar o [Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS),](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html)a gestão de recursos [YarN apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) e um modelo de programação [mapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) simples para processar e analisar dados de lote em paralelo.  Os clusters hadoop em HDInsight são compatíveis com [o armazenamento Azure Blob](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1,](../../data-lake-store/data-lake-store-overview.md)ou [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

Para ver os componentes da pilha tecnológica do Hadoop disponíveis no HDInsight, veja [Componentes e versões disponíveis com o HDInsight](../hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, veja a [página de funcionalidades do Azure para o HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>O que é MapReduce

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) é um quadro de software para escrever trabalhos que processam grandes quantidades de dados. Os dados de entrada são divididos em pedaços independentes. Cada pedaço é processado em paralelo através dos nós do seu aglomerado. Um trabalho mapreduce consiste em duas funções:

* **Mapper**: Consome dados de entrada, analisa-os (geralmente com operações de filtragem e triagem) e emite tuples (pares de valores-chave)

* **Redutor**: Consome tuples emitidos pelo Mapper e executa uma operação sumária que cria um resultado mais pequeno e combinado a partir dos dados do Mapper

Um exemplo de trabalho de contagem de palavras básica MapReduce é ilustrado no seguinte diagrama:

 :::image type="content" source="./media/apache-hadoop-introduction/hdi-word-count-diagram.gif" alt-text="O HDI. WordCountDiagram" border="true":::

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapper toma cada linha a partir do texto de entrada como uma entrada e quebra-a em palavras. Emite um par chave/valor cada vez que uma palavra ocorre da palavra é seguida por um 1. A saída é ordenada antes de enviá-la para redutor.
* O redutor soma que estes indivíduos contam para cada palavra e emite um único par chave/valor que contém a palavra seguida pela soma das suas ocorrências.

MapReduce pode ser implementado em várias línguas. Java é a implementação mais comum, e é usada para fins de demonstração neste documento.

## <a name="development-languages"></a>Línguas de desenvolvimento

Línguas ou quadros baseados em Java e na Máquina Virtual Java podem ser dirigidos diretamente como um [trabalho MapReduce](..//hadoop/submit-apache-hadoop-jobs-programmatically.md). O exemplo usado neste documento é uma aplicação Java MapReduce. As línguas não-Java, tais como C#, Python ou executáveis autónomos, devem utilizar **o streaming hadoop**.

O streaming hadoop comunica com o mapper e redutor sobre STDIN e STDOUT. O mapper e o redutor lê dados uma linha de cada vez a partir de STDIN, e escrever a saída para STDOUT. Cada linha lida ou emitida pelo mapper e pelo redutor deve estar no formato de um par chave/valor, delimitado por um caractere de separador:

`[key]\t[value]`

Para mais informações, consulte [o Hadoop Streaming.](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html)

Para exemplos de utilização do streaming hadoop com HDInsight, consulte o seguinte documento:

* [Desenvolver trabalhos de M90](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Por onde começo

* [Quickstart: Criar cluster Apache Hadoop em Azure HDInsight usando o portal Azure](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Tutorial: Submeter empregos da Apache Hadoop em HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Desenvolver programas Java MapReduce para Apache Hadoop em HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Use a Colmeia Apache como uma ferramenta de extrato, transformação e carga (ETL)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Extrair, transformar e carregar (ETL) em escala](../hadoop/apache-hadoop-etl-at-scale.md)
* [Operacionalizar um pipeline de análise de dados](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar cluster Apache Hadoop em HDInsight usando o portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Crie o cluster Apache Hadoop em HDInsight usando o modelo ARM](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
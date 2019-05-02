---
title: MapReduce com o Apache Hadoop no HDInsight
description: Saiba como executar tarefas de MapReduce no Apache Hadoop em clusters do HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9da6b6ba3ab697887e55f9077b44cf6fa100a981
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707961"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Utilizar o MapReduce no Apache Hadoop no HDInsight

Saiba como executar tarefas de MapReduce em clusters do HDInsight. 

## <a id="whatis"></a>O que é o MapReduce

Apache Hadoop MapReduce é uma estrutura de software para a escrita de tarefas que processam grandes quantidades de dados. Dados de entrada são divididos em partes independentes. Cada segmento é processado em paralelo em todos os nós do cluster. Uma tarefa de MapReduce consiste em duas funções:

* **Mapper**: Consome dados de entrada, analisa-(normalmente com o filtro e classificação de operações) e emite tuplas (pares chave-valor)

* **Reducer**: Consome tuplas emitidas pelo mapeador de pontos e realiza uma operação de resumida que cria um resultado mais pequeno, combinado a partir dos dados de mapeador de pontos

Um exemplo de tarefa de MapReduce de contagem de palavras básica é ilustrado no diagrama seguinte:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

O resultado deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapeador de pontos necessária cada linha do texto de entrada como entrada e divide em palavras. Emite um chave/valor par sempre que uma palavra ocorre da palavra é seguido de um 1. A saída é ordenada antes do envio para reducer.
* O reducer resume essas contagens individuais para cada palavra e emite um par de chaves/valores individual que contenha a palavra seguida pela soma de suas ocorrências.

MapReduce pode ser implementado em várias linguagens. Java é a implementação mais comuns e é utilizado para fins de demonstração neste documento.

## <a name="development-languages"></a>Linguagens de programação

Linguagens ou estruturas baseadas em Java e a Máquina Virtual de Java pode ser executado diretamente como uma tarefa de MapReduce. No exemplo utilizado neste documento é uma aplicação de Java MapReduce. Idiomas não-Java, como c#, Python ou executáveis autônomos, tem de utilizar **transmissão em fluxo do Hadoop**.

Transmissão em fluxo do Hadoop comunica com o mapeador de pontos e reducer através de STDIN e STDOUT. O mapeador de reducer ler uma linha de dados cada vez do STDIN e escrever a saída para STDOUT. Cada linha, ler ou emitidos pela mapeador de pontos e reducer tem de estar no formato de um par chave/valor, delimitado pelo caráter de tabulação:

    [key]/t[value]

Para obter mais informações, consulte [transmissão em fluxo do Hadoop](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obter exemplos de utilização do Hadoop de transmissão em fluxo com o HDInsight, consulte o documento seguinte:

* [Desenvolver trabalhos de MapReduce do c#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a id="data"></a>Dados de exemplo

HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados na `/example/data` e `/HdiSamples` diretório. Esses diretórios são no armazenamento padrão para o seu cluster. Neste documento, usamos o `/example/data/gutenberg/davinci.txt` ficheiro. Este ficheiro contém os blocos de anotações de Leonardo da Vinci.

## <a id="job"></a>MapReduce de exemplo

Um exemplo de aplicativo de contagem do MapReduce word está incluído com o seu cluster do HDInsight. Neste exemplo está localizado em `/example/jars/hadoop-mapreduce-examples.jar` no armazenamento padrão para o seu cluster.

O seguinte código de Java é a fonte do aplicativo MapReduce contida no `hadoop-mapreduce-examples.jar` ficheiro:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Para obter instruções para escrever seus próprios aplicativos de MapReduce, consulte o documento seguinte:

* [Desenvolver aplicações de Java MapReduce para o HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Executar o MapReduce

HDInsight pode executar tarefas de HiveQL utilizando vários métodos. Utilize a tabela seguinte para decidir qual é o método adequado para si, em seguida, siga a ligação para obter instruções.

| **Utilize esta opção**... | **... recurso aleatório fazê-lo** | ... .with isso **sistema operativo do cluster** | ... .from isso **sistema operativo do cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Utilize o comando de Hadoop por meio de **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Submeter a tarefa remotamente utilizando **REST** |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Submeter a tarefa remotamente utilizando **Windows PowerShell** |Linux ou Windows |Windows |

## <a id="nextsteps"></a>Passos seguintes

Para saber mais sobre como trabalhar com dados no HDInsight, veja os documentos seguintes:

* [Desenvolver programas Java MapReduce para o HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Utilizar o Apache Hive com o HDInsight][hdinsight-use-hive]

* [Utilizar o Apache Pig com o HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

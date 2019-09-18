---
title: MapReduce com Apache Hadoop no HDInsight
description: Saiba como executar trabalhos do Apache MapReduce em Apache Hadoop em clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: c4f975b56d3658731b6dc165e01b54ac09f3b89c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076222"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Usar o MapReduce no Apache Hadoop no HDInsight

Saiba como executar trabalhos MapReduce em clusters HDInsight.

## <a id="data"></a>Dados de exemplo

O `/example/data` HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados `/HdiSamples` no diretório e. Esses diretórios estão no armazenamento padrão para seu cluster. Neste documento, usamos o `/example/data/gutenberg/davinci.txt` arquivo. Esse arquivo contém os blocos de anotações de Leonardo da Vinci.

## <a id="job"></a>MapReduce de exemplo

Um exemplo de aplicativo de contagem de palavras MapReduce está incluído no seu cluster HDInsight. Este exemplo está localizado `/example/jars/hadoop-mapreduce-examples.jar` em no armazenamento padrão para seu cluster.

O código Java a seguir é a origem do aplicativo MapReduce contido no `hadoop-mapreduce-examples.jar` arquivo:

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

Para obter instruções sobre como escrever seus próprios aplicativos MapReduce, consulte o seguinte documento:

* [Desenvolver aplicativos Java MapReduce para o HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Executar o MapReduce

O HDInsight pode executar trabalhos do HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é ideal para você e, em seguida, siga o link para obter uma explicação.

| **Use isto**... | **... para fazer isso** | ... com este **sistema operacional de cluster** | ... deste **sistema operacional cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Usar o comando do Hadoop por meio de **SSH** |Linux |Linux, UNIX, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Enviar o trabalho remotamente usando o **REST** |Linux ou Windows |Linux, UNIX, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Enviar o trabalho remotamente usando o **Windows PowerShell** |Linux ou Windows |Windows |

## <a id="nextsteps"></a>Passos seguintes

Para saber mais sobre como trabalhar com dados no HDInsight, consulte os seguintes documentos:

* [Desenvolver programas Java MapReduce para o HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Usar o Apache Hive com o HDInsight][hdinsight-use-hive]

* [Usar o Apache Pig com o HDInsight][hdinsight-use-pig]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

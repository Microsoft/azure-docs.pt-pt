---
title: MapReduce com Apache Hadoop no HDInsight
description: Saiba como executar trabalhos do Apache MapReduce em Apache Hadoop em clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 238f37cc1e8cbc8d3260693181d6e54ac5f592f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435710"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Usar o MapReduce no Apache Hadoop no HDInsight

Saiba como executar trabalhos MapReduce em clusters HDInsight.

## <a name="example-data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados no diretório `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão para seu cluster. Neste documento, usamos o arquivo `/example/data/gutenberg/davinci.txt`. Esse arquivo contém os blocos de anotações de Leonardo da Vinci.

## <a name="example-mapreduce"></a>MapReduce de exemplo

Um exemplo de aplicativo de contagem de palavras MapReduce está incluído no seu cluster HDInsight. Este exemplo está localizado em `/example/jars/hadoop-mapreduce-examples.jar` no armazenamento padrão do cluster.

O código Java a seguir é a origem do aplicativo MapReduce contido no arquivo de `hadoop-mapreduce-examples.jar`:

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

Para obter instruções para escrever seus próprios aplicativos MapReduce, consulte [desenvolver aplicativos Java MapReduce para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Executar o MapReduce

O HDInsight pode executar trabalhos do HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é ideal para você e, em seguida, siga o link para obter uma explicação.

| **Use isto**... | **... para fazer isso** |  ... deste **sistema operacional cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Usar o comando do Hadoop por meio de **SSH** |Linux, UNIX, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Enviar o trabalho remotamente usando o **REST** |Linux, UNIX, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Enviar o trabalho remotamente usando o **Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como trabalhar com dados no HDInsight, consulte os seguintes documentos:

* [Desenvolver programas Java MapReduce para o HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Usar o Apache Hive com o HDInsight](./hdinsight-use-hive.md)

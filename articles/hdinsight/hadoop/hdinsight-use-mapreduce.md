---
title: MapReduce com Apache Hadoop em HDInsight
description: Aprenda a gerir os trabalhos apache mapreduce em Apache Hadoop em clusters HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944260"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Utilizar o MapReduce no Apache Hadoop no HDInsight

Aprenda a executar trabalhos mapReduce em clusters HDInsight.

## <a name="example-data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados no diretório e no `/example/data` `/HdiSamples` diretório. Estes diretórios estão no armazenamento padrão para o seu cluster. Neste documento, usamos o `/example/data/gutenberg/davinci.txt` ficheiro. Este ficheiro contém os cadernos de Leonardo da Vinci.

## <a name="example-mapreduce"></a>Exemplo MapReduce

Um exemplo da aplicação de contagem de palavras MapReduce está incluído com o seu cluster HDInsight. Este exemplo `/example/jars/hadoop-mapreduce-examples.jar` encontra-se no armazenamento predefinido para o seu cluster.

O seguinte código Java é a fonte da aplicação MapReduce contida no `hadoop-mapreduce-examples.jar` ficheiro:

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

Para obter instruções para escrever as suas próprias aplicações MapReduce, consulte [develop Java MapReduce applications for HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Executar o MapReduce

HDInsight pode executar trabalhos de HiveQL usando vários métodos. Use a seguinte tabela para decidir qual o método certo para si e, em seguida, siga o link para uma passagem.

| **Use isto...** | **... para fazer isto** |  ... deste **sistema operativo cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Use o comando Hadoop através **de SSH** |Linux, Unix, Mac OS X ou Windows |
| [Caracóis](apache-hadoop-use-mapreduce-curl.md) |Submeta o trabalho remotamente utilizando **REST** |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Submeta o trabalho remotamente utilizando **o Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como trabalhar com os dados em HDInsight, consulte os seguintes documentos:

* [Desenvolver programas Java MapReduce para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Use a Colmeia Apache com HDInsight](./hdinsight-use-hive.md)

---
title: Executar os exemplos de Hadoop no HDInsight - Azure
description: Começar a utilizar o serviço Azure HDInsight com os exemplos fornecidos. Utilize scripts do PowerShell que executar programas MapReduce em clusters de dados.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 3d27a9cc8dd4b460a75e2a43106413ed9ee1d559
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361528"
---
# <a name="run-mapreduce-samples-in-windows-based-hdinsight"></a>Executar exemplos de MapReduce no HDInsight baseado em Windows
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Um conjunto de exemplos é fornecido para ajudar a introdução tarefas de MapReduce em execução em clusters do Apache Hadoop através do Azure HDInsight. Estes exemplos são disponibilizados em cada um dos clusters HDInsight gerido que criar. Executar estes exemplos familiarizá-lo com a utilização de cmdlets do Azure PowerShell para executar tarefas em clusters do Hadoop.

* [**Contagem de palavras**][hdinsight-sample-wordcount]: Contagem de ocorrências de palavras num arquivo de texto.
* [**C#Contagem de palavras de transmissão em fluxo**][hdinsight-sample-csharp-streaming]: Contagem de ocorrências de palavras num arquivo de texto usando a interface de transmissão em fluxo do Hadoop.
* [**Estimador de PI**][hdinsight-sample-pi-estimator]: Utiliza uma estatística (quasi-Monte Carlo) método para calcular o valor de pi.
* [**Graysort de 10 GB**][hdinsight-sample-10gb-graysort]: Execute um GraySort para fins gerais num arquivo de 10 GB com HDInsight. Existem três tarefas para serem executadas: Teragen para gerar os dados, Terasort para classificar os dados e Teravalidate para confirmar que o tipo de dados foi classificado corretamente.

> [!NOTE]  
> O código-fonte pode ser encontrado no apêndice.

Documentação adicional muito existe na web para as tecnologias relacionadas com o Hadoop, tais como a programação de MapReduce com base em Java e o de transmissão em fluxo e a documentação sobre os cmdlets que são utilizados no Windows PowerShell scripts. Para obter mais informações sobre estes recursos, consulte:

* [Desenvolver programas Java MapReduce para o Apache Hadoop no HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Submeter tarefas do Apache Hadoop no HDInsight](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight][hdinsight-introduction]

Hoje em dia, muitas pessoas preferem Apache Hive e Apache Pig ao longo do MapReduce.  Para obter mais informações, consulte:

* [Utilizar o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig no HDInsight](hadoop/hdinsight-use-pig.md)

**Pré-requisitos**:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um cluster do HDInsight**. Para obter instruções sobre as várias formas em que estes clusters podem ser criadas, consulte [Apache Hadoop criar clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* **Uma estação de trabalho com o Azure PowerShell**.

    > [!IMPORTANT]  
    > O suporte do Azure PowerShell para gerir recursos do HDInsight com o Azure Service Manager foi **preterido** e será removido até 1 de janeiro de 2017. Os passos neste documento utilizam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga os passos em [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se tiver scripts que têm de ser modificado para usar os novos cmdlets que funcionam com o Azure Resource Manager, veja [migrar para as ferramentas de desenvolvimento baseado no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="hdinsight-sample-wordcount"></a>Contagem - Java de palavras
Para submeter um projeto do MapReduce, primeiro crie uma definição de tarefa de MapReduce. Na definição de tarefa, especifica o ficheiro. jar do programa MapReduce e a localização do ficheiro jar, que é **wasb:///example/jars/hadoop-mapreduce-examples.jar**, o nome da classe e os argumentos.  O programa de MapReduce do wordcount recebe dois argumentos: o ficheiro de origem que é utilizado para a contagem de palavras e a localização de saída.

O código-fonte pode ser encontrado no apêndice A.

Para o procedimento de desenvolvimento de um Java MapReduce de programa, consulte - [programas Java MapReduce desenvolver para o Apache Hadoop no HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

**Para submeter uma tarefa de MapReduce de contagem do word**

1. Open **ISE do Windows PowerShell**. Para obter instruções, consulte [instalar e configurar o Azure PowerShell][powershell-install-configure].
2. Cole o seguinte script do PowerShell:

    ```powershell
    $subscriptionName = "<Azure Subscription Name>"
    $resourceGroupName = "<Resource Group Name>"
    $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

    Select-AzSubscription -SubscriptionName $subscriptionName

    # Define the MapReduce job
    $mrJobDefinition = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "wordcount" `
                                -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Submit the job and wait for job completion
    $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:"
    $mrJob = Start-AzHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $clusterName `
                        -HttpCredential $cred `
                        -JobDefinition $mrJobDefinition

    Wait-AzHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -JobId $mrJob.JobId

    # Get the job output
    $cluster = Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer

    Get-AzHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -DefaultStorageAccountName $defaultStorageAccount `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultStorageContainer  `
        -JobId $mrJob.JobId `
        -DisplayOutputType StandardError

    # Download the job output to the workstation
    $storageContext = New-AzStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey
    Get-AzStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output file
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    ```

    A tarefa de MapReduce produz um ficheiro denominado *parte-r-00000*, que contém palavras e as contagens. O script utiliza o **findstr** command para listar todas as palavras que contém *"there"*.
3. Defina as primeiras três variáveis e execute o script.

## <a name="hdinsight-sample-csharp-streaming"></a>Contagem - c# de transmissão em fluxo de palavras
Hadoop fornece uma API de transmissão em fluxo para MapReduce, que permite-lhe escrever o mapa e reduzir as funções em linguagens diferentes de Java.

> [!NOTE]  
> Os passos neste tutorial aplicam-se apenas a clusters do HDInsight baseado em Windows. Para obter um exemplo de transmissão em fluxo para clusters do HDInsight baseado em Linux, veja [Python desenvolver programas de transmissão para HDInsight](hadoop/apache-hadoop-streaming-python.md).

No exemplo, o mapeador de pontos e a reducer são ficheiros executáveis que leia a entrada proveniente [stdin] [ stdin-stdout-stderr] (de linha por linha) e emitir a saída para [stdout] [ stdin-stdout-stderr]. O programa conta todas as palavras no texto.

Quando um executável é especificado para **mapeadores**, cada tarefa mapeador inicia o executável como um processo separado, quando o mapeador de pontos é inicializado. Como o mapeador de tarefa é executada, ela converte a respetiva entrada em linhas e feeds as linhas para o [stdin] [ stdin-stdout-stderr] do processo.

Entretanto, o mapeador de pontos recolhe a saída e orientados a linha em stdout do processo. Cada linha é convertida num par chave/valor, que é recolhido como a saída do mapeador de pontos. Por predefinição, o prefixo de uma linha até o primeiro caráter separador é a chave e o restante da linha (excluindo o caractere de tabulação) é o valor. Se não houver nenhum caractere de tabulação na linha, toda a linha é considerada como a chave e o valor é nulo.

Quando um executável é especificado para **redutores**, cada tarefa reducer inicia o executável como um processo separado, quando o reducer é inicializado. À medida que a tarefa de reducer é executada, ela converte seus pares de chaves/valores de entrada em linhas e ele alimenta as linhas para o [stdin] [ stdin-stdout-stderr] do processo.

Entretanto, o reducer recolhe a saída e orientados a linha dos [stdout] [ stdin-stdout-stderr] do processo. Cada linha é convertida para um par chave/valor, que é recolhido como a saída do reducer. Por predefinição, o prefixo de uma linha até o primeiro caráter separador é a chave e o restante da linha (excluindo o caractere de tabulação) é o valor.

**Para submeter um c# transmissão em fluxo de trabalho de contagem do word**

* Siga o procedimento na contagem de palavras - Java e substitua a definição de tarefa com a seguinte linha:

    ```powershell
    $mrJobDefinition = New-AzHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    O ficheiro de saída deve ser:

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>Estimador de PI
O estimador de pi utiliza uma estatística (quasi-Monte Carlo) método para calcular o valor de pi. Pontos de aleatoriamente colocados dentro de uma unidade square também são dentro de um círculo inscribed dentro desse quadrado com uma probabilidade igual para a área do círculo, pi/4. O valor de pi pode ser estimado do valor da 4R, onde o R é a proporção entre o número de pontos que estão dentro do círculo para o número total de pontos que estejam no quadrado. Quanto maior for o exemplo de pontos de utilizados, melhor será a estimativa é.

O script fornecido para este exemplo submete uma tarefa de jar do Hadoop e é configurado para ser executado com um valor de 16 mapas, cada um dos quais é necessário para pontos de amostra de 10 milhões de computação para os valores de parâmetros. Estes valores de parâmetro podem ser alterados para melhorar o valor estimado de pi. Para referência, as primeiras 10 casas decimais de pi são 3.1415926535.

**Para submeter um trabalho de estimador de pi**

* Siga o procedimento na contagem de palavras - Java e substitua a definição de tarefa com a seguinte linha:

    ```powershell
    $mrJobJobDefinition = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>Graysort de 10 GB
Este exemplo utiliza um modesto 10 GB de dados para que podem ser executada relativamente rapidamente. Ele usa os aplicativos de MapReduce desenvolvidos pela Owen O'Malley e a Arun Murthy que ganhou o parâmetro de comparação de ordenação de terabytes de fins gerais ("daytona") anual em 2009 com uma taxa de 0.578 TB por minuto (100 TB, em minutos 173). Para obter mais informações sobre este e outros parâmetros de comparação de classificação, consulte a [Sortbenchmark](https://sortbenchmark.org/) site.

Este exemplo utiliza três conjuntos de programas MapReduce:

1. **TeraGen** é um programa MapReduce que pode utilizar para gerar as linhas de dados para classificar.
2. **TeraSort** os dados de entrada de exemplo e usa o MapReduce para classificar os dados para um total do pedido. TeraSort é uma espécie de padrão de funções de MapReduce, exceto um particionador personalizado que utiliza uma lista ordenada de chaves do objeto de amostragem de n-1 que definem o intervalo de chaves para cada reduza. Em particular, todas as chaves desse tipo que fornecem uma amostra [i-1] < = chave < exemplo [i] são enviadas para reduzir a i. Isso são todas as garantias de que as saídas de reduzem i menores do que a saída de reduzir i + 1.
3. **TeraValidate** é um programa MapReduce que valida que a saída globalmente está classificada. Ele cria um mapa por ficheiro no diretório de saída, e cada mapa garante que cada chave é menor ou igual ao anterior. A função map também gera registos das chaves próprio e apelidos de cada arquivo e a função de reduza garante que a primeira chave de ficheiro i é maior do que a última chave do i-1 de ficheiro. Todos os problemas são apresentados como uma saída a reduza com as chaves que estão fora de ordem.

O formato de entrada e saído, utilizado por todos os aplicativos de três, lê e escreve os ficheiros de texto no formato correto. A saída da reduza contém replicação definida como 1, em vez da predefinição de 3, porque o concurso de parâmetro de comparação não requer que os dados de saída ser replicado para vários nós.

Por exemplo, cada um correspondendo a um dos programas MapReduce descritos na introdução necessárias três tarefas:

1. Gerar os dados para ordenação ao executar o **TeraGen** tarefa de MapReduce.
2. Classificar os dados ao executar o **TeraSort** tarefa de MapReduce.
3. Confirme que o tipo de dados foi classificado corretamente ao executar o **TeraValidate** tarefa de MapReduce.

**Para submeter as tarefas**

* Siga o procedimento na contagem de palavras - Java e utilize as seguintes definições de tarefa:

    ```powershell
    $teragen = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teragen" `
                                -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

    $terasort = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "terasort" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

    $teravalidate = New-AzHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teravalidate" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"
    ```

## <a name="next-steps"></a>Passos Seguintes
Este artigo e os artigos em cada um dos exemplos, aprendeu a executar os exemplos incluídos com os clusters do HDInsight com o Azure PowerShell. Para tutoriais sobre como utilizar o Pig, Hive e o MapReduce com o HDInsight, consulte os tópicos seguintes:

* [Introdução à utilização do Apache Hadoop com o Apache Hive no HDInsight para analisar a utilização de aparelho móveis][hdinsight-get-started]
* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight][hdinsight-use-pig]
* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight][hdinsight-use-hive]
* [Submeter tarefas do Apache Hadoop no HDInsight][hdinsight-submit-jobs]

## <a name="appendix-a---the-word-count-source-code"></a>Apêndice A – o código de origem de contagem do Word

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

## <a name="appendix-b---the-word-count-streaming-source-code"></a>Apêndice B - a contagem de palavras, o código de origem de transmissão em fluxo
O programa MapReduce utiliza a aplicação de cat.exe como uma interface de mapeamento para transmitir o texto para a consola e a aplicação de wc.exe como a interface de redução para contar o número de palavras que são transmitidos em fluxo a partir de um documento. O mapeador de pontos e reducer leem carateres, linha por linha, do fluxo de entrada padrão (stdin) e escrever no fluxo de saída padrão (stdout).

```csharp
// The source code for the cat.exe (Mapper).

using System;
using System.IO;

namespace cat
{
    class cat
    {
        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            char[] separators = { ' ', '\n'};
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split(separators);
                foreach (var word in words)
                {
                    Console.WriteLine("{0}\t1", word);
                }
            }
        }
    }
}
```

O código de mapeador de pontos no arquivo cat.cs usa um [StreamReader] [ streamreader] objeto para ler os carateres do fluxo de entrada para o console, que, em seguida, grava o fluxo no fluxo de saída padrão com o estático [ Console. WriteLine] [ console-writeline] método.

```csharp
// The source code for wc.exe (Reducer) is:

using System;
using System.IO;
using System.Linq;
using System.Collections;

namespace wc
{
    class wc
    {
        static void Main(string[] args)
        {
            string line;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            Hashtable wordCount = new Hashtable();
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split('\t');

                string key = words[0];

                if (wordCount.ContainsKey(key) == true)
                {
                    int n = Convert.ToInt32(wordCount[key]);
                    wordCount[key] = Convert.ToString(n + 1);
                }
                else
                {
                    wordCount[key] = words[1];
                }
            }
            foreach (var key in wordCount.Keys)
            {
                Console.WriteLine("{0} {1}", key, wordCount[key]);
            }
        }
    }
}
```

O código de reducer no ficheiro wc.cs usa um [StreamReader] [ streamreader] objeto ler carateres do fluxo de entrada padrão que foram produzidas pelo mapeador de pontos de cat.exe. Como ele lê os caracteres com o [Console. WriteLine] [ console-writeline] método, que conta as palavras por contando espaços e carateres de fim da linha no final de cada palavra. Em seguida, escreve o total para o fluxo de saída padrão com o [Console. WriteLine] [ console-writeline] método.

## <a name="appendix-c---the-pi-estimator-source-code"></a>Apêndice C - o código de origem de estimador de Pi
O estimador de pi código Java que contém as funções de mapeador de pontos e reducer está disponível para inspeção abaixo. O programa de mapeador de pontos gera um número especificado de pontos de aleatoriamente colocada dentro de um quadrado de unidade e, em seguida, a conta o número desses pontos que estão dentro do círculo. O programa de reducer acumula pontos contados pelos mapeadores e, em seguida, calcula o valor de pi do 4R fórmulas, onde o R é a proporção entre o número de pontos contabilizado no interior do círculo para o número total de pontos que estejam no quadrado.

```java
/**
* Licensed to the Apache Software Foundation (ASF) under one
* or more contributor license agreements. See the NOTICE file
* distributed with this work for additional information
* regarding copyright ownership. The ASF licenses this file
* to you under the Apache License, Version 2.0 (the
* "License"); you may not use this file except in compliance
* with the License. You may obtain a copy of the License at
*
* https://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or     implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package org.apache.hadoop.examples;

import java.io.IOException;
import java.math.BigDecimal;
import java.util.Iterator;

import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BooleanWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.WritableComparable;
import org.apache.hadoop.io.SequenceFile.CompressionType;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.SequenceFileInputFormat;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

//A Map-reduce program to estimate the value of Pi
//using quasi-Monte Carlo method.
//
//Mapper:
//Generate points in a unit square
//and then count points inside/outside of the inscribed circle of the square.
//
//Reducer:
//Accumulate points inside/outside results from the mappers.
//Let numTotal = numInside + numOutside.
//The fraction numInside/numTotal is a rational approximation of
//the value (Area of the circle)/(Area of the square),
//where the area of the inscribed circle is Pi/4
//and the area of unit square is 1.
//Then, Pi is estimated value to be 4(numInside/numTotal).
//

public class PiEstimator extends Configured implements Tool {
//tmp directory for input/output
static private final Path TMP_DIR = new Path(
PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

//2-dimensional Halton sequence {H(i)},
//where H(i) is a 2-dimensional point and i >= 1 is the index.
//Halton sequence is used to generate sample points for Pi estimation.
private static class HaltonSequence {
// Bases
static final int[] P = {2, 3};
//Maximum number of digits allowed
static final int[] K = {63, 40};

private long index;
private double[] x;
private double[][] q;
private int[][] d;

//Initialize to H(startindex),
//so the sequence begins with H(startindex+1).
HaltonSequence(long startindex) {
index = startindex;
x = new double[K.length];
q = new double[K.length][];
d = new int[K.length][];
for(int i = 0; i < K.length; i++) {
q[i] = new double[K[i]];
d[i] = new int[K[i]];
}

for(int i = 0; i < K.length; i++) {
long k = index;
x[i] = 0;

for(int j = 0; j < K[i]; j++) {
q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
d[i][j] = (int)(k % P[i]);
k = (k - d[i][j])/P[i];
x[i] += d[i][j] * q[i][j];
}
}
}

//Compute next point.
//Assume the current point is H(index).
//Compute H(index+1).
//@return a 2-dimensional point with coordinates in [0,1)^2
double[] nextPoint() {
index++;
for(int i = 0; i < K.length; i++) {
for(int j = 0; j < K[i]; j++) {
d[i][j]++;
x[i] += q[i][j];
if (d[i][j] < P[i]) {
break;
}
d[i][j] = 0;
x[i] -= (j == 0? 1.0: q[i][j-1]);
}
}
return x;
}
}

//Mapper class for Pi estimation.
//Generate points in a unit square and then
//count points inside/outside of the inscribed circle of the square.
public static class PiMapper extends MapReduceBase
implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

//Map method.
//@param offset samples starting from the (offset+1)th sample.
//@param size the number of samples for this map
//@param out output {true->numInside, false->numOutside}
//@param reporter
public void map(LongWritable offset,
LongWritable size,
OutputCollector<BooleanWritable, LongWritable> out,
Reporter reporter) throws IOException {

final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
long numInside = 0L;
long numOutside = 0L;

for(long i = 0; i < size.get(); ) {
//generate points in a unit square
final double[] point = haltonsequence.nextPoint();

//count points inside/outside of the inscribed circle of the square
final double x = point[0] - 0.5;
final double y = point[1] - 0.5;
if (x*x + y*y > 0.25) {
numOutside++;
} else {
numInside++;
}

//report status
i++;
if (i % 1000 == 0) {
reporter.setStatus("Generated " + i + " samples.");
}
}

//output map results
out.collect(new BooleanWritable(true), new LongWritable(numInside));
out.collect(new BooleanWritable(false), new LongWritable(numOutside));
}
}

//Reducer class for Pi estimation.
//Accumulate points inside/outside results from the mappers.
public static class PiReducer extends MapReduceBase
implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

private long numInside = 0;
private long numOutside = 0;
private JobConf conf; //configuration for accessing the file system

//Store job configuration.
@Override
public void configure(JobConf job) {
conf = job;
}

// Accumulate number of points inside/outside results from the mappers.
// @param isInside Is the points inside?
// @param values An iterator to a list of point counts
// @param output dummy, not used here.
// @param reporter

public void reduce(BooleanWritable isInside,
Iterator<LongWritable> values,
OutputCollector<WritableComparable<?>, Writable> output,
Reporter reporter) throws IOException {
if (isInside.get()) {
for(; values.hasNext(); numInside += values.next().get());
} else {
for(; values.hasNext(); numOutside += values.next().get());
}
}

//Reduce task done, write output to a file.
@Override
public void close() throws IOException {
//write output to a file
Path outDir = new Path(TMP_DIR, "out");
Path outFile = new Path(outDir, "reduce-out");
FileSystem fileSys = FileSystem.get(conf);
SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
outFile, LongWritable.class, LongWritable.class,
CompressionType.NONE);
writer.append(new LongWritable(numInside), new LongWritable(numOutside));
writer.close();
}
}

//Run a map/reduce job for estimating Pi.
//@return the estimated value of Pi.
public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
)
throws IOException {
//setup job conf
jobConf.setJobName(PiEstimator.class.getSimpleName());

jobConf.setInputFormat(SequenceFileInputFormat.class);

jobConf.setOutputKeyClass(BooleanWritable.class);
jobConf.setOutputValueClass(LongWritable.class);
jobConf.setOutputFormat(SequenceFileOutputFormat.class);

jobConf.setMapperClass(PiMapper.class);
jobConf.setNumMapTasks(numMaps);

jobConf.setReducerClass(PiReducer.class);
jobConf.setNumReduceTasks(1);

// turn off speculative execution, because DFS doesn't handle
// multiple writers to the same file.
jobConf.setSpeculativeExecution(false);

//setup input/output directories
final Path inDir = new Path(TMP_DIR, "in");
final Path outDir = new Path(TMP_DIR, "out");
FileInputFormat.setInputPaths(jobConf, inDir);
FileOutputFormat.setOutputPath(jobConf, outDir);

final FileSystem fs = FileSystem.get(jobConf);
if (fs.exists(TMP_DIR)) {
throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
+ " already exists. Remove it first.");
}
if (!fs.mkdirs(inDir)) {
throw new IOException("Cannot create input directory " + inDir);
}

//generate an input file for each map task
try {
for(int i=0; i < numMaps; ++i) {
final Path file = new Path(inDir, "part"+i);
final LongWritable offset = new LongWritable(i * numPoints);
final LongWritable size = new LongWritable(numPoints);
final SequenceFile.Writer writer = SequenceFile.createWriter(
fs, jobConf, file,
LongWritable.class, LongWritable.class, CompressionType.NONE);
try {
writer.append(offset, size);
} finally {
writer.close();
}
System.out.println("Wrote input for Map #"+i);
}

//start a map/reduce job
System.out.println("Starting Job");
final long startTime = System.currentTimeMillis();
JobClient.runJob(jobConf);
final double duration = (System.currentTimeMillis() - startTime)/1000.0;
System.out.println("Job Finished in " + duration + " seconds");

//read outputs
Path inFile = new Path(outDir, "reduce-out");
LongWritable numInside = new LongWritable();
LongWritable numOutside = new LongWritable();
SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
try {
reader.next(numInside, numOutside);
} finally {
reader.close();
}

//compute estimated value
return BigDecimal.valueOf(4).setScale(20)
.multiply(BigDecimal.valueOf(numInside.get()))
.divide(BigDecimal.valueOf(numMaps))
.divide(BigDecimal.valueOf(numPoints));
} finally {
fs.delete(TMP_DIR, true);
}
}

//Parse arguments and then runs a map/reduce job.
//Print output in standard out.
//@return a non-zero if there is an error. Otherwise, return 0.
public int run(String[] args) throws Exception {
if (args.length != 2) {
System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
ToolRunner.printGenericCommandUsage(System.err);
return -1;
}

final int nMaps = Integer.parseInt(args[0]);
final long nSamples = Long.parseLong(args[1]);

System.out.println("Number of Maps = " + nMaps);
System.out.println("Samples per Map = " + nSamples);

final JobConf jobConf = new JobConf(getConf(), getClass());
System.out.println("Estimated value of Pi is "
+ estimate(nMaps, nSamples, jobConf));
return 0;
}

//main method for running it as a stand alone command.
public static void main(String[] argv) throws Exception {
System.exit(ToolRunner.run(null, new PiEstimator(), argv));
}
}
```

## <a name="appendix-d---the-10gb-graysort-source-code"></a>Apêndice D - o código de origem de graysort de 10gb
O código para o programa TeraSort MapReduce é apresentado para inspeção nesta secção.

```java
/**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements.  See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership.  The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License.  You may obtain a copy of the License at
    *
    *     https://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

package org.apache.hadoop.examples.terasort;

import java.io.IOException;
import java.io.PrintStream;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.filecache.DistributedCache;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.Partitioner;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
    * Generates the sampled split points, launches the job,
    * and waits for it to finish.
    * <p>
    * To run the program:
    * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
    */

public class TeraSort extends Configured implements Tool {
    private static final Log LOG = LogFactory.getLog(TeraSort.class);

    /**
    * A partitioner that splits text keys into roughly equal
    * partitions in a global sorted order.
    */

    static class TotalOrderPartitioner implements Partitioner<Text,Text>{
    private TrieNode trie;
    private Text[] splitPoints;

    /**
        * A generic trie node
        */
    static abstract class TrieNode {
        private int level;
        TrieNode(int level) {
        this.level = level;
        }
        abstract int findPartition(Text key);
        abstract void print(PrintStream strm) throws IOException;
        int getLevel() {
        return level;
        }
    }

    /**
        * An inner trie node that contains 256 children based on the next
        * character.
        */
    static class InnerTrieNode extends TrieNode {
        private TrieNode[] child = new TrieNode[256];

        InnerTrieNode(int level) {
        super(level);
        }
        int findPartition(Text key) {
        int level = getLevel();
        if (key.getLength() <= level) {
            return child[0].findPartition(key);
        }
        return child[key.getBytes()[level]].findPartition(key);
        }
        void setChild(int idx, TrieNode child) {
        this.child[idx] = child;
        }
        void print(PrintStream strm) throws IOException {
        for(int ch=0; ch < 255; ++ch) {
            for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
            }
            strm.print(ch);
            strm.println(" ->");
            if (child[ch] != null) {
            child[ch].print(strm);
            }
        }
        }
    }

    /**
        * A leaf trie node that does string compares to figure out where the given
        * key belongs between lower..upper.
        */
    static class LeafTrieNode extends TrieNode {
        int lower;
        int upper;
        Text[] splitPoints;
        LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
        super(level);
        this.splitPoints = splitPoints;
        this.lower = lower;
        this.upper = upper;
        }
        int findPartition(Text key) {
        for(int i=lower; i<upper; ++i) {
            if (splitPoints[i].compareTo(key) >= 0) {
            return i;
            }
        }
        return upper;
        }
        void print(PrintStream strm) throws IOException {
        for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
        }
        strm.print(lower);
        strm.print(", ");
        strm.println(upper);
        }
    }

    /**
        * Read the cut points from the given sequence file.
        * @param fs the file system
        * @param p the path to read
        * @param job the job config
        * @return the strings to split the partitions on
        * @throws IOException
        */
    private static Text[] readPartitions(FileSystem fs, Path p,
                                            JobConf job) throws IOException {
        SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
        List<Text> parts = new ArrayList<Text>();
        Text key = new Text();
        NullWritable value = NullWritable.get();
        while (reader.next(key, value)) {
        parts.add(key);
        key = new Text();
        }
        reader.close();
        return parts.toArray(new Text[parts.size()]);
    }

    /**
        * Given a sorted set of cut points, build a trie that will find the correct
        * partition quickly.
        * @param splits the list of cut points
        * @param lower the lower bound of partitions 0..numPartitions-1
        * @param upper the upper bound of partitions 0..numPartitions-1
        * @param prefix the prefix that we have already checked against
        * @param maxDepth the maximum depth we will build a trie for
        * @return the trie node that will divide the splits correctly
        */
    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                        Text prefix, int maxDepth) {
        int depth = prefix.getLength();
        if (depth >= maxDepth || lower == upper) {
        return new LeafTrieNode(depth, splits, lower, upper);
        }
        InnerTrieNode result = new InnerTrieNode(depth);
        Text trial = new Text(prefix);
        // append an extra byte on to the prefix
        trial.append(new byte[1], 0, 1);
        int currentBound = lower;
        for(int ch = 0; ch < 255; ++ch) {
        trial.getBytes()[depth] = (byte) (ch + 1);
        lower = currentBound;
        while (currentBound < upper) {
            if (splits[currentBound].compareTo(trial) >= 0) {
            break;
            }
            currentBound += 1;
        }
        trial.getBytes()[depth] = (byte) ch;
        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                        maxDepth);
        }
        // pick up the rest
        trial.getBytes()[depth] = 127;
        result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                    maxDepth);
        return result;
    }

    public void configure(JobConf job) {
        try {
        FileSystem fs = FileSystem.getLocal(job);
        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
        splitPoints = readPartitions(fs, partFile, job);
        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
        } catch (IOException ie) {
        throw new IllegalArgumentException("can't read partitions file", ie);
        }
    }

    public TotalOrderPartitioner() {
    }

    public int getPartition(Text key, Text value, int numPartitions) {
        return trie.findPartition(key);
    }

    }

    public int run(String[] args) throws Exception {
    LOG.info("starting");
    JobConf job = (JobConf) getConf();
    Path inputDir = new Path(args[0]);
    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
    URI partitionUri = new URI(partitionFile.toString() +
                                "#" + TeraInputFormat.PARTITION_FILENAME);
    TeraInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setJobName("TeraSort");
    job.setJarByClass(TeraSort.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(Text.class);
    job.setInputFormat(TeraInputFormat.class);
    job.setOutputFormat(TeraOutputFormat.class);
    job.setPartitionerClass(TotalOrderPartitioner.class);
    TeraInputFormat.writePartitionFile(job, partitionFile);
    DistributedCache.addCacheFile(partitionUri, job);
    DistributedCache.createSymlink(job);
    job.setInt("dfs.replication", 1);
    TeraOutputFormat.setFinalSync(job, true);
    JobClient.runJob(job);
    LOG.info("done");
    return 0;
    }

    /**
    * @param args
    */

    public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
    System.exit(res);
    }
}
```

[hdinsight-submit-jobs]: hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:hadoop/apache-hadoop-introduction.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]: hadoop/hdinsight-use-pig.md

[streamreader]: https://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: https://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx

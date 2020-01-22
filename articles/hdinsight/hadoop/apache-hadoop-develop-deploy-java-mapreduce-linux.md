---
title: Criar um MapReduce Java para Apache Hadoop-Azure HDInsight
description: Saiba como usar o Apache Maven para criar um aplicativo MapReduce baseado em Java e, em seguida, executá-lo com o Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311959"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Desenvolver programas Java MapReduce para Apache Hadoop no HDInsight

Saiba como usar o Apache Maven para criar um aplicativo MapReduce baseado em Java e, em seguida, executá-lo com Apache Hadoop no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* O [Apache Maven](https://maven.apache.org/download.cgi) foi [instalado](https://maven.apache.org/install.html) corretamente de acordo com o Apache.  O Maven é um sistema de compilação de projeto para projetos Java.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

O ambiente usado para este artigo foi um computador que executa o Windows 10. Os comandos foram executados em um prompt de comando e os vários arquivos foram editados com o bloco de notas. Modifique de acordo com o seu ambiente.

Em um prompt de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Digite o seguinte comando para criar um projeto Maven chamado **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Esse comando cria um diretório com o nome especificado pelo parâmetro `artifactID` (**wordcountjava** neste exemplo). Esse diretório contém os seguintes itens:

    * `pom.xml`-o [modelo de objeto de projeto (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém informações e detalhes de configuração usados para compilar o projeto.
    * src\main\java\org\apache\hadoop\examples: contém o código do aplicativo.
    * src\test\java\org\apache\hadoop\examples: contém testes para seu aplicativo.

1. Remova o código de exemplo gerado. Exclua os arquivos de teste e de aplicativo gerados `AppTest.java`e `App.java` inserindo os comandos abaixo:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Atualizar o modelo de objeto do projeto

Para obter uma referência completa do arquivo pom. xml, consulte https://maven.apache.org/pom.html. Abra `pom.xml` inserindo o comando abaixo:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Adicionar dependências

Em `pom.xml`, adicione o seguinte texto na seção `<dependencies>`:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Isso define as bibliotecas necessárias (listadas em &lt;artefatoid\>) com uma versão específica (listada em &lt;\>de versão). No momento da compilação, essas dependências são baixadas do repositório Maven padrão. Você pode usar a [pesquisa de repositório do Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para exibir mais.

O `<scope>provided</scope>` informa ao Maven que essas dependências não devem ser empacotadas com o aplicativo, pois são fornecidas pelo cluster HDInsight em tempo de execução.

> [!IMPORTANT]
> A versão usada deve corresponder à versão do Hadoop presente no cluster. Para obter mais informações sobre versões, consulte o documento [versionamento de componente do HDInsight](../hdinsight-component-versioning.md) .

### <a name="build-configuration"></a>Configuração de compilação

Os plug-ins do Maven permitem que você personalize os estágios de Build do projeto. Esta seção é usada para adicionar plug-ins, recursos e outras opções de configuração de compilação.

Adicione o seguinte código ao arquivo de `pom.xml` e, em seguida, salve e feche o arquivo. Esse texto deve estar dentro das marcas de `<project>...</project>` no arquivo, por exemplo, entre `</dependencies>` e `</project>`.

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
        </execution>
        </executions>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

Esta seção configura o plug-in do compilador Apache Maven e o plug-in de sombreamento Apache Maven. O plug-in do compilador é usado para compilar a topologia. O plug-in de sombreamento é usado para impedir a duplicação de licença no pacote JAR criado pelo Maven. Esse plug-in é usado para impedir um erro de "arquivos de licença duplicados" no tempo de execução no cluster HDInsight. Usar o plug-in Maven-Shad-plugin com a implementação de `ApacheLicenseResourceTransformer` impede o erro.

O plug-in do Maven-Shad-plugin também produz um jar Uber que contém todas as dependências exigidas pelo aplicativo.

Guarde o ficheiro `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Criar o aplicativo MapReduce

1. Digite o comando a seguir para criar e abrir um novo arquivo `WordCount.java`. Selecione **Sim** no prompt para criar um novo arquivo.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Em seguida, copie e cole o código Java abaixo no novo arquivo. Em seguida, feche o arquivo.

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

    Observe que o nome do pacote é `org.apache.hadoop.examples` e o nome da classe é `WordCount`. Você usa esses nomes ao enviar o trabalho MapReduce.

## <a name="build-and-package-the-application"></a>Compilar e empacotar o aplicativo

No diretório `wordcountjava`, use o seguinte comando para criar um arquivo JAR que contém o aplicativo:

```cmd
mvn clean package
```

Este comando limpa todos os artefatos de compilação anteriores, baixa as dependências que ainda não foram instaladas e, em seguida, compila e empacota o aplicativo.

Quando o comando for concluído, o diretório `wordcountjava/target` conterá um arquivo chamado `wordcountjava-1.0-SNAPSHOT.jar`.

> [!NOTE]
> O arquivo de `wordcountjava-1.0-SNAPSHOT.jar` é um uberjar, que contém não apenas o trabalho WordCount, mas também as dependências que o trabalho requer em tempo de execução.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Carregar o JAR e executar trabalhos (SSH)

As etapas a seguir usam `scp` para copiar o JAR para o nó principal primário do Apache HBase no cluster HDInsight. O comando `ssh` é usado para se conectar ao cluster e executar o exemplo diretamente no nó de cabeçalho.

1. Carregue o JAR no cluster. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight e, em seguida, digite o seguinte comando:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Conecte-se ao cluster. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight e, em seguida, digite o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Na sessão SSH, use o seguinte comando para executar o aplicativo MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Esse comando inicia o aplicativo WordCount MapReduce. O arquivo de entrada é `/example/data/gutenberg/davinci.txt`e o diretório de saída é `/example/data/wordcountout`. O arquivo de entrada e a saída são armazenados no armazenamento padrão do cluster.

1. Quando o trabalho for concluído, use o seguinte comando para exibir os resultados:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Você deve receber uma lista de palavras e contagens, com valores semelhantes ao seguinte texto:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a desenvolver um trabalho do MapReduce em Java. Consulte os documentos a seguir para obter outras maneiras de trabalhar com o HDInsight.

* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
* [Centro de Programadores do Java](https://azure.microsoft.com/develop/java/)

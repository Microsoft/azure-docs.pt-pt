---
title: Criar Java MapReduce para Apache Hadoop - Azure HDInsight
description: Aprenda a usar Apache Maven para criar uma aplicação MapReduce baseada em Java e, em seguida, executá-la com Hadoop em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 84d9253b865ddac6d97395af3d8632e29cc2ea24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323813"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Desenvolver programas Java MapReduce para Apache Hadoop em HDInsight

Aprenda a usar Apache Maven para criar uma aplicação MapReduce baseada em Java e, em seguida, executá-la com Apache Hadoop em Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado de](https://maven.apache.org/install.html) acordo com Apache.  Maven é um sistema de construção de projetos para projetos java.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

O ambiente utilizado para este artigo era um computador que executava o Windows 10. Os comandos foram executados num pedido de comando, e os vários ficheiros foram editados com Bloco de Notas. Modifique em conformidade para o seu ambiente.

A partir de um pedido de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Insira o seguinte comando para criar um projeto Maven chamado **wordcountjava:**

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Este comando cria um diretório com o nome especificado pelo `artifactID` parâmetro **(wordcountjava** neste exemplo.) Este diretório contém os seguintes itens:

    * `pom.xml` - O [Modelo de Objeto de Projeto (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém detalhes de informação e configuração utilizados para construir o projeto.
    * src\main\java\org\apache\hadoop\exemplos: Contém o seu código de aplicação.
    * src\test\java\org\apache\hadoop\exemplos: Contém testes para a sua aplicação.

1. Remova o código de exemplo gerado. Elimine os ficheiros de teste e aplicação gerados `AppTest.java` e `App.java` introduzindo os comandos abaixo:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Atualizar o modelo de objeto de projeto

Para uma referência completa do ficheiro pom.xml, consulte https://maven.apache.org/pom.html . Abra `pom.xml` introduzindo o comando abaixo:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Adicionar dependências

In `pom.xml` , adicione o seguinte texto na `<dependencies>` secção:

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

Isto define bibliotecas necessárias (listadas dentro &lt; do \> artifactId) com uma versão específica (listada na &lt; \> versão). No momento da compilação, estas dependências são descarregadas do repositório padrão de Maven. Você pode usar a [pesquisa do repositório Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver mais.

O `<scope>provided</scope>` diz a Maven que estas dependências não devem ser embaladas com a aplicação, uma vez que são fornecidas pelo cluster HDInsight em tempo de execução.

> [!IMPORTANT]
> A versão utilizada deve coincidir com a versão de Hadoop presente no seu cluster. Para obter mais informações sobre versões, consulte o documento [de versão do componente HDInsight.](../hdinsight-component-versioning.md)

### <a name="build-configuration"></a>Configuração de construção

Os plug-ins maven permitem-lhe personalizar as fases de construção do projeto. Esta secção é usada para adicionar plug-ins, recursos e outras opções de configuração de construção.

Adicione o seguinte código ao `pom.xml` ficheiro e, em seguida, guarde e feche o ficheiro. Este texto deve estar dentro das `<project>...</project>` etiquetas do ficheiro, por exemplo, entre `</dependencies>` e . `</project>` .

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

Esta secção configura o Plugin Do Compilador Apache Maven e o Plugin de Sombra Apache Maven. O plug-in do compilador é utilizado para compilar a topologia. O plug-in de sombra é utilizado para evitar a duplicação de licenças no pacote JAR que é construído pela Maven. Este plugin é utilizado para evitar um erro de "duplicação de ficheiros de licença" no tempo de execução no cluster HDInsight. A utilização de plugin de sombra maven com a `ApacheLicenseResourceTransformer` implementação evita o erro.

O maven-shade-plugin também produz um frasco uber que contém todas as dependências exigidas pela aplicação.

Guarde o ficheiro `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Criar a aplicação MapReduce

1. Introduza o comando abaixo para criar e abrir um novo ficheiro `WordCount.java` . Selecione **Sim** na origem para criar um novo ficheiro.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Em seguida, copie e cole o código java abaixo no novo ficheiro. Em seguida, feche o arquivo.

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

    Note que o nome do pacote é `org.apache.hadoop.examples` e o nome da classe é `WordCount` . Usa estes nomes quando envia o trabalho de MapReduce.

## <a name="build-and-package-the-application"></a>Construa e embale a aplicação

A partir do `wordcountjava` diretório, utilize o seguinte comando para construir um ficheiro JAR que contenha a aplicação:

```cmd
mvn clean package
```

Este comando limpa quaisquer artefactos anteriores de construção, descarrega quaisquer dependências que ainda não tenham sido instaladas, e depois constrói e embala a aplicação.

Uma vez terminado o comando, o `wordcountjava/target` diretório contém um ficheiro chamado `wordcountjava-1.0-SNAPSHOT.jar` .

> [!NOTE]
> O `wordcountjava-1.0-SNAPSHOT.jar` ficheiro é um uberjar, que contém não só o trabalho wordCount, mas também dependências que o trabalho requer em tempo de execução.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Faça o upload do JAR e executar empregos (SSH)

Os passos seguintes usam `scp` para copiar o JAR para o nó de cabeça primária do seu Apache HBase no cluster HDInsight. O `ssh` comando é então utilizado para ligar ao cluster e executar o exemplo diretamente no nó de cabeça.

1. Faça o upload do jarro para o aglomerado. `CLUSTERNAME`Substitua-o pelo seu nome de cluster HDInsight e, em seguida, introduza o seguinte comando:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Ligue-se ao aglomerado. `CLUSTERNAME`Substitua-o pelo seu nome de cluster HDInsight e, em seguida, introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sessão SSH, utilize o seguinte comando para executar a aplicação MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Este comando inicia a aplicação WordCount MapReduce. O ficheiro de entrada é `/example/data/gutenberg/davinci.txt` , e o diretório de saída é `/example/data/wordcountout` . Tanto o ficheiro de entrada como a saída são armazenados no armazenamento predefinido para o cluster.

1. Uma vez concluído o trabalho, utilize o seguinte comando para visualizar os resultados:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Deverá receber uma lista de palavras e contagens, com valores semelhantes ao seguinte texto:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a desenvolver um trabalho java MapReduce. Consulte os seguintes documentos para outras formas de trabalhar com a HDInsight.

* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Use MapReduce com HDInsight](hdinsight-use-mapreduce.md)
* [Centro de Programadores do Java](https://azure.microsoft.com/develop/java/)

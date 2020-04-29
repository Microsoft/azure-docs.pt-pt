---
title: Criar Java MapReduce para Apache Hadoop - Azure HDInsight
description: Aprenda a usar Apache Maven para criar uma aplicação MapReduce baseada em Java e, em seguida, executá-la com Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76311959"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Desenvolver java mapReduce programas para Apache Hadoop no HDInsight

Aprenda a usar Apache Maven para criar uma aplicação MapReduce baseada em Java e, em seguida, executá-la com Apache Hadoop no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado](https://maven.apache.org/install.html) de acordo com Apache.  Maven é um sistema de construção de projetos para projetos Java.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

O ambiente utilizado para este artigo era um computador que executava o Windows 10. Os comandos foram executados num pedido de comando, e os vários ficheiros foram editados com o Bloco de Notas. Modifique em conformidade para o seu ambiente.

A partir de um pedido de comando, introduza os comandos abaixo para criar um ambiente de trabalho:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Insira o seguinte comando para criar um projeto Maven chamado **wordcountjava:**

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Este comando cria um diretório com `artifactID` o nome especificado pelo parâmetro **(wordcountjava** neste exemplo.) Este diretório contém os seguintes itens:

    * `pom.xml`- O Modelo de [Objeto sinuoso (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém informações e detalhes de configuração utilizados para construir o projeto.
    * src\main\java\org\apache\hadoop\exemplos: Contém o seu código de aplicação.
    * src\test\java\org\apache\hadoop\exemplos: Contém testes para a sua aplicação.

1. Remova o código de exemplo gerado. Elimine os ficheiros `AppTest.java`de `App.java` teste e aplicação gerados e introduzindo os comandos abaixo:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Atualizar o Modelo de Objeto de Projeto

Para obter uma referência completa do ficheiro https://maven.apache.org/pom.htmlpom.xml, consulte . Abra `pom.xml` entrando no comando abaixo:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Adicionar dependências

Em, `pom.xml`adicione o seguinte `<dependencies>` texto na secção:

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

Isto define as bibliotecas &lt;necessárias\>(listadas no artefactoId) com uma versão específica (listada na &lt;versão).\> No momento da compilação, estas dependências são descarregadas do repositório padrão maven. Pode utilizar a pesquisa de [repositório Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver mais.

O `<scope>provided</scope>` Maven diz à Maven que estas dependências não devem ser embaladas com a aplicação, uma vez que são fornecidas pelo cluster HDInsight em tempo de execução.

> [!IMPORTANT]
> A versão utilizada deve coincidir com a versão de Hadoop presente no seu cluster. Para obter mais informações sobre as versões, consulte o documento de versão do [componente HDInsight.](../hdinsight-component-versioning.md)

### <a name="build-configuration"></a>Configuração de construção

Os plug-ins Maven permitem personalizar as fases de construção do projeto. Esta secção é usada para adicionar plug-ins, recursos e outras opções de configuração de construção.

Adicione o seguinte `pom.xml` código ao ficheiro e, em seguida, guarde e feche o ficheiro. Este texto deve `<project>...</project>` estar dentro das etiquetas do `</dependencies>` `</project>`ficheiro, por exemplo, entre e .

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

Esta secção configura o Apache Maven Compiler Plugin e o Apache Maven Shade Plugin. O plug-in do compilador é usado para compilar a topologia. O plug-in de sombra é usado para evitar a duplicação de licenças no pacote JAR que é construído pela Maven. Este plugin é utilizado para evitar um erro de "duplicado ficheiros de licença" no tempo de execução no cluster HDInsight. A utilização de maven-shade-plugin com a `ApacheLicenseResourceTransformer` implementação evita o erro.

O plugin de sombra maven também produz um jarro uber que contém todas as dependências exigidas pela aplicação.

Guarde o ficheiro `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Criar a aplicação MapReduce

1. Introduza o comando abaixo para `WordCount.java`criar e abra um novo ficheiro . Selecione **Sim** na solicitação para criar um novo ficheiro.

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

    Repare maquete e `org.apache.hadoop.examples` o `WordCount`nome da classe é . Usa estes nomes quando submete o trabalho MapReduce.

## <a name="build-and-package-the-application"></a>Construir e embalar a aplicação

A `wordcountjava` partir do diretório, utilize o seguinte comando para construir um ficheiro JAR que contenha a aplicação:

```cmd
mvn clean package
```

Este comando limpa quaisquer artefactos de construção anteriores, descarrega quaisquer dependências que ainda não tenham sido instaladas, e depois constrói e embala a aplicação.

Uma vez terminada `wordcountjava/target` a ordem, o `wordcountjava-1.0-SNAPSHOT.jar`diretório contém um ficheiro chamado .

> [!NOTE]
> O `wordcountjava-1.0-SNAPSHOT.jar` ficheiro é um uberjar, que contém não só o trabalho do WordCount, mas também dependências que o trabalho requer no tempo de execução.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Faça upload do JAR e gere postos de trabalho (SSH)

Os seguintes `scp` passos são utilizados para copiar o JAR para o nó principal da cabeça do seu Apache HBase no cluster HDInsight. O `ssh` comando é então utilizado para ligar ao cluster e executar o exemplo diretamente no nó da cabeça.

1. Faça upload do jarro para o aglomerado. Substitua-o `CLUSTERNAME` pelo nome do cluster HDInsight e introduza o seguinte comando:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Ligue-se ao aglomerado. Substitua-o `CLUSTERNAME` pelo nome do cluster HDInsight e introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sessão SSH, utilize o seguinte comando para executar a aplicação MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Este comando inicia a aplicação WordCount MapReduce. O ficheiro de `/example/data/gutenberg/davinci.txt`entrada é , `/example/data/wordcountout`e o diretório de saída é . Tanto o ficheiro de entrada como a saída são armazenados no armazenamento predefinido do cluster.

1. Uma vez concluída a função, utilize o seguinte comando para visualizar os resultados:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Deve receber uma lista de palavras e contagens, com valores semelhantes ao seguinte texto:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a desenvolver um trabalho java MapReduce. Consulte os seguintes documentos para outras formas de trabalhar com o HDInsight.

* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Utilizar mapeiaReduzir com HDInsight](hdinsight-use-mapreduce.md)
* [Centro de Programadores do Java](https://azure.microsoft.com/develop/java/)

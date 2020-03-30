---
title: Apache Storm exemplo Java topology - Azure HDInsight
description: Aprenda a criar topoologias de tempestade Apache em Java criando um exemplo de topologia de contagem de palavras.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 75100b47ddf8f36ed9a22ff3073c439f8ad9040b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083286"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Crie uma topologia da tempestade Apache em Java

Aprenda a criar uma topologia baseada em Java para [a Tempestade Apache.](https://storm.apache.org/) Aqui, cria-se uma topologia de tempestade que implementa uma aplicação de contagem de palavras. [Usa-se o Apache Maven](https://maven.apache.org/) para construir e embalar o projeto. Depois, aprende-se a definir a topologia usando a estrutura do [Fluxo de Tempestade Apache.](https://storm.apache.org/releases/2.0.0/flux.html)

Depois de completar os passos deste documento, pode implementar a topologia para a Tempestade Apache no HDInsight.

> [!NOTE]  
> Uma versão completa dos exemplos de topologia storm [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)criados neste documento está disponível em .

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado](https://maven.apache.org/install.html) de acordo com Apache.  Maven é um sistema de construção de projetos para projetos Java.

## <a name="test-environment"></a>Ambiente de teste

O ambiente utilizado para este artigo era um computador que executava o Windows 10.  Os comandos foram executados num pedido de comando, e os vários ficheiros foram editados com o Bloco de Notas.

A partir de um pedido de comando, introduza os comandos abaixo para criar um ambiente de trabalho:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Insira o seguinte comando para criar um projeto Maven chamado **WordCount:**

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Este comando cria um `WordCount` diretório nomeado no local atual, que contém um projeto básico de Maven. O segundo comando altera o `WordCount`atual diretório de trabalho para . O terceiro comando cria um `resources`novo diretório, que será usado mais tarde.  O `WordCount` diretório contém os seguintes itens:

* `pom.xml`: Contém configurações para o projeto Maven.
* `src\main\java\com\microsoft\example`: Contém o seu código de candidatura.
* `src\test\java\com\microsoft\example`: Contém testes para a sua aplicação.  

### <a name="remove-the-generated-example-code"></a>Remova o código de exemplo gerado

Elimine os ficheiros `AppTest.java`de `App.java` teste e aplicação gerados e introduzindo os comandos abaixo:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Adicione repositórios Maven

O HDInsight baseia-se na Plataforma de Dados Hortonworks (HDP), pelo que recomendamos a utilização do repositório Hortonworks para descarregar dependências para os seus projetos apache storm.  

Abra `pom.xml` entrando no comando abaixo:

```cmd
notepad pom.xml
```

Em seguida, adicione o `<url>https://maven.apache.org</url>` seguinte XML após a linha:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Adicionar propriedades

A Maven permite-lhe definir valores de nível de projeto chamados propriedades. In, `pom.xml`adicione o seguinte `</repositories>` texto após a linha:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Pode agora utilizar este valor noutras `pom.xml`secções do . Por exemplo, ao especificar a versão dos componentes `${storm.version}` Storm, pode utilizar em vez de codificar um valor.

## <a name="add-dependencies"></a>Adicionar dependências

Adicione uma dependência dos componentes da tempestade. Em, `pom.xml`adicione o seguinte `<dependencies>` texto na secção:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Na hora da compilação, Maven `storm-core` usa esta informação para procurar no repositório Maven. Olha pela primeira vez no repositório do seu computador local. Se os ficheiros não estiverem lá, maven descarrega-os do repositório público de Maven e armazena-os no repositório local.

> [!NOTE]  
> Reparem `<scope>provided</scope>` na linha nesta secção. Esta definição diz à Maven para excluir o **núcleo de tempestade** de quaisquer ficheiros JAR que são criados, porque é fornecido pelo sistema.

## <a name="build-configuration"></a>Configuração de construção

Os plug-ins Maven permitem personalizar as fases de construção do projeto. Por exemplo, a forma como o projeto é compilado ou como embalá-lo num ficheiro JAR. Em `pom.xml`, adicione o seguinte `</project>` texto diretamente acima da linha.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Esta secção é usada para adicionar plug-ins, recursos e outras opções de configuração de construção. Para obter uma `pom.xml` referência completa [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html)do ficheiro, consulte .

### <a name="add-plug-ins"></a>Adicionar plug-ins

* **Exec Maven Plugin**

    Para as topoologias apache storm implementadas em Java, o [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/) é útil porque permite-lhe executar facilmente a topologia localmente no seu ambiente de desenvolvimento. Adicione o `<plugins>` seguinte à `pom.xml` secção do ficheiro para incluir o plugin Exec Maven:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler Plugin**

    Outro plug-in útil é o [Plugin compilador Apache Maven,](https://maven.apache.org/plugins/maven-compiler-plugin/)que é usado para alterar opções de compilação. Altere a versão Java que Maven usa para a fonte e alvo para a sua aplicação.

  * Para HDInsight __3.4 ou anterior,__ defina a versão java de origem e de meta para __1.7__.

  * Para hDInsight __3.5,__ defina a versão Java de origem e de meta para __1.8__.

  Adicione o `<plugins>` seguinte texto na `pom.xml` secção do ficheiro para incluir o plugin do Compilador Apache Maven. Este exemplo especifica 1.8, pelo que a versão hDInsight alvo é de 3.5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Configurar recursos

A secção de recursos permite-lhe incluir recursos não codificados, tais como ficheiros de configuração necessários por componentes na topologia. Para este exemplo, adicione o `<resources>` seguinte `pom.xml` texto na secção do ficheiro. Em seguida, guarde e feche o ficheiro.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Este exemplo adiciona o diretório de recursos`${basedir}`na raiz do projeto como um `log4j2.xml`local que contém recursos, e inclui o ficheiro nomeado . Este ficheiro é utilizado para configurar que informações são registadas pela topologia.

## <a name="create-the-topology"></a>Criar a topologia

Uma topologia apache baseada em Java consiste em três componentes que deve ser autor (ou referência) como dependência.

* **Bicos**: Lê dados de fontes externas e emite fluxos de dados para a topologia.

* **Parafusos**: Executa o processamento em fluxos emitidos por bicos ou outros parafusos, e emite um ou mais fluxos.

* **Topologia**: Define como os bicos e parafusos são organizados, e fornece o ponto de entrada para a topologia.

### <a name="create-the-spout"></a>Criar o bico

Para reduzir os requisitos para a criação de fontes de dados externas, o bico que se segue simplesmente emite frases aleatórias. É uma versão modificada de um bico que é fornecido com os exemplos de [Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Embora esta topologia utilize apenas um bico, outros podem ter vários que alimentam dados de diferentes fontes para a topologia.

Introduza o comando abaixo para `RandomSentenceSpout.java`criar e abra um novo ficheiro:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Em seguida, copie e cole o código java abaixo no novo ficheiro.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Para um exemplo de um bico que lê a partir de uma fonte de dados externa, consulte um dos seguintes exemplos:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Um exemplo que lê no Twitter.
> * [Tempestade-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Um bico que se lê de Kafka.

### <a name="create-the-bolts"></a>Criar os parafusos

Os parafusos tratam do processamento de dados. Os parafusos podem fazer qualquer coisa, por exemplo, computação, persistência ou conversa ção com componentes externos. Esta topologia usa dois parafusos:

* **SplitSentence**: Divide as frases emitidas por **RandomSentenceSpout** em palavras individuais.

* **WordCount**: Conta quantas vezes cada palavra ocorreu.

#### <a name="splitsentence"></a>SplitSentence

Introduza o comando abaixo para `SplitSentence.java`criar e abra um novo ficheiro:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Em seguida, copie e cole o código java abaixo no novo ficheiro.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Introduza o comando abaixo para `WordCount.java`criar e abra um novo ficheiro:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Em seguida, copie e cole o código java abaixo no novo ficheiro.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Defina a topologia

A topologia une os bicos e parafusos num gráfico, que define como os dados fluem entre os componentes. Também fornece pistas de paralelismo que storm usa ao criar instâncias dos componentes dentro do cluster.

A imagem que se segue é um diagrama básico do gráfico dos componentes para esta topologia.

![diagrama mostrando os bicos e parafusos arranjo](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Para implementar a topologia, insira o comando `WordCountTopology.java`abaixo para criar e abrir um novo ficheiro:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Em seguida, copie e cole o código java abaixo no novo ficheiro.  Em seguida, feche o arquivo.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Configurar a exploração madeireira

A tempestade usa [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para registar informações. Se não configurar o registo, a topologia emite informações de diagnóstico. Para controlar o que está registado, crie um ficheiro nomeado `log4j2.xml` no `resources` diretório, introduzindo o comando abaixo:

```cmd
notepad resources\log4j2.xml
```

Em seguida, copie e cole o texto XML abaixo no novo ficheiro.  Em seguida, feche o arquivo.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Este XML configura um novo `com.microsoft.example` madeireiro para a classe, que inclui os componentes neste exemplo topologia. O nível está definido para rastrear este madeireiro, que captura qualquer informação de registo emitida por componentes nesta topologia.

A `<Root level="error">` secção configura o nível raiz `com.microsoft.example`do registo (tudo o que não está dentro) para apenas registar informações de erro.

Para obter mais informações sobre a configuração do registo para o Log4j 2, consulte [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Versão de tempestade 0.10.0 e uso mais alto Log4j 2.x. Versões mais antigas da tempestade usaram log4j 1.x, que usou um formato diferente para configuração de log. Para obter informações sobre [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)a configuração mais antiga, consulte .

## <a name="test-the-topology-locally"></a>Teste a topologia localmente

Depois de guardar os ficheiros, utilize o seguinte comando para testar a topologia localmente.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

À medida que funciona, a topologia exibe informações de arranque. O seguinte texto é um exemplo da saída da contagem de palavras:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Este registo de exemplo indica que a palavra 'e' foi emitida 113 vezes. A contagem continua a subir enquanto a topologia correr porque o bico emite continuamente as mesmas frases.

Há um intervalo de 5 segundos entre a emissão de palavras e as contagens. O componente **WordCount** está configurado apenas para emitir informações quando um tique-taque chegar. Pede que os tuples de carrapato só sejam entregues a cada cinco segundos.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia em Flux

[O Flux](https://storm.apache.org/releases/2.0.0/flux.html) é um novo quadro disponível com a Tempestade 0.10.0 e superior, que lhe permite separar a configuração da implementação. Os seus componentes ainda estão definidos em Java, mas a topologia é definida usando um ficheiro YAML. Pode embalar uma definição de topologia padrão com o seu projeto, ou usar um ficheiro autónomo ao submeter a topologia. Ao submeter a topologia à Tempestade, pode utilizar variáveis ambientais ou ficheiros de configuração para preencher valores na definição de topologia YAML.

O ficheiro YAML define os componentes a utilizar para a topologia e o fluxo de dados entre eles. Pode incluir um ficheiro YAML como parte do ficheiro do frasco ou pode utilizar um ficheiro YAML externo.

Para obter mais informações sobre o Flux, consulte a [estrutura do Fluxo .https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)

> [!WARNING]  
> Devido a um [bug (comhttps://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) a tempestade 1.0.1, você pode precisar de instalar um ambiente de desenvolvimento de [tempestade](https://storm.apache.org/releases/current/Setting-up-development-environment.html) para executar topoologias de Fluxo localmente.

1. Anteriormente, `WordCountTopology.java` definiu a topologia, mas não é necessário com o Flux. Eliminar o ficheiro com o seguinte comando:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Introduza o comando abaixo para `topology.yaml`criar e abra um novo ficheiro:

    ```cmd
    notepad resources\topology.yaml
    ```

    Em seguida, copie e cole o texto abaixo no novo ficheiro.  Em seguida, feche o arquivo.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Introduza o comando `pom.xml` abaixo para abrir para fazer as revisões descritas abaixo:

    ```cmd
    notepad pom.xml
    ```

   1. Adicione a seguinte nova `<dependencies>` dependência na secção:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Adicione o seguinte plugin à `<plugins>` secção. Este plugin trata da criação de um pacote (arquivo de frascos) para o projeto, e aplica algumas transformações específicas ao Flux ao criar o pacote.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
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
        ```

   1. Para a secção Exec Maven `<configuration>`  >  `<mainClass>` Plugin, navegue e mude `${storm.topology}` para `org.apache.storm.flux.Flux`. Esta definição permite ao Flux lidar com a gestão da topologia localmente em desenvolvimento.

   1. Na `<resources>` secção, adicione o `<includes>`seguinte a . Este XML inclui o ficheiro YAML que define a topologia como parte do projeto.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Teste a topologia do fluxo localmente

1. Introduza o seguinte comando para compilar e executar a topologia do Fluxo usando Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Se a sua topologia usar a Tempestade 1.0.1 bits, este comando falha. Esta falha é [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)causada por . Em vez disso, [instale storm no seu ambiente](https://storm.apache.org/releases/current/Setting-up-development-environment.html) de desenvolvimento e use os seguintes passos:
    >
    > Se instalou a Tempestade no seu ambiente de [desenvolvimento,](https://storm.apache.org/releases/current/Setting-up-development-environment.html)pode utilizar os seguintes comandos:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    O `--local` parâmetro executa a topologia em modo local no seu ambiente de desenvolvimento. O `-R /topology.yaml` parâmetro utiliza `topology.yaml` o recurso de ficheiro do ficheiro do frasco para definir a topologia.

    À medida que funciona, a topologia exibe informações de arranque. O seguinte texto é um exemplo da saída:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Há um atraso de 10 segundos entre lotes de informação registada.

2. Crie um novo yaml de topologia do projeto.

    1. Introduza o comando `topology.xml`abaixo para abrir:

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Encontre a secção seguinte e `10` `5`altere o valor de . Esta modificação altera o intervalo entre a emissão de lotes de palavras conta de 10 segundos para 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Guardar ficheiros como `newtopology.yaml`.

3. Para executar a topologia, insira o seguinte comando:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Ou, se tiver storm no seu ambiente de desenvolvimento:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Este comando `newtopology.yaml` usa a definição de topologia. Como não incluímos `compile` o parâmetro, Maven usa a versão do projeto construído em etapas anteriores.

    Uma vez iniciada a topologia, deve notar que o tempo entre `newtopology.yaml`os lotes emitidos mudou para refletir o valor em . Para que possa ver que pode alterar a sua configuração através de um ficheiro YAML sem ter de recompilar a topologia.

Para obter mais informações sobre estas e outras características da estrutura do Fluxo, consulte [o Flux .https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)

## <a name="trident"></a>Tridente

[Tridente](https://storm.apache.org/releases/current/Trident-API-Overview.html) é uma abstração de alto nível que é fornecida pela Tempestade. Apoia o processamento audais. A principal vantagem do Tridentél é que pode garantir que cada mensagem que entra na topologia seja processada apenas uma vez. Sem utilizar o Trident, a sua topologia só pode garantir que as mensagens são processadas pelo menos uma vez. Há também outras diferenças, como componentes incorporados que podem ser usados em vez de criar parafusos. Na verdade, os parafusos são substituídos por componentes menos genéricos, tais como filtros, projeções e funções.

As aplicações tridente podem ser criadas utilizando projetos Maven. Usa os mesmos passos básicos apresentados anteriormente neste artigo — só o código é diferente. O tridente também não pode (atualmente) ser usado com a estrutura do Fluxo.

Para mais informações sobre tridente, consulte a visão geral da [API tridente](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Passos Seguintes

Aprendeste a criar uma topologia da Tempestade Apache usando o Java. Agora aprenda a:

* [Implementar e gerir as topoologias da tempestade Apache no HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Desenvolva topoologias C# para Apache Storm no HDInsight usando o Estúdio Visual](apache-storm-develop-csharp-visual-studio-topology.md)

Você pode encontrar mais exemplos de topoologias apache storm visitando [as topoologias exemplo para Apache Storm em HDInsight](apache-storm-example-topology.md).

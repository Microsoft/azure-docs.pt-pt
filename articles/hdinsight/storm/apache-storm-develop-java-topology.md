---
title: Topologia de Java de exemplo do Apache Storm – Azure HDInsight
description: Saiba como criar topologias do Apache Storm no Java através da criação de uma topologia de contagem de palavras de exemplo.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: o Apache storm, o exemplo do apache storm, o java de storm, o exemplo de topologia do storm
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 2c1c144899189e2320d1388fca848fa3d7ec2257
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122085"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Criar uma topologia do Apache Storm no Java

Saiba como criar uma topologia baseada em Java para [Apache Storm](https://storm.apache.org/). Aqui, vai criar uma topologia do Storm que implementa um aplicativo de contagem de palavras. Utilizar [Apache Maven](https://maven.apache.org/) para compilar e empacotar o projeto. Em seguida, aprenderá como definir a topologia através da [Apache Storm fluxo](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) framework.

Depois de concluir os passos neste documento, pode implementar a topologia ao Apache Storm no HDInsight.

> [!NOTE]  
> Está disponível numa versão concluída dos exemplos de topologia Storm criada este documento [ https://github.com/Azure-Samples/hdinsight-java-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de desenvolvimento Java (JDK) versão 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) , de acordo com o Apache.  A maven é um projeto de criar o sistema de projetos de Java.

## <a name="test-environment"></a>Ambiente de teste
O ambiente utilizado para este artigo foi um computador com Windows 10.  Os comandos foram executados numa linha de comandos e os diversos arquivos foram editados com o bloco de notas.

Num prompt de comando, introduza os comandos abaixo para criar um ambiente de trabalho:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Crie um projeto Maven

Introduza o seguinte comando para criar um projeto Maven com o nome **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Este comando cria um diretório chamado `WordCount` na localização atual, que contém um projeto Maven básico. O segundo comando altera o diretório de trabalho atual para `WordCount`. O terceiro comando cria um novo diretório, `resources`, que será utilizado mais tarde.  O `WordCount` diretório contém os seguintes itens:

* `pom.xml`: Contém definições para o projeto Maven.
* `src\main\java\com\microsoft\example`: Contém o código da aplicação.
* `src\test\java\com\microsoft\example`: Contém testes para a sua aplicação.  

### <a name="remove-the-generated-example-code"></a>Remover o código de exemplo gerado

Eliminar os ficheiros de aplicação e de teste gerado `AppTest.java`, e `App.java` ao introduzir os comandos abaixo:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Adicionar repositórios do Maven

HDInsight baseia-se no Hortonworks Data Platform (HDP), pelo que recomendamos que utilize o repositório de Hortonworks para transferir as dependências para os seus projetos do Apache Storm.  

Abra `pom.xml` introduzindo o comando abaixo:

```cmd
notepad pom.xml
```

Em seguida, adicione o seguinte XML após o `<url> https://maven.apache.org</url>` linha:

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

Maven permite-lhe definir os valores de nível de projeto chamados propriedades. Na `pom.xml`, adicione o seguinte texto após o `</repositories>` linha:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Agora, pode utilizar este valor noutras secções o `pom.xml`. Por exemplo, quando especificar a versão dos componentes do Storm, pode utilizar `${storm.version}` em vez de disco rígido de um valor de codificação.

## <a name="add-dependencies"></a>Adicionar dependências

Adicione uma dependência de componentes do Storm. Na `pom.xml`, adicione o seguinte texto no `<dependencies>` secção:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

No momento da compilação Maven utiliza estas informações para procurar `storm-core` no repositório Maven. Ele procura pela primeira vez no repositório no seu computador local. Se os ficheiros não estão lá, o Maven transfere-os a partir do repositório público do Maven e armazena-os no repositório local.

> [!NOTE]  
> Tenha em atenção o `<scope>provided</scope>` linha nesta secção. Esta definição indica o Maven para excluir **storm núcleos** de todos os ficheiros JAR que são criados, porque ele é fornecido pelo sistema.

## <a name="build-configuration"></a>Criar configuração

Plug-ins do maven permitem-lhe personalizar os estágios de compilação do projeto. Por exemplo, como o projeto é compilado ou como empacotá-lo para um ficheiro JAR. Na `pom.xml`, adicione o seguinte texto diretamente acima da `</project>` linha.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Esta secção é usada para adicionar plug-ins, recursos e outras opções de configuração de compilação. Para obter uma referência completa da `pom.xml` de ficheiros, consulte [ https://maven.apache.org/pom.html ](https://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Adicionar plug-ins

* **Exec Plug-in do Maven**

    Para topologias do Apache Storm implementadas em Java, o [Plug-in Maven de Exec](https://www.mojohaus.org/exec-maven-plugin/) é útil porque ele permite que execute facilmente a topologia localmente no seu ambiente de desenvolvimento. Adicione o seguinte para o `<plugins>` secção do `pom.xml` arquivo para incluir o plug-in do Exec Maven:
    
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

* **Plug-in de compilador do Apache Maven**

    Outro plug-in é útil a [Plug-in de compilador do Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/), que é utilizado para alterar as opções de compilação. Altere a versão de Java que utiliza o Maven para a origem e destino para a sua aplicação.
    
  * Para o HDInsight __3.4 ou anterior__, defina a origem e destino versão de Java para __1.7__.
    
  * Para o HDInsight __3.5__, defina a origem e destino versão de Java para __1.8__.
    
    Adicione o seguinte texto no `<plugins>` secção do `pom.xml` arquivo para incluir o plug-in do compilador do Apache Maven. Este exemplo Especifica 1.8, portanto, a versão do HDInsight de destino é a 3.5.
    
    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
      <source>1.8</source>
      <target>1.8</target>
      </configuration>
    </plugin>
    ```

### <a name="configure-resources"></a>Configurar recursos

A seção de recursos permite-lhe incluir o código não-recursos, tais como ficheiros de configuração necessários para componentes na topologia. Neste exemplo, adicione o seguinte texto no `<resources>` secção do `pom.xml` ficheiro.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Este exemplo adiciona o diretório de recursos na raiz do projeto (`${basedir}`) como uma localização que contém recursos e inclui o arquivo chamado `log4j2.xml`. Este ficheiro é utilizado para configurar as informações que são registadas pela topologia.

## <a name="create-the-topology"></a>Criar a topologia

Uma topologia baseada em Java Apache Storm é composta por três componentes que tem de criar (ou referência) como uma dependência.

* **Spouts**: Leia os dados de fontes externas e emite fluxos de dados para a topologia.

* **Bolts**: Executa o processamento em fluxos emitidos por spouts ou outros bolts e emite um ou mais fluxos.

* **Topologia**: Define como o spouts e bolts são dispostas e fornece o ponto de entrada para a topologia.

### <a name="create-the-spout"></a>Criar o spout

Para reduzir os requisitos para configurar origens de dados externas, o seguinte spout simplesmente emite frases aleatórias. É uma versão modificada de um spout que é fornecido com o [exemplos de Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Embora esta topologia utiliza apenas um spout, outras pessoas podem ter vários esse feed de dados de origens diferentes para a topologia.

Introduza o comando abaixo para criar e abrir um novo ficheiro `RandomSentenceSpout.java`:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Em seguida, copie e cole o código de java abaixo para o novo ficheiro.  Em seguida, feche o ficheiro.

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
> Para obter um exemplo de um spout que lê a partir de uma origem de dados externas, consulte um dos exemplos a seguir:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Spout de exemplo que lê do Twitter.
> * [Storm Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Spout que lê a partir do Kafka.


### <a name="create-the-bolts"></a>Criar os práticos

Os Bolts lidar com o processamento de dados. Os Bolts podem fazer qualquer coisa, por exemplo, computação, persistência ou falar com componentes externos. Esta topologia utiliza dois bolts:

* **SplitSentence**: Divide as frases emitidas pelo **RandomSentenceSpout** em palavras individuais.

* **WordCount**: Conta o número de vezes que ocorreu cada palavra.


#### <a name="splitsentence"></a>SplitSentence

Introduza o comando abaixo para criar e abrir um novo ficheiro `SplitSentence.java`:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Em seguida, copie e cole o código de java abaixo para o novo ficheiro.  Em seguida, feche o ficheiro.

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

Introduza o comando abaixo para criar e abrir um novo ficheiro `WordCount.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Em seguida, copie e cole o código de java abaixo para o novo ficheiro.  Em seguida, feche o ficheiro.

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

### <a name="define-the-topology"></a>Definir a topologia

A topologia vincula os spouts e bolts juntos num gráfico, que define a forma como os dados fluem entre os componentes. Ele também fornece sugestões de paralelismo que utiliza o Storm durante a criação de instâncias dos componentes dentro do cluster.

A imagem seguinte é um diagrama básico do gráfico de componentes para esta topologia.

![diagrama que mostra a disposição de spouts e bolts](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Para implementar a topologia, introduza o comando abaixo para criar e abrir um novo ficheiro `WordCountTopology.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Em seguida, copie e cole o código de java abaixo para o novo ficheiro.  Em seguida, feche o ficheiro.

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

### <a name="configure-logging"></a>Configurar o registo

Utiliza o Storm [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para registar informações. Se não configurar o registo, a topologia emite informações de diagnóstico. Para controlar o que é registado, crie um ficheiro denominado `log4j2.xml` no `resources` diretório ao introduzir o comando abaixo:

```cmd
notepad resources\log4j2.xml
```

Em seguida, copie e cole o texto XML abaixo para o novo ficheiro.  Em seguida, feche o ficheiro.

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

Esse XML configura um novo logger para o `com.microsoft.example` classe, que inclui os componentes nesta topologia de exemplo. O nível está definido para o rastreio para esse agente, que captura quaisquer informações de registo emitidas pelos componentes nesta topologia.

O `<Root level="error">` secção configura o nível de raiz de registo (tudo o que não `com.microsoft.example`) para registar apenas informações de erro.

Para obter mais informações sobre como configurar o registo para Log4j 2, consulte [ https://logging.apache.org/log4j/2.x/manual/configuration.html ](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Versão 0.10.0 e a utilização superior Log4j do Storm 2.x. As versões mais antigas do storm utilizado Log4j 1.x, que é utilizado um formato diferente para a configuração de registo. Para obter informações sobre a configuração mais antiga, consulte [ https://wiki.apache.org/logging-log4j/Log4jXmlFormat ](https://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testar localmente a topologia

Depois de guardar os ficheiros, utilize o seguinte comando para testar localmente a topologia.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

À medida que é executado, a topologia apresenta informações de arranque. O texto seguinte é um exemplo do resultado de contagem do word:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Este registo de exemplo indica que a palavra "e" 113 vezes foi emitido. A contagem de continuar subir, desde que a topologia é executado uma vez que o spout emite continuamente as frases mesmo.

Existe um intervalo de 5 segundos entre emissões de palavras e contagens. O **WordCount** componente está configurado para emitir apenas informações quando chega uma tupla de sinalização. Ele solicita que escala tuplas sejam entregues somente a cada cinco segundos.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia de fluxo

[Fluxo](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) é uma nova estrutura disponível com o Storm 0.10.0 e superior, que permite que separe a configuração da implementação. Os componentes ainda são definidos em Java, mas a topologia é definida com um ficheiro YAML. Pode empacotar uma definição de topologia de padrão com seu projeto ou utilizar um ficheiro autónomo ao submeter a topologia. Ao submeter a topologia de Storm, pode utilizar variáveis de ambiente ou ficheiros de configuração para preencher valores na definição de topologia do YAML.

O ficheiro YAML define os componentes a utilizar para a topologia e os dados de fluxo entre eles. Pode incluir um ficheiro YAML como parte do ficheiro jar ou pode utilizar um ficheiro YAML externo.

Para obter mais informações sobre o fluxo, consulte [estrutura de fluxo (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html).

> [!WARNING]  
> Devido a um [bug (https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) com o Storm 1.0.1, poderá ter de instalar uma [ambiente de desenvolvimento do Storm](https://storm.apache.org/releases/current/Setting-up-development-environment.html) para executar localmente a topologias de fluxo.

1. Anteriormente, `WordCountTopology.java` definida a topologia, mas não é necessária com o fluxo. Elimine o ficheiro com o seguinte comando:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. Introduza o comando abaixo para criar e abrir um novo ficheiro `topology.yaml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    Em seguida, copie e cole o texto abaixo para o novo ficheiro.  Em seguida, feche o ficheiro.

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

3. Introduza o comando abaixo para abrir `pom.xml` para tornar as revisões descritas abaixo:

    ```cmd
    notepad pom.xml
    ```

   * Adicione a seguinte dependência de novo no `<dependencies>` secção:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   * Adicione o seguinte Plug-in para o `<plugins>` secção. Este plug-in processa a criação de um pacote (ficheiro. jar) para o projeto e aplica-se algumas transformações específicas ao fluxo ao criar o pacote.

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

   * Na **exec--Plug-in maven** `<configuration>` secção, altere o valor de `<mainClass>` partir `${storm.topology}` para `org.apache.storm.flux.Flux`. Esta definição permite que o fluxo processar a executar a topologia localmente em desenvolvimento.

   * Na `<resources>` secção, adicione o seguinte ao `<includes>`. Esse XML inclui o ficheiro YAML que define a topologia como parte do projeto.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testar localmente a topologia de fluxo

1. Introduza o seguinte comando para compilar e executar a topologia de fluxo com o Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Se a topologia usa o bits de Storm 1.0.1, este comando falha. Esse incumprimento seja causado por [ https://issues.apache.org/jira/browse/STORM-2055 ](https://issues.apache.org/jira/browse/STORM-2055). Em vez disso, [instalar o Storm no seu ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html) e utilize os seguintes passos:
    >
    > Se tiver [instalado o Storm no seu ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html), pode utilizar em vez disso, os seguintes comandos:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    O `--local` parâmetro executa a topologia no modo de local no seu ambiente de desenvolvimento. O `-R /topology.yaml` parâmetro utiliza a `topology.yaml` recursos de ficheiros do ficheiro jar para definir a topologia.

    À medida que é executado, a topologia apresenta informações de arranque. O texto seguinte é um exemplo da saída:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Existe um atraso de 10 segundos entre lotes de informações com sessão iniciada.

2. Crie um novo yaml de topologia do projeto.
 
    a. Introduza o comando abaixo para abrir `topology.xml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    b. Localize a seguinte secção e altera o valor de `10` para `5`. Essa modificação altera o intervalo entre lotes de emissão de contagens de word a partir de 10 segundos para 5.  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. Guardar ficheiro como `newtopology.yaml`.

3. Para executar a topologia, introduza o seguinte comando:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Em alternativa, se tiver o Storm no seu ambiente de desenvolvimento:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     Este comando utiliza o `newtopology.yaml` como a definição de topologia. Uma vez que não incluímos o `compile` parâmetro, Maven utiliza a versão do projeto criado nos passos anteriores.

    Assim que for iniciada a topologia, deve observar que o tempo entre lotes emitidos foi alterado para refletir o valor no `newtopology.yaml`. Pode ver o que é possível alterar a configuração através de um ficheiro YAML sem precisar recompilar a topologia.

Para obter mais informações sobre esses e outros recursos da estrutura de fluxo, consulte [fluxo (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) é uma abstração de alto nível que é fornecida pela Storm. Ele suporta processamento com monitoração de estado. A principal vantagem de Trident é que pode garantir que todas as mensagens que introduz a topologia é processada apenas uma vez. Sem utilizar Trident, a topologia apenas pode garantir que as mensagens são processadas pelo menos uma vez. Também existem outras diferenças, como componentes internos que podem ser utilizados em vez de criar os bolts. Na verdade, os bolts são substituídos pelos componentes de menos genérica, como filtros, projeções e funções.

Aplicativos de Trident podem ser criados usando os projetos do Maven. Utilize as mesmas etapas básicas, conforme apresentado anteriormente neste artigo — apenas o código é diferente. Trident também não é possível (atualmente) ser utilizada com a estrutura de fluxo.

Para obter mais informações sobre Trident, consulte a [descrição geral da API do Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Próximos Passos

Aprendeu como criar uma topologia do Apache Storm com Java. Agora é saber como:

* [Implementar e gerir topologias do Apache Storm no HDInsight](apache-storm-deploy-monitor-topology.md)

* [Desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Pode encontrar mais exemplo topologias do Apache Storm, visitando [topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md).

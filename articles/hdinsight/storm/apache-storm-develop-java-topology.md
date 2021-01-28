---
title: Apache Storm exemplo Java topologia - Azure HDInsight
description: Aprenda a criar topologias da Tempestade Apache em Java criando um exemplo de topologia de contagem de palavras.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020,devx-track-java
ms.date: 04/27/2020
ms.openlocfilehash: 620a4e1627b25af22db68173f35924376e26f5f8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929122"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Criar uma topologia da Tempestade Apache em Java

Aprenda a criar uma topologia baseada em Java para a Tempestade Apache. Cria-se uma topologia storm que implementa uma aplicação de contagem de palavras. Usas o Apache Maven para construir e embalar o projeto. Depois, aprende-se a definir a topologia usando a estrutura apache storm flux.

Depois de completar os passos neste documento, você pode colocar a topologia para Apache Storm em HDInsight.

> [!NOTE]  
> Uma versão completa dos exemplos de topologia storm criados neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) .

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado de](https://maven.apache.org/install.html) acordo com Apache.  Maven é um sistema de construção de projetos para projetos java.

## <a name="test-environment"></a>Ambiente de teste

O ambiente utilizado para este artigo era um computador que executava o Windows 10.  Os comandos foram executados num pedido de comando, e os vários ficheiros foram editados com Bloco de Notas.

A partir de um pedido de comando, insira os comandos abaixo para criar um ambiente de trabalho:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Criar um projeto do Maven

Insira o seguinte comando para criar um projeto Maven chamado **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Este comando cria um diretório nomeado `WordCount` na localização atual, que contém um projeto maven básico. O segundo comando altera o atual diretório de trabalho para `WordCount` . O terceiro comando cria um novo diretório, `resources` que será usado mais tarde.  O `WordCount` diretório contém os seguintes itens:

* `pom.xml`: Contém definições para o projeto Maven.
* `src\main\java\com\microsoft\example`: Contém o seu código de aplicação.
* `src\test\java\com\microsoft\example`: Contém testes para a sua aplicação.  

### <a name="remove-the-generated-example-code"></a>Remova o código de exemplo gerado

Elimine os ficheiros de teste e aplicação gerados `AppTest.java` e `App.java` introduzindo os comandos abaixo:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Adicionar repositórios maven

O HDInsight baseia-se na Plataforma de Dados da Hortonworks (HDP), pelo que recomendamos a utilização do repositório da Hortonworks para descarregar dependências para os seus projetos de Apache Storm.  

Abra `pom.xml` introduzindo o comando abaixo:

```cmd
notepad pom.xml
```

Em seguida, adicione o seguinte XML após a `<url>https://maven.apache.org</url>` linha:

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

A Maven permite-lhe definir valores de nível de projeto chamados propriedades. In `pom.xml` , adicione o seguinte texto após a `</repositories>` linha:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Agora pode utilizar este valor noutras secções do `pom.xml` . Por exemplo, ao especificar a versão dos componentes Storm, pode utilizar `${storm.version}` em vez de codificar um valor.

## <a name="add-dependencies"></a>Adicionar dependências

Adicione uma dependência para os componentes da tempestade. In `pom.xml` , adicione o seguinte texto na `<dependencies>` secção:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Na hora da compilação, Maven usa esta informação para procurar `storm-core` no repositório de Maven. Primeiro olha para o repositório no seu computador local. Se os ficheiros não estiverem lá, Maven os descarrega do repositório público de Maven e armazena-os no repositório local.

> [!NOTE]  
> Reparem `<scope>provided</scope>` na linha desta secção. Esta definição diz ao Maven para excluir **o núcleo de tempestade de** quaisquer ficheiros JAR que são criados, porque é fornecido pelo sistema.

## <a name="build-configuration"></a>Compilar a configuração

Os plug-ins maven permitem-lhe personalizar as fases de construção do projeto. Por exemplo, como o projeto é compilado ou como embalá-lo em um arquivo JAR. In `pom.xml` , adicione o seguinte texto diretamente acima da `</project>` linha.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Esta secção é usada para adicionar plug-ins, recursos e outras opções de configuração de construção. Para uma referência completa do `pom.xml` ficheiro, consulte [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) .

### <a name="add-plug-ins"></a>Adicionar plug-ins

* **Executivo Maven Plugin**

    Para as topologias da Tempestade Apache implementadas em Java, o [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/) é útil porque permite-lhe executar facilmente a topologia localmente no seu ambiente de desenvolvimento. Adicione o seguinte à `<plugins>` secção do ficheiro para incluir o `pom.xml` plugin Exec Maven:

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

    Outro plug-in útil é o [`Apache Maven Compiler Plugin`](https://maven.apache.org/plugins/maven-compiler-plugin/) , que é usado para alterar opções de compilação. Altere a versão Java que Maven usa para a origem e o alvo para a sua aplicação.

  * Para HDInsight __3.4 ou anterior__, defina a versão de origem e java alvo para __1.7__.

  * Para HDInsight __3.5__, defina a versão de origem e java alvo para __1.8__.

  Adicione o texto a seguir na `<plugins>` secção do ficheiro para incluir o `pom.xml` plugin Apache Maven Compiler. Este exemplo especifica 1.8, pelo que a versão target HDInsight é 3.5.

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

### <a name="configure-resources"></a>Configure recursos

A secção de recursos permite-lhe incluir recursos não codificantes, tais como ficheiros de configuração necessários por componentes na topologia. Para este exemplo, adicione o seguinte texto na `<resources>` secção do `pom.xml` ficheiro. Em seguida, guarde e feche o ficheiro.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Este exemplo adiciona o diretório de recursos na raiz do projeto `${basedir}` como um local que contém recursos, e inclui o ficheiro denominado `log4j2.xml` . Este ficheiro é usado para configurar que informações são registadas pela topologia.

## <a name="create-the-topology"></a>Criar a topologia

Uma topologia da Tempestade Apache baseada em Java consiste em três componentes que deve ser autor (ou referência) como uma dependência.

* **Bicos**: Lê dados de fontes externas e emite fluxos de dados para a topologia.

* **Parafusos**: O processamento em correntes emitidas por bicos ou outros parafusos e emite um ou mais fluxos.

* **Topologia**: Define como os bicos e parafusos são organizados, e fornece o ponto de entrada para a topologia.

### <a name="create-the-spout"></a>Criar o bico

Para reduzir os requisitos de configuração de fontes de dados externas, o bico seguinte simplesmente emite frases aleatórias. É uma versão modificada de um bico que é fornecido com os [exemplos de Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Embora esta topologia use um bico, outros podem ter vários que alimentam dados de diferentes fontes para a topologia`.`

Introduza o comando abaixo para criar e abrir um novo `RandomSentenceSpout.java` ficheiro:

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
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Um bico de exemplo que lê no Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Um bico que lê de Kafka.

### <a name="create-the-bolts"></a>Criar os parafusos

Os parafusos tratam o processamento de dados. Os parafusos podem fazer qualquer coisa, por exemplo, computação, persistência ou falar com componentes externos. Esta topologia usa dois parafusos:

* **SplitSentence**: Divide as frases emitidas pela **RandomSentenceSpout** em palavras individuais.

* **WordCount**: Conta quantas vezes cada palavra ocorreu.

#### <a name="splitsentence"></a>Frase dividida

Introduza o comando abaixo para criar e abrir um novo `SplitSentence.java` ficheiro:

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

Introduza o comando abaixo para criar e abrir um novo `WordCount.java` ficheiro:

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

### <a name="define-the-topology"></a>Definir a topologia

A topologia une os bicos e parafusos num gráfico. O gráfico define como os dados fluem entre os componentes. Também fornece dicas de paralelismo que a Tempestade usa ao criar instâncias dos componentes dentro do cluster.

A imagem a seguir é um diagrama básico do gráfico de componentes para esta topologia.

![diagrama mostrando o arranjo de bicos e parafusos](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Para implementar a topologia, insira o comando abaixo para criar e abrir um novo `WordCountTopology.java` ficheiro:

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

### <a name="configure-logging"></a>Confiria a exploração madeireira

A tempestade usa [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para registar informações. Se não configurar o registo, a topologia emite informações de diagnóstico. Para controlar o que está registado, crie um ficheiro nomeado `log4j2.xml` no `resources` diretório introduzindo o comando abaixo:

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

Este XML configura um novo madeirão para a `com.microsoft.example` classe, que inclui os componentes nesta topologia exemplo. O nível está definido para rastrear este madeireiro, que captura qualquer informação de registo emitido por componentes nesta topologia.

A `<Root level="error">` secção configura o nível de raiz da exploração madeireira (tudo o que não está em ) `com.microsoft.example` apenas para registar informações de erro.

Para obter mais informações sobre a configuração do registo registado no Log4j 2, consulte [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) .

> [!NOTE]  
> Versão de tempestade 0.10.0 e utilização mais alta Log4j 2.x. Versões mais antigas da tempestade usaram Log4j 1.x, que usou um formato diferente para a configuração do registo. Para obter informações sobre a configuração mais antiga, consulte [https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat](https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat) .

## <a name="test-the-topology-locally"></a>Teste a topologia localmente

Depois de guardar os ficheiros, utilize o seguinte comando para testar a topologia localmente.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

À medida que funciona, a topologia exibe informações de arranque. O texto a seguir é um exemplo da saída da contagem de palavras:

```output
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
```

Este registo de exemplo indica que a palavra "e" foi emitida 113 vezes. A contagem continua a aumentar enquanto a topologia correr. Este aumento deve-se ao facto de o bico emitir continuamente as mesmas frases.

Há um intervalo de 5 segundos entre a emissão de palavras e contagens. O componente **WordCount** é configurado apenas para emitir informações quando um tuple de carrapato chega. Pede que os tique-taques só sejam entregues a cada cinco segundos.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia em Fluxo

[O fluxo](https://storm.apache.org/releases/2.0.0/flux.html) é um novo quadro disponível com a Tempestade 0.10.0 ou superior. O fluxo permite separar a configuração da implementação. Os seus componentes ainda estão definidos em Java, mas a topologia é definida usando um ficheiro YAML. Pode embalar uma definição de topologia padrão com o seu projeto, ou usar um ficheiro autónomo ao submeter a topologia. Ao submeter a topologia à Tempestade, utilize variáveis ambientais ou ficheiros de configuração para preencher valores de definição de topologia YAML.

O ficheiro YAML define os componentes a utilizar para a topologia e o fluxo de dados entre eles. Pode incluir um ficheiro YAML como parte do ficheiro do frasco. Ou pode usar um ficheiro YAML externo.

Para obter mais informações sobre o Fluxo, consulte [o quadro do Fluxo (). https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html)

> [!WARNING]  
> Devido a um [bug (com https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) a Tempestade 1.0.1, poderá ser necessário instalar um ambiente de desenvolvimento de [tempestade](https://storm.apache.org/releases/current/Setting-up-development-environment.html) para executar as topologias do Flux localmente.

1. Anteriormente, `WordCountTopology.java` definiu a topologia, mas não é necessária com o Flux. Eliminar o ficheiro com o seguinte comando:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Introduza o comando abaixo para criar e abrir um novo `topology.yaml` ficheiro:

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

1. Introduza o comando abaixo para abrir `pom.xml` para fazer as revisões descritas abaixo:

    ```cmd
    notepad pom.xml
    ```

   1. Adicione a seguinte nova dependência na `<dependencies>` secção:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Adicione o seguinte plugin à `<plugins>` secção. Este plugin trata da criação de um pacote (ficheiro jar) para o projeto, e aplica algumas transformações específicas ao Flux ao criar o pacote.

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

   1. Para a secção Exec Maven Plugin, navegue `<configuration>`  >  `<mainClass>` para e mude `${storm.topology}` para `org.apache.storm.flux.Flux` . Esta definição permite ao Flux lidar com a topologia localmente em desenvolvimento.

   1. Na `<resources>` secção, adicione o seguinte a `<includes>` . Este XML inclui o ficheiro YAML que define a topologia como parte do projeto.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Teste a topologia do fluxo localmente

1. Introduza o seguinte comando para compilar e executar a topologia do Fluxo com recurso a Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Se a sua topologia usar a Tempestade 1.0.1 bits, este comando falha. Esta falha é causada [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) por. Em vez disso, [instale a Tempestade no seu ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html) e use os seguintes passos:
    >
    > Se [instalou a Storm no seu ambiente de desenvolvimento,](https://storm.apache.org/releases/current/Setting-up-development-environment.html)pode utilizar os seguintes comandos:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    O `--local` parâmetro executa a topologia em modo local no seu ambiente de desenvolvimento. O `-R /topology.yaml` parâmetro utiliza o recurso de ficheiro do ficheiro do frasco para definir a `topology.yaml` topologia.

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

    1. Introduza o comando abaixo para `topology.xml` abrir:

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Encontre a seguinte secção e altere o valor de `10` `5` . Esta modificação altera o intervalo entre a emissão de lotes de contagem de palavras de 10 segundos a 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Guarde o ficheiro como `newtopology.yaml` .

3. Para executar a topologia, insira o seguinte comando:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Ou, se tiveres a Tempestade no teu ambiente de desenvolvimento:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Este comando usa a `newtopology.yaml` definição de topologia. Como não incluímos o `compile` parâmetro, Maven usa a versão do projeto construído em passos anteriores.

    Uma vez iniciada a topologia, deve notar que o tempo entre os lotes emitidos mudou para refletir o valor em `newtopology.yaml` . Para que possa ver que pode alterar a sua configuração através de um ficheiro YAML sem ter de recompiler a topologia.

Para obter mais informações sobre estas e outras características da estrutura do Fluxo, consulte [o Flux ( . https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html)

## <a name="trident"></a>Tridente

[Tridente](https://storm.apache.org/releases/current/Trident-API-Overview.html) é uma abstração de alto nível que é fornecida pela Tempestade. Apoia o processamento estatal. A principal vantagem do Tridente é que garante que cada mensagem que entra na topologia é processada apenas uma vez. Sem usar o Trident, a sua topologia só pode garantir que as mensagens são processadas pelo menos uma vez. Há também outras diferenças, como componentes incorporados que podem ser usados em vez de criar parafusos. Os parafusos são substituídos por componentes menos genéricos, tais como filtros, projeções e funções.

As aplicações tridentes podem ser criadas utilizando projetos Maven. Usa os mesmos passos básicos que apresentados anteriormente neste artigo — apenas o código é diferente. Tridente também não pode (atualmente) ser usado com a estrutura do fluxo.

Para mais informações sobre tridente, consulte a Visão Geral da [API Trident.](https://storm.apache.org/releases/current/Trident-API-Overview.html)

## <a name="next-steps"></a>Passos Seguintes

Aprendeste a criar uma topologia da Tempestade Apache usando o Java. Agora aprenda a:

* [Implementar e gerir as topologias da Tempestade Apache em HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Desenvolver topologias com Python](apache-storm-develop-python-topology.md)

Você pode encontrar mais exemplo As topologias da Tempestade Apache visitando [Topologias exemplo para Apache Storm em HDInsight](apache-storm-example-topology.md).
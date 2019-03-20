---
title: 'Tutorial: Utilizar a API de fluxos do Apache Kafka - Azure HDInsight '
description: Saiba como utilizar a API Streams do Apache Kafka com o Kafka no HDInsight. Esta API permite-lhe executar o processamento de fluxos entre tópicos do Kafka.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: 3c40e00d55af49b1b040d3fe706f08af719b2238
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112794"
---
# <a name="tutorial-apache-kafka-streams-api"></a>Tutorial: API Apache Kafka streams

Saiba como criar uma aplicação que utiliza a API de fluxos do Apache Kafka e executá-lo com o Kafka no HDInsight. 

A aplicação utilizada neste tutorial é uma contagem de palavras de transmissão em fluxo. Lê os dados do texto de um tópico do Kafka, extrai palavras individuais e, em seguida, armazena as palavras e as contagens noutro tópico.

> [!NOTE]  
> Processamento de fluxo de Kafka, muitas vezes, é feito com o Apache Spark ou Apache Storm. A API Streams do Kafka foi introduza na versão do Kafka 0.10.0 (no HDInsight 3.5 e 3.6). Esta API permite-lhe transformar fluxos de dados entre tópicos de entrada e de saída. Em alguns casos, esta pode ser uma alternativa à criação de uma solução de transmissão em fluxo no Spark ou no Storm. 
>
> Para obter mais informações sobre as Transmissões em Fluxo do Kafka, veja a documentação [Intro to Streams](https://kafka.apache.org/10/documentation/streams/) (Introdução às Transmissões em Fluxo) em Apache.org.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o ambiente de desenvolvimento
> * Compreender o código
> * Criar e implementar a aplicação
> * Configurar tópicos do Kafka
> * Executar o código

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Kafka no HDInsight 3.6. Para saber como criar um Kafka num cluster do HDInsight, veja a [introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md) documento.

* Conclua os passos a [consumidor do Apache Kafka e API de produtor](apache-kafka-producer-consumer-api.md) documento. Os passos neste documento utilizam a aplicação de exemplo e os tópicos que criou neste tutorial.

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Tem de ter os seguintes componentes instalados no seu ambiente de desenvolvimento:

* [Java JDK 8](https://aka.ms/azure-jdks) ou equivalente, como OpenJDK.

* [Apache Maven](https://maven.apache.org/)

* Um cliente SSH e o comando `scp`. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

A aplicação de exemplo está localizada em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), no subdiretório `Streaming`. A aplicação é composta por dois ficheiros:

* `pom.xml`: Esse arquivo define as dependências do projeto, versão de Java e métodos de empacotamento.
* `Stream.java`: Esse arquivo implementa a lógica de transmissão em fluxo.

### <a name="pomxml"></a>Pom.xml

Seguem-se os aspetos importantes a compreender em relação ao ficheiro `pom.xml`:

* Dependências: Este projeto conta com a API de fluxos Kafka, que é fornecido pelo `kafka-clients` pacote. Esta dependência é definida pelo seguinte código XML:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]  
    > A entrada `${kafka.version}` é declarada na secção `<properties>..</properties>` de `pom.xml` e está configurada para a versão de Kafka do cluster do HDInsight.

* Plug-ins: Plug-ins do maven fornecem vários recursos. Neste projeto, são utilizados os seguintes plug-ins:

    * `maven-compiler-plugin`: Usado para definir a versão de Java utilizada pelo projeto para 8. O HDInsight 3.6 requer o Java 8.
    * `maven-shade-plugin`: Usado para gerar um jar uber que contém esta aplicação, bem como quaisquer dependências. Também é utilizado para definir o ponto de entrada da aplicação, para que possa executar diretamente o ficheiro JAR sem ter de especificar a classe principal.

### <a name="streamjava"></a>Stream.java

O ficheiro [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) utiliza a API Streams para implementar uma aplicação de contagem de palavras. Lê os dados de um tópico do Kafka, denominado `test`, e escreve as contagens de palavras num tópico com o nome `wordcounts`.

O código seguinte define a aplicação de contagem de palavras:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>Criar e implementar o exemplo

Para compilar e implementar o projeto no cluster do Kafka no HDInsight, siga os passos abaixo:

1. Transfira os exemplos partir de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Mude os diretórios para o diretório `Streaming` e utilize o seguinte comando para criar um pacote jar:

    ```bash
    mvn clean package
    ```

    Este comando cria o pacote em `target/kafka-streaming-1.0-SNAPSHOT.jar`.

3. Utilize o seguinte comando para copiar o ficheiro `kafka-streaming-1.0-SNAPSHOT.jar` para o cluster do HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Substitua `sshuser` pelo utilizador SSH do seu cluster e `clustername` pelo nome do seu cluster. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH. Para obter mais informações sobre como utilizar `scp` com o HDInsight, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-apache-kafka-topics"></a>Crie tópicos do Apache Kafka

1. Para abrir uma ligação de SSH ao cluster, utilize o seguinte comando:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` pelo utilizador SSH do seu cluster e `clustername` pelo nome do seu cluster. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH. Para obter mais informações sobre como utilizar `scp` com o HDInsight, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para guardar o nome do cluster numa variável e instalar um utilitário de análise JSON (`jq`), utilize os comandos mais abaixo. Quando lhe for pedido, introduza o nome do cluster de Kafka:

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. Para obter o mediador Kafka anfitriões e os anfitriões de Apache Zookeeper, utilize os seguintes comandos. Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster. É-lhe pedida a palavra-passe duas vezes.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. Para criar os tópicos que a operação de transmissão em fluxo vai utilizar, utilize os seguintes comandos:

    > [!NOTE]  
    > Poderá receber um erro que diz que o tópico `test` já existe. Esse erro não é problemático, porque o tópico pode já ter sido criado no tutorial da API Producer and Consumer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Os tópicos são utilizados para os seguintes fins:

   * `test`: Este tópico é onde os registos são recebidos. A aplicação de transmissão em fluxo lê a partir deste tópico.
   * `wordcounts`: Este tópico é onde a aplicação de transmissão em fluxo armazena sua saída.
   * `RekeyedIntermediateTopic`: Este tópico é utilizado para criar novas partições de dados à medida que a contagem é atualizada pelo `countByKey` operador.
   * `wordcount-example-Counts-changelog`: Este tópico é utilizado por um armazenamento de Estados a `countByKey` operação

     > [!IMPORTANT]  
     > O Kafka no HDInsight também pode ser configurado para criar tópicos automaticamente. Para obter mais informações, veja o documento [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Configurar a criação automática de tópicos).

## <a name="run-the-code"></a>Executar o código

1. Para iniciar a aplicação de transmissão em fluxo como um processo em segundo plano, utilize o seguinte comando:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]  
    > Poderá receber um aviso sobre o Apache log4j. Pode ignorá-lo.

2. Para enviar os registos para o tópico `test`, utilize o seguinte comando para iniciar a aplicação de produtor:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Após a conclusão do produtor, utilize o seguinte comando par ver as informações armazenada no tópico `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]  
    > Os parâmetros `--property` dizem ao consumidor da consola para imprimir a chave (palavra), juntamente com a contagem (valor). Este parâmetro também configura o desserializador que vai ser utilizado para ler esses valores a partir do Kafka.

    O resultado é semelhante ao seguinte texto:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]  
    > O parâmetro `--from-beginning` configura o consumidor para começar no início dos registos armazenados no tópico. A contagem aumenta sempre que for encontrada uma palavra, de modo que o tópico contém múltiplas entradas para cada palavra, com uma contagem progressiva.

7. Utilize __Ctrl + C__ para sair do produtor. Continue a utilizar __Ctrl + C__ para sair da aplicação e do consumidor.

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a utilizar a API de fluxos do Apache Kafka com o Kafka no HDInsight. Utilize o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Analisar registos do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters do Apache Kafka](apache-kafka-mirroring.md)

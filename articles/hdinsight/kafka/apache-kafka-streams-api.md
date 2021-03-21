---
title: 'Tutorial: Utilizar a API Streams do Apache Kafka - Azure HDInsight '
description: Tutorial - Saiba como usar a API Apache Kafka Streams com Kafka em HDInsight. Esta API permite-lhe executar o processamento de fluxos entre tópicos do Kafka.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/20/2020
ms.openlocfilehash: 5a1548cdf1d05a1f9d42f5c64b7fdc18f514518e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939204"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Tutorial: Use Apache Kafka streams API in Azure HDInsight

Saiba como criar uma aplicação que utilize a API Apache Kafka Streams e execute-a com a Kafka em HDInsight.

A aplicação utilizada neste tutorial é uma contagem de palavras de transmissão em fluxo. Lê os dados do texto de um tópico do Kafka, extrai palavras individuais e, em seguida, armazena as palavras e as contagens noutro tópico.

O processamento de fluxo de Kafka é frequentemente feito usando Apache Spark ou Apache Storm. A versão Kafka 1.1.0 (em HDInsight 3.5 e 3.6) introduziu a API kafka Streams. Esta API permite-lhe transformar fluxos de dados entre tópicos de entrada e de saída. Em alguns casos, esta pode ser uma alternativa à criação de uma solução de transmissão em fluxo no Spark ou no Storm.

Para obter mais informações sobre as Transmissões em Fluxo do Kafka, veja a documentação [Intro to Streams](https://kafka.apache.org/10/documentation/streams/) (Introdução às Transmissões em Fluxo) em Apache.org.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compreender o código
> * Criar e implementar a aplicação
> * Configurar tópicos do Kafka
> * Executar o código

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Kafka no HDInsight 3.6. Para aprender a criar um kafka no cluster HDInsight, consulte o Start with Apache Kafka no documento [HDInsight.](apache-kafka-get-started.md)

* Complete os passos no documento [Apache Kafka Consumer and Producer API.](apache-kafka-producer-consumer-api.md) Os passos neste documento utilizam a aplicação de exemplo e os tópicos que criou neste tutorial.

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) ou equivalente, como OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado de](https://maven.apache.org/install.html) acordo com Apache.  Maven é um sistema de construção de projetos para projetos java.

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

A aplicação de exemplo está localizada [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) em, no `Streaming` subdiretório. A aplicação é composta por dois ficheiros:

* `pom.xml`: este ficheiro define as dependências do projeto, a versão de Java e os métodos de empacotamento.
* `Stream.java`: este ficheiro implementa a lógica de transmissão em fluxo.

### <a name="pomxml"></a>Pom.xml

Seguem-se os aspetos importantes a compreender em relação ao ficheiro `pom.xml`:

* Dependências: este projeto depende das APIs de Streams do Kafka, que são disponibilizadas pelo pacote `kafka-clients`. Esta dependência é definida pelo seguinte código XML:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    A entrada `${kafka.version}` é declarada na secção `<properties>..</properties>` de `pom.xml` e está configurada para a versão de Kafka do cluster do HDInsight.

* Plug-ins: os plug-ins de Maven proporcionam diversas funcionalidades. Neste projeto, são utilizados os seguintes plug-ins:

    * `maven-compiler-plugin`: utilizado para definir a versão de Java utilizada pelo projeto para a versão 8. O HDInsight 3.6 requer o Java 8.
    * `maven-shade-plugin`: Usado para gerar um frasco uber que contém esta aplicação, e quaisquer dependências. Também é usado para definir o ponto de entrada da aplicação, para que você possa executar diretamente o ficheiro Jar sem ter que especificar a classe principal.

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

1. Desa estale o seu diretório atual para a localização do `hdinsight-kafka-java-get-started-master\Streaming` diretório e, em seguida, use o seguinte comando para criar um pacote de frascos:

    ```cmd
    mvn clean package
    ```

    Este comando cria o pacote em `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Substitua `sshuser` pelo utilizador SSH do seu cluster e `clustername` pelo nome do seu cluster. Utilize o seguinte comando para copiar o `kafka-streaming-1.0-SNAPSHOT.jar` ficheiro para o seu cluster HDInsight. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Criar tópicos Apache Kafka

1. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Abra uma ligação SSH ao cluster, introduzindo o seguinte comando. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Instale [o JQ](https://stedolan.github.io/jq/), um processador JSON de linha de comando. A partir da ligação SSH aberta, insira o seguinte comando para `jq` instalar:

    ```bash
    sudo apt -y install jq
    ```

3. Configurar a variável de senha. `PASSWORD`Substitua-se pela palavra-passe de login do cluster e, em seguida, introduza o comando:

    ```bash
    export password='PASSWORD'
    ```

4. Extrair o nome do cluster corretamente arquivado. O invólucro real do nome do cluster pode ser diferente do que se espera, dependendo da forma como o cluster foi criado. Este comando obterá o invólucro real e, em seguida, armazená-lo-á numa variável. Introduza o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Se você está fazendo este processo de fora do cluster, há um procedimento diferente para armazenar o nome do cluster. Obtenha o nome do cluster em minúsculas a partir do portal Azure. Em seguida, substitua o nome do cluster pelo `<clustername>` seguinte comando e execute-o: `export clusterName='<clustername>'` .  

5. Para obter os anfitriões corretores Kafka e os anfitriões do Apache Zookeeper, use os seguintes comandos. Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Estes comandos requerem acesso a Ambari. Se o seu cluster estiver por trás de um NSG, execute estes comandos a partir de uma máquina que pode aceder a Ambari.

6. Para criar os tópicos que a operação de transmissão em fluxo vai utilizar, utilize os seguintes comandos:

    > [!NOTE]  
    > Poderá receber um erro que diz que o tópico `test` já existe. Esse erro não é problemático, porque o tópico pode já ter sido criado no tutorial da API Producer and Consumer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Os tópicos são utilizados para os seguintes fins:

   * `test`: é neste tópico onde os registos são recebidos. A aplicação de transmissão em fluxo lê a partir deste tópico.
   * `wordcounts`: é neste tópico que a aplicação de transmissão em fluxo armazena a respetiva saída.
   * `RekeyedIntermediateTopic`: este tópico é utilizado para reparticionar os dados à medida que a contagem é atualizada pelo operador `countByKey`.
   * `wordcount-example-Counts-changelog`: este tópico é um arquivo de estado que a operação `countByKey` utiliza.

    O Kafka no HDInsight também pode ser configurado para criar tópicos automaticamente. Para obter mais informações, veja o documento [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Configurar a criação automática de tópicos).

## <a name="run-the-code"></a>Executar o código

1. Para iniciar a aplicação de transmissão em fluxo como um processo em segundo plano, utilize o seguinte comando:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Pode receber um aviso sobre Apache log4j. Pode ignorá-lo.

2. Para enviar os registos para o tópico `test`, utilize o seguinte comando para iniciar a aplicação de produtor:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Após a conclusão do produtor, utilize o seguinte comando par ver as informações armazenada no tópico `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    Os parâmetros `--property` dizem ao consumidor da consola para imprimir a chave (palavra), juntamente com a contagem (valor). Este parâmetro também configura o desserializador que vai ser utilizado para ler esses valores a partir do Kafka.

    O resultado é semelhante ao seguinte texto:

    ```output
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
    ```

    O parâmetro `--from-beginning` configura o consumidor para começar no início dos registos armazenados no tópico. A contagem aumenta sempre que for encontrada uma palavra, de modo que o tópico contém múltiplas entradas para cada palavra, com uma contagem progressiva.

4. Utilize __Ctrl + C__ para sair do produtor. Continue a utilizar __Ctrl + C__ para sair da aplicação e do consumidor.

5. Para eliminar os tópicos utilizados pela operação de streaming, utilize os seguintes comandos:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar a API Apache Kafka Streams com Kafka em HDInsight. Use o seguinte para saber mais sobre trabalhar com Kafka.

> [!div class="nextstepaction"]
> [Analisar registos do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
---
title: 'Tutorial: Utilizar o Apache Kafka produtor e consumidor APIs - Azure HDInsight '
description: Saiba como utilizar as APIs de Produtor e de Consumidor de Apache Kafka com o Kafka no HDInsight. Neste tutorial, irá aprender a utilizar estas APIs com o Kafka no HDInsight a partir de uma aplicação Java.
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 3dead1bdedb75a1b6fafb947da9c88094f0c4de9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724147"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Utilizar o Apache Kafka produtor e consumidor APIs

Saiba como utilizar as APIs de Produtor e de Consumidor de Apache Kafka com o Kafka no HDInsight.

A API de Produtor de Kafka permite que as aplicações enviem fluxos de dados para o cluster de Kafka. A API de Consumidor de Kafka permite que as aplicações leiam fluxos de dados a partir do cluster.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Pré-requisitos
> * Compreender o código
> * Criar e implementar a aplicação
> * Executar a aplicação no cluster

Para obter mais informações sobre as APIs, veja a documentação do Apache dedicada à [API de Produtor](https://kafka.apache.org/documentation/#producerapi) e à [API de Consumidor](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Pré-requisitos

* Apache Kafka no HDInsight 3.6. Para saber como criar um Kafka num cluster do HDInsight, veja [introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

* [Kit de desenvolvimento Java (JDK) versão 8](https://aka.ms/azure-jdks) ou equivalente, como OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) , de acordo com o Apache.  A maven é um projeto de criar o sistema de projetos de Java.

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

A aplicação de exemplo está localizada em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), no subdiretório `Producer-Consumer`. Essencialmente, a aplicação é composta por quatro ficheiros:

* `pom.xml`: Esse arquivo define as dependências do projeto, versão de Java e métodos de empacotamento.
* `Producer.java`: Este ficheiro envia frases aleatórias ao Kafka com o API de produtor.
* `Consumer.java`: Este ficheiro utiliza o API de consumidor para ler dados do Kafka e emiti-lo para STDOUT.
* `Run.java`: A interface de linha de comandos utilizada para executar o código de produtor e consumidor.

### <a name="pomxml"></a>Pom.xml

Seguem-se os aspetos importantes a compreender em relação ao ficheiro `pom.xml`:

* Dependências: Este projeto depende do produtor Kafka e consumidor APIs, que é fornecida pelo `kafka-clients` pacote. Esta dependência é definida pelo seguinte código XML:

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

    * `maven-compiler-plugin`: Usado para definir a versão de Java utilizada pelo projeto para 8. Esta é a versão de Java utilizada pelo HDInsight 3.6.
    * `maven-shade-plugin`: Usado para gerar um jar uber que contém esta aplicação, bem como quaisquer dependências. Também é utilizado para definir o ponto de entrada da aplicação, para que possa executar diretamente o ficheiro JAR sem ter de especificar a classe principal.

### <a name="producerjava"></a>Producer.Java

O produtor comunica com os anfitriões de mediador (nós de trabalho) de Kafka e envia os dados para um tópico do Kafka. O seguinte trecho de código é do [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) ficheiro a partir do [repositório do GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra como definir as propriedades de produtor:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.Java

O consumidor comunica com os anfitriões de mediador (nós de trabalho) de Kafka e lê os registos de forma cíclica. O seguinte fragmento de código do ficheiro [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) define as propriedades de consumidor:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

Neste código, o consumidor está configurado para ler a partir do início do tópico (`auto.offset.reset` está definido como `earliest`).

### <a name="runjava"></a>Run.Java

O [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) ficheiro fornece uma interface de linha de comandos que executa o código do produtor ou do consumidor. Tem de fornecer as informações do anfitrião de mediador de Kafka como um parâmetro. Opcionalmente, pode incluir um valor de ID de grupo, o que é utilizado pelo processo de consumidor. Se criar várias instâncias de consumidor com o mesmo ID de grupo, eles serão balanceamento de carga de leitura do tópico.

## <a name="build-and-deploy-the-example"></a>Criar e implementar o exemplo

1. Transferir e extrair os exemplos de [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Definir o seu diretório atual para a localização do `hdinsight-kafka-java-get-started\Producer-Consumer` diretório e utilize o seguinte comando:

    ```cmd
    mvn clean package
    ```

    Este comando cria um diretório com o nome `target`, que contém um ficheiro com o nome `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Introduza o seguinte comando para copiar o `kafka-producer-consumer-1.0-SNAPSHOT.jar` ficheiro ao seu cluster do HDInsight. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> Executar o exemplo

1. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Abra uma ligação SSH ao cluster, introduzindo o seguinte comando. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Instale [jq](https://stedolan.github.io/jq/), um processador JSON da linha de comandos. A partir da ligação de SSH aberta, introduzir o seguinte comando para instalar `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Configure variáveis de ambiente. Substitua `PASSWORD` e `CLUSTERNAME` com a palavra-passe de início de sessão do cluster e o cluster nome respectivamente, em seguida, introduza o comando:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Extrair nome do cluster corretamente com maiúsculas e minúsculas. A real letras maiúsculas e minúsculas no nome do cluster podem ser diferente do que o esperado, dependendo de como o cluster foi criado. Este comando irá obter as maiúsculas e minúsculas real, guarde-o numa variável e, em seguida, exibir o nome corretamente com maiúsculas e minúsculas e o nome que indicou anteriormente. Introduza o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Para obter o mediador Kafka anfitriões e os anfitriões de Apache Zookeeper, utilize o seguinte comando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

6. Criar o tópico do Kafka, `myTest`, introduzindo o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

7. Para executar o produtor e escrever dados para o tópico, utilize o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

8. Quando o produtor tiver terminado, utilize o seguinte comando para ler a partir do tópico:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    A leitura dos registos, juntamente com uma contagem de registos, é apresentada.

9. Utilize __Ctrl + C__ para sair do consumidor.

### <a name="multiple-consumers"></a>Vários consumidores

Os consumidores de Kafka utilizam um grupo de consumidores quando leem os registos. Utilizar o mesmo grupo com vários consumidores resulta em leituras com balanceamento de carga de um tópico. Cada consumidor no grupo recebe uma parte dos registos.

A aplicação de consumidor aceita um parâmetro que é utilizado como o ID de grupo. Por exemplo, o seguinte comando inicia um consumidor através de um ID de grupo de `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Utilize __Ctrl + C__ para sair do consumidor.

Para ver este processo em ação, utilize o seguinte comando:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Este comando utiliza `tmux` para dividir o terminal em duas colunas. É iniciado um consumidor em cada coluna, com o mesmo valor de ID de grupo. Após os consumidores concluírem a leitura, tenha em atenção que cada um deles lê apenas uma parte dos registos. Uso __Ctrl + C__ duas vezes para sair `tmux`.

O consumo pelos clientes dentro do mesmo grupo é processado pelas partições do tópico. Neste exemplo de tópico, o tópico `test` criado anteriormente, tem oito partições. Se iniciar oito consumidores, cada consumidor lê os registos de uma única partição do tópico.

> [!IMPORTANT]  
> Não podem existir mais instâncias de consumidor num grupo de consumidores do que partições. Neste exemplo, um grupo de consumidores pode incluir até oito consumidores, pois esse é o número de partições no tópico. Também pode ter vários grupos de consumidores, em que cada grupo não tem mais do que oito consumidores.

Os registos armazenados no Kafka são armazenados pela ordem em que são recebidos dentro de uma partição. Para obter uma entrega por ordem dos registos *dentro de uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponde ao número de partições. Para obter uma entrega por ordem dos registos *dentro do tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a utilizar o Apache Kafka produtor e a API de consumidor com o Kafka no HDInsight. Utilize o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Analisar registos do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters do Apache Kafka](apache-kafka-mirroring.md)
* [API com o HDInsight de fluxos do Apache Kafka](apache-kafka-streams-api.md)

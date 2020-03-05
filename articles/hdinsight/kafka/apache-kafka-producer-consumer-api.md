---
title: 'Tutorial: Apache Kafka Produtor e APIs de Consumo - Azure HDInsight'
description: Saiba como utilizar as APIs de Produtor e de Consumidor de Apache Kafka com o Kafka no HDInsight. Neste tutorial, irá aprender a utilizar estas APIs com o Kafka no HDInsight a partir de uma aplicação Java.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: 5a7d4d1917f65cd3d836db83600937a3e3d89de6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271884"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Utilizar as APIs de Produtor e de Consumidor de Apache Kafka

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

* Apache Kafka no cluster HDInsight. Para aprender a criar o cluster, consulte [Start with Apache Kafka no HDInsight](apache-kafka-get-started.md).
* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks) ou equivalente, como OpenJDK.
* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado](https://maven.apache.org/install.html) de acordo com Apache.  Maven é um sistema de construção de projetos para projetos Java.
* Um cliente sSH como o Putty. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

A aplicação de exemplo está localizada em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), no subdiretório `Producer-Consumer`. Se estiver a utilizar o cluster Kafka ativado pelo **Enterprise Security Package (ESP),** deverá utilizar a versão de aplicação localizada no subdiretório `DomainJoined-Producer-Consumer`.

Essencialmente, a aplicação é composta por quatro ficheiros:
* `pom.xml`: este ficheiro define as dependências do projeto, a versão de Java e os métodos de empacotamento.
* `Producer.java`: este ficheiro envia frases aleatórias para o Kafka através da API de produtor.
* `Consumer.java`: este ficheiro utiliza a API de consumidor para ler os dados a partir de Kafka e emiti-los para STDOUT.
* `AdminClientWrapper.java`: Este ficheiro utiliza a API de administração para criar, descrever e eliminar tópicos de Kafka.
* `Run.java`: a interface de linha de comandos utilizada para executar o código do produtor e do consumidor.

### <a name="pomxml"></a>Pom.xml

Seguem-se os aspetos importantes a compreender em relação ao ficheiro `pom.xml`:

* Dependências: este projeto depende das APIs de produtor e de consumidor de Kafka, fornecidas pelo pacote `kafka-clients`. Esta dependência é definida pelo seguinte código XML:

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

    * `maven-compiler-plugin`: utilizado para definir a versão de Java utilizada pelo projeto para a versão 8. Esta é a versão de Java utilizada pelo HDInsight 3.6.
    * `maven-shade-plugin`: utilizado para gerar um JAR com dependências, que contém precisamente não só esta aplicação como todas as dependências. Também é utilizado para definir o ponto de entrada da aplicação, para que possa executar diretamente o ficheiro JAR sem ter de especificar a classe principal.

### <a name="producerjava"></a>Producer.Java

O produtor comunica com os anfitriões de mediador (nós de trabalho) de Kafka e envia os dados para um tópico do Kafka. O seguinte código é do ficheiro [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) do [repositório GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra como definir as propriedades do produtor:

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

O ficheiro [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fornece uma interface de linha de comando que executa o produtor ou o código do consumidor. Tem de fornecer as informações do anfitrião de mediador de Kafka como um parâmetro. Pode incluir opcionalmente um valor de identificação de grupo, que é utilizado pelo processo de consumo. Se criar várias instâncias de consumo utilizando o mesmo ID de grupo, eles carregarão a leitura do equilíbrio a partir do tópico.

## <a name="build-and-deploy-the-example"></a>Criar e implementar o exemplo

Se você quiser saltar este passo, os frascos pré-construídos podem ser descarregados do subdiretório `Prebuilt-Jars`. Baixe o kafka-produtor-consumidor.jar. Se o seu cluster estiver ativado **pelo Enterprise Security Package (ESP),** utilize kafka-produtor-produtor-consumidor-esp.jar. Execute o passo 3 para copiar o jarro para o seu cluster HDInsight.

1. Descarregue e extraios os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Desloque o seu diretório atual para a localização do diretório `hdinsight-kafka-java-get-started\Producer-Consumer`. Se estiver a utilizar o pacote de **segurança empresarial (ESP)** ativado pelo cluster Kafka, deverá definir a localização para `DomainJoined-Producer-Consumer`subdiretório. Utilize o seguinte comando para construir a aplicação:

    ```cmd
    mvn clean package
    ```

    Este comando cria um diretório com o nome `target`, que contém um ficheiro com o nome `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Introduza o seguinte comando para copiar o ficheiro `kafka-producer-consumer-1.0-SNAPSHOT.jar` para o seu cluster HDInsight. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> Executar o exemplo

1. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Abra uma ligação SSH ao cluster, entrando no seguinte comando. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para obter os anfitriões do corretor Kafka, substitua os valores por `<clustername>` e `<password>` no comando seguinte e execute-os. Utilize o mesmo invólucro para `<clustername>` como mostrado no portal Azure. Substitua `<password>` com a senha de login do cluster e, em seguida, execute:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso ambari. Se o seu cluster estiver por trás de um NSG, execute este comando a partir de uma máquina que possa aceder a Ambari.

1. Crie o tópico kafka, `myTest`, entrando no seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Para executar o produtor e escrever dados para o tópico, utilize o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Quando o produtor tiver terminado, utilize o seguinte comando para ler a partir do tópico:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    A leitura dos registos, juntamente com uma contagem de registos, é apresentada.

1. Utilize __Ctrl + C__ para sair do consumidor.

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

Este comando utiliza `tmux` para dividir o terminal em duas colunas. É iniciado um consumidor em cada coluna, com o mesmo valor de ID de grupo. Após os consumidores concluírem a leitura, tenha em atenção que cada um deles lê apenas uma parte dos registos. Utilize __ctrl + C__ duas vezes para sair `tmux`.

O consumo pelos clientes dentro do mesmo grupo é processado pelas partições do tópico. Neste exemplo de tópico, o tópico `test` criado anteriormente, tem oito partições. Se iniciar oito consumidores, cada consumidor lê os registos de uma única partição do tópico.

> [!IMPORTANT]  
> Não podem existir mais instâncias de consumidor num grupo de consumidores do que partições. Neste exemplo, um grupo de consumidores pode incluir até oito consumidores, pois esse é o número de partições no tópico. Também pode ter vários grupos de consumidores, em que cada grupo não tem mais do que oito consumidores.

Os registos armazenados em Kafka são armazenados na ordem em que são recebidos dentro de uma divisória. Para obter uma entrega por ordem dos registos *dentro de uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponde ao número de partições. Para obter uma entrega por ordem dos registos *dentro do tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar o Apache Kafka Producer and Consumer API com kafka no HDInsight. Utilize o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Use Kafka REST Proxy](rest-proxy.md)
* [Analise os registos apache kafka](apache-kafka-log-analytics-operations-management.md)

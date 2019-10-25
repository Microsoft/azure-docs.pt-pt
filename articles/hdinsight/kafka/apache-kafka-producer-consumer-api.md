---
title: 'Tutorial: Utilizar as APIs de Produtor e de Consumidor do Apache Kafka - Azure HDInsight '
description: Saiba como utilizar as APIs de Produtor e de Consumidor de Apache Kafka com o Kafka no HDInsight. Neste tutorial, irá aprender a utilizar estas APIs com o Kafka no HDInsight a partir de uma aplicação Java.
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: 3ac68732042016c747b693e97bf8da15e1843b1e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817789"
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

* Apache Kafka no HDInsight 3,6. Para saber como criar um Kafka no cluster HDInsight, consulte [Iniciar com o Apache Kafka no HDInsight](apache-kafka-get-started.md).

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks) ou um equivalente, como OpenJDK.

* O [Apache Maven](https://maven.apache.org/download.cgi) foi [instalado](https://maven.apache.org/install.html) corretamente de acordo com o Apache.  O Maven é um sistema de compilação de projeto para projetos Java.

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

A aplicação de exemplo está localizada em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), no subdiretório `Producer-Consumer`. Essencialmente, a aplicação é composta por quatro ficheiros:

* `pom.xml`: este ficheiro define as dependências do projeto, a versão de Java e os métodos de empacotamento.
* `Producer.java`: este ficheiro envia frases aleatórias para o Kafka através da API de produtor.
* `Consumer.java`: este ficheiro utiliza a API de consumidor para ler os dados a partir de Kafka e emiti-los para STDOUT.
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

O produtor comunica com os anfitriões de mediador (nós de trabalho) de Kafka e envia os dados para um tópico do Kafka. O trecho de código a seguir é do arquivo [produtor. java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) do [repositório GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra como definir as propriedades do produtor:

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

O arquivo [Run. java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fornece uma interface de linha de comando que executa o produtor ou o código do consumidor. Tem de fornecer as informações do anfitrião de mediador de Kafka como um parâmetro. Opcionalmente, você pode incluir um valor de ID de grupo, que é usado pelo processo do consumidor. Se você criar várias instâncias de consumidor usando a mesma ID de grupo, elas balancearão a leitura do tópico.

## <a name="build-and-deploy-the-example"></a>Criar e implementar o exemplo

1. Baixe e extraia os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Defina o diretório atual como o local do diretório de `hdinsight-kafka-java-get-started\Producer-Consumer` e use o seguinte comando:

    ```cmd
    mvn clean package
    ```

    Este comando cria um diretório com o nome `target`, que contém um ficheiro com o nome `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Digite o seguinte comando para copiar o arquivo de `kafka-producer-consumer-1.0-SNAPSHOT.jar` para o cluster HDInsight. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> Executar o exemplo

1. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Abra uma conexão SSH para o cluster, inserindo o comando a seguir. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Instale o [JQ](https://stedolan.github.io/jq/), um processador de linha de comando JSON. Na conexão SSH aberta, digite o seguinte comando para instalar o `jq`:

    ```bash
    sudo apt -y install jq
    ```

1. Configure a variável de senha. Substitua `PASSWORD` pela senha de logon do cluster e, em seguida, digite o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Extraia corretamente o nome do cluster em maiúsculas. A capitalização real do nome do cluster pode ser diferente do esperado, dependendo de como o cluster foi criado. Esse comando obterá o capital real e, em seguida, o armazenará em uma variável. Introduza o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```
    > [!Note]  
    > Se você estiver fazendo esse processo de fora do cluster, haverá um procedimento diferente para armazenar o nome do cluster. Obtenha o nome do cluster em minúsculas no portal do Azure. Em seguida, substitua o nome do cluster por `<clustername>` no comando a seguir e execute-o: `export clusterName='<clustername>'`.  

1. Para obter os hosts do agente do Kafka, use o seguinte comando:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso Ambari. Se o cluster estiver atrás de um NSG, execute este comando em um computador que possa acessar o Ambari.

1. Crie o tópico Kafka, `myTest`, digitando o seguinte comando:

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

Este comando utiliza `tmux` para dividir o terminal em duas colunas. É iniciado um consumidor em cada coluna, com o mesmo valor de ID de grupo. Após os consumidores concluírem a leitura, tenha em atenção que cada um deles lê apenas uma parte dos registos. Use __Ctrl + C__ duas vezes para sair `tmux`.

O consumo pelos clientes dentro do mesmo grupo é processado pelas partições do tópico. Neste exemplo de tópico, o tópico `test` criado anteriormente, tem oito partições. Se iniciar oito consumidores, cada consumidor lê os registos de uma única partição do tópico.

> [!IMPORTANT]  
> Não podem existir mais instâncias de consumidor num grupo de consumidores do que partições. Neste exemplo, um grupo de consumidores pode incluir até oito consumidores, pois esse é o número de partições no tópico. Também pode ter vários grupos de consumidores, em que cada grupo não tem mais do que oito consumidores.

Os registros armazenados em Kafka são armazenados na ordem em que são recebidos em uma partição. Para obter uma entrega por ordem dos registos *dentro de uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponde ao número de partições. Para obter uma entrega por ordem dos registos *dentro do tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a usar o Apache Kafka produtor e a API do consumidor com o Kafka no HDInsight. Utilize o seguinte para obter mais informações sobre como trabalhar com o Kafka:

> [!div class="nextstepaction"]
> [Analisar logs de Apache Kafka](apache-kafka-log-analytics-operations-management.md)

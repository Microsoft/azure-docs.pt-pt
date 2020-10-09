---
title: 'Tutorial: Apache Kafka Producer & Consumer APIs - Azure HDInsight'
description: Saiba como utilizar as APIs de Produtor e de Consumidor de Apache Kafka com o Kafka no HDInsight. Neste tutorial, irá aprender a utilizar estas APIs com o Kafka no HDInsight a partir de uma aplicação Java.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 260a3fbb8486a1e9eeaa87e920143615e5fae867
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83681812"
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

* Apache Kafka no aglomerado HDInsight. Para aprender a criar o cluster, consulte [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).
* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks) ou equivalente, como OpenJDK.
* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado de](https://maven.apache.org/install.html) acordo com Apache.  Maven é um sistema de construção de projetos para projetos java.
* Um cliente da SSH como o Putty. Para obter mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Compreender o código

A aplicação de exemplo está localizada [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) em, no `Producer-Consumer` subdiretório. Se estiver a utilizar o pacote de segurança da **empresa (ESP)** ativado pelo cluster Kafka, deverá utilizar a versão de aplicação localizada na `DomainJoined-Producer-Consumer` subdirectória.

Essencialmente, a aplicação é composta por quatro ficheiros:
* `pom.xml`: este ficheiro define as dependências do projeto, a versão de Java e os métodos de empacotamento.
* `Producer.java`: este ficheiro envia frases aleatórias para o Kafka através da API de produtor.
* `Consumer.java`: este ficheiro utiliza a API de consumidor para ler os dados a partir de Kafka e emiti-los para STDOUT.
* `AdminClientWrapper.java`: Este ficheiro utiliza a API de administração para criar, descrever e eliminar tópicos Kafka.
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

O produtor comunica com os anfitriões de mediador (nós de trabalho) de Kafka e envia os dados para um tópico do Kafka. O seguinte corte de código é do ficheiro [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) do [repositório GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra como definir as propriedades do produtor. Para os agrupamentos habilitados para a segurança empresarial deve ser adicionada uma propriedade adicional "propriedades.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

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

O consumidor comunica com os anfitriões de mediador (nós de trabalho) de Kafka e lê os registos de forma cíclica. O seguinte código do ficheiro [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) define as propriedades do consumidor. Para os agrupamentos habilitados para a segurança empresarial deve ser adicionada uma propriedade adicional "propriedades.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

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

O ficheiro [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fornece uma interface de linha de comando que executa o código de produtor ou de consumo. Tem de fornecer as informações do anfitrião de mediador de Kafka como um parâmetro. Pode opcionalmente incluir um valor de ID de grupo, que é usado pelo processo de consumo. Se criar várias instâncias de consumo usando o mesmo ID do grupo, eles carregarão a leitura do equilíbrio a partir do tópico.

## <a name="build-and-deploy-the-example"></a>Criar e implementar o exemplo

### <a name="use-pre-built-jar-files"></a>Utilize ficheiros JAR pré-construídos

Descarregue os frascos da [amostra Kafka Get Started Azure.](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars) Se o seu cluster estiver ativado no **Pacote de Segurança Empresarial (ESP),** utilize kafka-produtor-consumidor-esp.jar. Utilize o comando abaixo para copiar os frascos para o seu aglomerado.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Construa os ficheiros JAR a partir do código

Se você quiser saltar este passo, os frascos pré-construídos podem ser descarregados a partir do `Prebuilt-Jars` subdiretório. Baixe o kafka-produtor-consumidor.jar. Se o seu cluster estiver ativado no **Pacote de Segurança Empresarial (ESP),** utilize kafka-produtor-consumidor-esp.jar. Execute o passo 3 para copiar o jarro para o seu cluster HDInsight.

1. Descarregue e extraia os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) .

2. Desa estale o seu diretório atual para a localização do `hdinsight-kafka-java-get-started\Producer-Consumer` diretório. Se estiver a utilizar o pacote de **segurança da empresa (ESP)** ativado pelo cluster Kafka, deverá definir o local para `DomainJoined-Producer-Consumer` subdiretoriidade. Utilize o seguinte comando para construir a aplicação:

    ```cmd
    mvn clean package
    ```

    Este comando cria um diretório com o nome `target`, que contém um ficheiro com o nome `kafka-producer-consumer-1.0-SNAPSHOT.jar`. Para os clusters ESP o ficheiro será `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Introduza o seguinte comando para copiar o `kafka-producer-consumer-1.0-SNAPSHOT.jar` ficheiro para o seu cluster HDInsight. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Executar o exemplo

1. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Abra uma ligação SSH ao cluster, introduzindo o seguinte comando. Se tal lhe for pedido, introduza a palavra-passe da conta de utilizador SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para obter os anfitriões do corretor Kafka, substitua os valores para `<clustername>` e no seguinte comando e `<password>` execute-o. Utilize o mesmo invólucro para `<clustername>` o que mostra o portal Azure. `<password>`Substitua-o pela palavra-passe de login do cluster e execute:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso a Ambari. Se o seu cluster estiver por trás de um NSG, executar este comando a partir de uma máquina que pode aceder a Ambari.

1. Criar o tópico `myTest` Kafka, introduzindo o seguinte comando:

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
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
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

Este comando utiliza `tmux` para dividir o terminal em duas colunas. É iniciado um consumidor em cada coluna, com o mesmo valor de ID de grupo. Após os consumidores concluírem a leitura, tenha em atenção que cada um deles lê apenas uma parte dos registos. Utilize __ctrl + C__ duas vezes para sair `tmux` .

O consumo pelos clientes dentro do mesmo grupo é processado pelas partições do tópico. Neste exemplo de tópico, o tópico `test` criado anteriormente, tem oito partições. Se iniciar oito consumidores, cada consumidor lê os registos de uma única partição do tópico.

> [!IMPORTANT]  
> Não podem existir mais instâncias de consumidor num grupo de consumidores do que partições. Neste exemplo, um grupo de consumidores pode incluir até oito consumidores, pois esse é o número de partições no tópico. Também pode ter vários grupos de consumidores, em que cada grupo não tem mais do que oito consumidores.

Os registos armazenados em Kafka são armazenados na ordem em que são recebidos dentro de uma divisória. Para obter uma entrega por ordem dos registos *dentro de uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponde ao número de partições. Para obter uma entrega por ordem dos registos *dentro do tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="common-issues-faced"></a>Questões Comuns enfrentadas

1. **Criação de tópicos falha** Se o seu cluster estiver ativado no Pack de Segurança Empresarial, utilize os [ficheiros JAR pré-construídos para produtor e consumidor.](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar) O frasco ESP pode ser construído a partir do código na [ `DomainJoined-Producer-Consumer` subdiretória](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer). Note que o produtor e as propriedades do consumidor procuram um imóvel adicional `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` para clusters habilitados pela ESP.

2. **Problema de enfrentar com clusters habilitados a ESP** Se as operações de produção e consumo falharem e estiver a utilizar um cluster ativado por ESP, verifique se o utilizador `kafka` está presente em todas as políticas ranger. Se não estiver presente, adicione-o a todas as políticas dos Rangers.

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar a Apache Kafka Producer and Consumer API com a Kafka na HDInsight. Utilize o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Use Kafka REST Proxy](rest-proxy.md)
* [Analisar registos do Apache Kafka](apache-kafka-log-analytics-operations-management.md)

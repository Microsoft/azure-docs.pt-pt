---
title: Usar Apache Kafka no HDInsight com o Hub IoT do Azure
description: Saiba como usar Apache Kafka no HDInsight com o Hub IoT do Azure. O projeto do Hub IoT do Kafka Connect do Azure fornece um conector de origem e de coletor para Kafka. O conector de origem pode ler dados do Hub IoT e o conector do coletor grava no Hub IoT.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 884d10ce1bc5e6b710c849d0be1cb9165caac4c5
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706098"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Usar Apache Kafka no HDInsight com o Hub IoT do Azure

Saiba como usar o conector do [Hub IOT do azure Apache Kafka Connect](https://github.com/Azure/toketi-kafka-connect-iothub) para mover dados entre Apache Kafka no HDInsight e no Hub IOT do Azure. Neste documento, você aprende a executar o conector do Hub IoT de um nó de borda no cluster.

A API do Kafka Connect permite que você implemente conectores que extraem dados continuamente no Kafka ou enviem dados de Kafka para outro sistema. A [Apache Kafka conectar o Hub IOT do Azure](https://github.com/Azure/toketi-kafka-connect-iothub) é um conector que efetua pull de dados do Hub IOT do Azure para o Kafka. Ele também pode enviar dados do Kafka para o Hub IoT.

Ao extrair do Hub IoT, você usa um conector de __origem__ . Ao enviar para o Hub IoT, você usa um conector de __coletor__ . O conector do Hub IoT fornece os conectores de origem e de coletor.

O diagrama a seguir mostra o fluxo de dados entre o Hub IoT do Azure e o Kafka no HDInsight ao usar o conector.

![Imagem mostrando dados que fluem do Hub IoT para Kafka por meio do conector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Para obter mais informações sobre a API de conexão, consulte [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Kafka no HDInsight. Para obter mais informações, veja o documento [Início rápido do Kafka no HDInsight](apache-kafka-get-started.md).

* Um nó de borda no cluster Kafka. Para obter mais informações, consulte o documento [usar nós de borda com o HDInsight](../hdinsight-apps-use-edge-node.md) .

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Um hub IoT do Azure e um dispositivo. Para este artigo, considere usar [conectar simulador online do Raspberry Pi ao Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* [Ferramenta de criação escalar](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Criar o conector

1. Baixe a origem do conector de [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) para seu ambiente local.

2. Em um prompt de comando, navegue até o diretório `toketi-kafka-connect-iothub-master`. Em seguida, use o seguinte comando para compilar e empacotar o projeto:

    ```cmd
    sbt assembly
    ```

    A compilação levará alguns minutos para ser concluída. O comando cria um arquivo chamado `kafka-connect-iothub-assembly_2.11-0.7.0.jar` no diretório `toketi-kafka-connect-iothub-master\target\scala-2.11` para o projeto.

## <a name="install-the-connector"></a>Instalar o conector

1. Carregue o arquivo. jar no nó de borda do seu Kafka no cluster HDInsight. Edite o comando a seguir, substituindo `CLUSTERNAME` pelo nome real do cluster. Os valores padrão para a conta de usuário SSH e o nome do [nó de borda](../hdinsight-apps-use-edge-node.md#access-an-edge-node) são usados abaixo, modifique conforme necessário.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Depois que a cópia de arquivo for concluída, conecte-se ao nó de borda usando SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para instalar o conector no diretório Kafka `libs`, use o seguinte comando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Mantenha a conexão SSH ativa para as etapas restantes.

## <a name="configure-apache-kafka"></a>Configurar Apache Kafka

Em sua conexão SSH com o nó de borda, use as seguintes etapas para configurar o Kafka para executar o conector no modo autônomo:

1. Configure a variável de senha. Substitua senha pela senha de logon do cluster e, em seguida, digite o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Instale o utilitário [JQ](https://stedolan.github.io/jq/) . JQ torna mais fácil processar documentos JSON retornados de consultas Ambari. Introduza o seguinte comando:

    ```bash
    sudo apt -y install jq
    ```

1. Obtenha o endereço dos agentes Kafka. Pode haver muitos agentes no cluster, mas você só precisa fazer referência a um ou dois. Para obter o endereço de dois hosts de agente, use o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Copie os valores para uso posterior. O valor devolvido é semelhante ao seguinte texto:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Obtenha o endereço dos nós do Apache Zookeeper. Há vários nós Zookeeper no cluster, mas você só precisa fazer referência a um ou dois. Use o seguinte comando para armazenar os endereços na variável `KAFKAZKHOSTS`:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Ao executar o conector no modo autônomo, o arquivo de `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` é usado para se comunicar com os agentes Kafka. Para editar o arquivo de `connect-standalone.properties`, use o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Faça as seguintes edições:

    |Valor atual |Novo valor | Comentário |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Substitua o valor `localhost:9092` pelos hosts do agente da etapa anterior|Define a configuração autônoma para o nó de borda para localizar os agentes Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Essa alteração permite que você teste usando o produtor do console incluído com Kafka. Você pode precisar de conversores diferentes para outros produtores e consumidores. Para obter informações sobre como usar outros valores de conversor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|O mesmo que acima.|
    |N/A|`consumer.max.poll.records=10`|Adicionar ao final do arquivo. Essa alteração é para evitar tempos limite no conector do coletor limitando-o a 10 registros por vez. Para mais informações, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Para salvar o arquivo, use __Ctrl + X__, __Y__e, em seguida, __Enter__.

1. Para criar os tópicos usados pelo conector, use os seguintes comandos:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Para verificar se os tópicos de `iotin` e `iotout` existem, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    O tópico `iotin` é usado para receber mensagens do Hub IoT. O tópico `iotout` é usado para enviar mensagens ao Hub IoT.

## <a name="get-iot-hub-connection-information"></a>Obter informações de conexão do Hub IoT

Para recuperar as informações do Hub IoT usadas pelo conector, use as seguintes etapas:

1. Obtenha o ponto de extremidade compatível com o Hub de eventos e o nome do ponto de extremidade compatível com o Hub de eventos para o Hub IoT. Para obter essas informações, use um dos seguintes métodos:

   * __No [portal do Azure](https://portal.azure.com/)__ , use as seguintes etapas:

     1. Navegue até o Hub IoT e selecione __pontos de extremidade__.
     2. Em __pontos de extremidade internos__, selecione __eventos__.
     3. Em __Propriedades__, copie o valor dos seguintes campos:

         * __Nome compatível com o Hub de eventos__
         * __Ponto de extremidade compatível com o Hub de eventos__
         * __Partições__

        > [!IMPORTANT]  
        > O valor do ponto de extremidade do portal pode conter texto extra que não é necessário neste exemplo. Extraia o texto que corresponde a este padrão `sb://<randomnamespace>.servicebus.windows.net/`.

   * __No [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ , use o seguinte comando:

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Substitua `myhubname` pelo nome do Hub IoT. A resposta é semelhante ao seguinte texto:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Obtenha a __chave__e a __política de acesso compartilhado__ . Para este exemplo, use a chave de __serviço__ . Para obter essas informações, use um dos seguintes métodos:

    * __No [portal do Azure](https://portal.azure.com/)__ , use as seguintes etapas:

        1. Selecione __políticas de acesso compartilhado__e, em seguida, selecione __serviço__.
        2. Copie o valor da __chave primária__ .
        3. Copie o valor da __cadeia de conexão--chave primária__ .

    * __No [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ , use o seguinte comando:

        1. Para obter o valor da chave primária, use o seguinte comando:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Substitua `myhubname` pelo nome do Hub IoT. A resposta é a chave primária para a política de `service` para esse Hub.

        2. Para obter a cadeia de conexão para a política de `service`, use o seguinte comando:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Substitua `myhubname` pelo nome do Hub IoT. A resposta é a cadeia de conexão para a política de `service`.

## <a name="configure-the-source-connection"></a>Configurar a conexão de origem

Para configurar a origem para trabalhar com o Hub IoT, execute as seguintes ações de uma conexão SSH para o nó de borda:

1. Crie uma cópia do arquivo de `connect-iot-source.properties` no diretório `/usr/hdp/current/kafka-broker/config/`. Para baixar o arquivo do projeto toketi-Kafka-Connect-iothub, use o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Para editar o arquivo de `connect-iot-source.properties` e adicionar as informações do Hub IoT, use o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. No editor, localize e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotin`. As mensagens recebidas do Hub IoT são colocadas no tópico `iotin`.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Substitua `PLACEHOLDER` pelo nome compatível com o Hub de eventos.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Substitua `PLACEHOLDER` pelo ponto de extremidade compatível com o Hub de eventos.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Substitua `PLACEHOLDER` por `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Substitua `PLACEHOLDER` pela chave primária da política de `service`.|
    |`IotHub.Partitions=PLACEHOLDER`|Substitua `PLACEHOLDER` pelo número de partições das etapas anteriores.|
    |`IotHub.StartTime=PLACEHOLDER`|Substitua `PLACEHOLDER` por uma data UTC. Essa data é quando o conector inicia a verificação de mensagens. O formato de data é `yyyy-mm-ddThh:mm:ssZ`.|
    |`BatchSize=100`|Substitua `100` por `5`. Essa alteração faz com que o conector Leia mensagens em Kafka quando houver cinco novas mensagens no Hub IoT.|

    Para obter um exemplo de configuração, consulte [conector de origem do Kafka Connect para o Hub IOT do Azure](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Para salvar as alterações, use __Ctrl + X__, __Y__e, em seguida, __Enter__.

Para obter mais informações sobre como configurar a origem do conector, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Configurar a conexão do coletor

Para configurar a conexão do coletor para funcionar com o Hub IoT, execute as seguintes ações de uma conexão SSH para o nó de borda:

1. Crie uma cópia do arquivo de `connect-iothub-sink.properties` no diretório `/usr/hdp/current/kafka-broker/config/`. Para baixar o arquivo do projeto toketi-Kafka-Connect-iothub, use o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Para editar o arquivo de `connect-iothub-sink.properties` e adicionar as informações do Hub IoT, use o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. No editor, localize e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`topics=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotout`. As mensagens gravadas no tópico `iotout` são encaminhadas para o Hub IoT.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Substitua `PLACEHOLDER` pela cadeia de conexão da política de `service`.|

    Para obter um exemplo de configuração, consulte [conector do coletor do Kafka Connect para o Hub IOT do Azure](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Para salvar as alterações, use __Ctrl + X__, __Y__e, em seguida, __Enter__.

Para obter mais informações sobre como configurar o coletor do conector, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Iniciar o conector de origem

1. Para iniciar o conector de origem, use o seguinte comando de uma conexão SSH para o nó de borda:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Depois que o conector for iniciado, envie mensagens para o Hub IoT de seus dispositivos. Como o conector lê mensagens do Hub IoT e as armazena no tópico Kafka, ele registra as informações no console:

    ```text
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Você pode ver vários avisos conforme o conector é iniciado. Esses avisos não causam problemas com o recebimento de mensagens do Hub IoT.

1. Pare o conector após alguns minutos usando **Ctrl + C** duas vezes. Levará alguns minutos para que o conector pare.

## <a name="start-the-sink-connector"></a>Iniciar o conector do coletor

De uma conexão SSH para o nó de borda, use o seguinte comando para iniciar o conector do coletor no modo autônomo:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

À medida que o conector é executado, informações semelhantes ao seguinte texto são exibidas:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Você pode notar vários avisos conforme o conector é iniciado. Pode ignorar com segurança estes avisos.

## <a name="send-messages"></a>Enviar mensagens

Para enviar mensagens por meio do conector, use as seguintes etapas:

1. Abra *uma segunda* sessão SSH para o cluster Kafka:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Obtenha o endereço dos agentes Kafka para a nova sessão SSH. Substitua senha pela senha de logon do cluster e, em seguida, digite o comando:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Para enviar mensagens para o tópico `iotout`, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Este comando não retorna para o prompt de bash normal. Em vez disso, ele envia entrada de teclado para o tópico `iotout`.

1. Para enviar uma mensagem para o seu dispositivo, Cole um documento JSON na sessão SSH para o `kafka-console-producer`.

    > [!IMPORTANT]  
    > Você deve definir o valor da entrada `"deviceId"` para a ID do seu dispositivo. No exemplo a seguir, o dispositivo é nomeado `myDeviceId`:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    O esquema para este documento JSON é descrito em mais detalhes em [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Se você estiver usando o dispositivo Simulated Raspberry Pi e ele estiver em execução, a seguinte mensagem será registrada pelo dispositivo:

    ```text
    Receive message: Turn On
    ```

    Reenvie o documento JSON, mas altere o valor da entrada `"message"`. O novo valor é registrado pelo dispositivo.

Para obter mais informações sobre como usar o conector do coletor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a usar a API do Apache Kafka Connect para iniciar o conector do IoT Kafka no HDInsight. Use os links a seguir para descobrir outras maneiras de trabalhar com o Kafka:

* [Usar Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usar Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

---
title: Use Apache Kafka em HDInsight com Azure IoT Hub
description: Aprenda a usar Apache Kafka em HDInsight com Azure IoT Hub. O projeto Kafka Connect Azure IoT Hub fornece um conector de origem e pia para Kafka. O conector de origem pode ler dados do IoT Hub, e o conector da pia escreve para o IoT Hub.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: b4f390e6d362895d58be0e8695e72d058a021b34
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485550"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Use Apache Kafka em HDInsight com Azure IoT Hub

Saiba como utilizar o conector [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) para mover dados entre Apache Kafka em HDInsight e Azure IoT Hub. Neste documento, aprende-se a executar o conector IoT Hub a partir de um nó de borda no cluster.

A API Kafka Connect permite-lhe implementar conectores que puxam continuamente dados para Kafka ou empurram dados de Kafka para outro sistema. O [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) é um conector que retira dados do Azure IoT Hub para Kafka. Também pode empurrar dados de Kafka para o IoT Hub.

Ao puxar do hub IoT, utilize um conector __de origem.__ Ao empurrar para o IoT Hub, utilize um conector __de pia.__ O conector IoT Hub fornece conectores de origem e pia.

O diagrama seguinte mostra o fluxo de dados entre Azure IoT Hub e Kafka em HDInsight ao utilizar o conector.

![Imagem mostrando dados que fluem do Hub IoT para Kafka através do conector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Para obter mais informações sobre a API connect, consulte [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) .

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Kafka em HDInsight. Para obter mais informações, veja o documento [Início rápido do Kafka no HDInsight](apache-kafka-get-started.md).

* Um nó de borda no aglomerado de Kafka. Para obter mais informações, consulte os nós de borda de utilização com o documento [HDInsight.](../hdinsight-apps-use-edge-node.md)

* Um cliente SSH. Para obter mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Um hub E dispositivo Azure IoT. Para este artigo, considere utilizar o [simulador online Connect Raspberry Pi para o Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* [Ferramenta de construção de Scala.](https://www.scala-sbt.org/)

## <a name="build-the-connector"></a>Construa o conector

1. Faça o download da fonte do conector [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) para o seu ambiente local.

2. De um pedido de comando, navegue para o `toketi-kafka-connect-iothub-master` diretório. Em seguida, use o seguinte comando para construir e embalar o projeto:

    ```cmd
    sbt assembly
    ```

    A construção levará alguns minutos para ser concluída. O comando cria um ficheiro nomeado `kafka-connect-iothub-assembly_2.11-0.7.0.jar` no `toketi-kafka-connect-iothub-master\target\scala-2.11` diretório do projeto.

## <a name="install-the-connector"></a>Instale o conector

1. Faça o upload do ficheiro .jar para o nó de borda do seu Kafka no cluster HDInsight. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome real do seu cluster. Os valores predefinidos para a conta de utilizador SSH e o nome do nó de [borda](../hdinsight-apps-use-edge-node.md#access-an-edge-node) são utilizados abaixo, modificam conforme necessário.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Assim que a cópia do ficheiro estiver concluída, ligue-se ao nó de borda utilizando sSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para instalar o conector no `libs` diretório kafka, utilize o seguinte comando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Mantenha a sua ligação SSH ativa para os restantes passos.

## <a name="configure-apache-kafka"></a>Configurar Apache Kafka

Desde a sua ligação SSH até ao nó de borda, utilize os seguintes passos para configurar a Kafka para executar o conector no modo autónomo:

1. Configurar a variável de senha. Substitua a PASSWORD pela palavra-passe de login do cluster e, em seguida, insira o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Instale o utilitário [jq.](https://stedolan.github.io/jq/) jq facilita o processo de documentos JSON devolvidos de consultas de Ambari. Introduza o seguinte comando:

    ```bash
    sudo apt -y install jq
    ```

1. Pegue o endereço dos corretores Kafka. Pode haver muitos corretores no seu cluster, mas só precisa de fazer referência a um ou dois. Para obter o endereço de dois anfitriões corretores, utilize o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Copie os valores para utilização posterior. O valor devolvido é semelhante ao seguinte texto:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Pegue o endereço dos nós apache zookeeper. Existem vários nós zookeeper no aglomerado, mas você só precisa referenciar um ou dois. Utilize o seguinte comando para armazenar os endereços na `KAFKAZKHOSTS` variável:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Ao executar o conector em modo autónomo, o `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` ficheiro é utilizado para comunicar com os corretores Kafka. Para editar o `connect-standalone.properties` ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Faça as seguintes edições:

    |Valor atual |Valor novo | Comentário |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Substitua o `localhost:9092` valor pelos anfitriões do corretor do passo anterior|Configura a configuração autónoma para o nó de borda encontrar os corretores Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Esta alteração permite-lhe testar utilizando o produtor de consola incluído com Kafka. Pode precisar de diferentes conversores para outros produtores e consumidores. Para obter informações sobre a utilização de outros valores de conversor, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|O mesmo que acima.|
    |N/D|`consumer.max.poll.records=10`|Adicione ao fim do ficheiro. Esta alteração é para evitar intervalos de tempo no conector da pia, limitando-o a 10 registos de cada vez. Para obter mais informações, veja [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Para guardar o ficheiro, utilize __Ctrl + X,__ __Y,__ e, em seguida, __Insira__.

1. Para criar os tópicos utilizados pelo conector, utilize os seguintes comandos:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Para verificar se `iotin` `iotout` existem tópicos e tópicos, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    O `iotin` tópico é usado para receber mensagens do IoT Hub. O `iotout` tópico é usado para enviar mensagens para o IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Obtenha informações de conexão IoT Hub

Para recuperar as informações do hub IoT utilizadas pelo conector, utilize os seguintes passos:

1. Obtenha o ponto final compatível com o Event Hub e o nome de ponto final compatível com o Event Hub para o seu hub IoT. Para obter esta informação, utilize um dos seguintes métodos:

   * __A partir do [portal Azure,](https://portal.azure.com/)__ utilize os seguintes passos:

     1. Navegue para o seu Hub IoT e __selecione Endpoints__.
     2. A partir __de pontos finais incorporados,__ selecione __Eventos__.
     3. A partir de __Propriedades,__ copie o valor dos seguintes campos:

         * __Nome compatível com Hub de Evento__
         * __Ponto final compatível com o Centro de Eventos__
         * __Partições__

        > [!IMPORTANT]  
        > O valor do ponto final do portal pode conter texto extra que não é necessário neste exemplo. Extrair o texto que corresponde a este `sb://<randomnamespace>.servicebus.windows.net/` padrão.

   * __A partir do [Azure CLI,](/cli/azure/get-started-with-azure-cli)__ utilize o seguinte comando:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       `myhubname`Substitua-o pelo nome do seu hub IoT. A resposta é semelhante ao seguinte texto:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Obtenha a __política de acesso compartilhado__ e __a chave__. Para este exemplo, utilize a chave __de serviço.__ Para obter esta informação, utilize um dos seguintes métodos:

    * __A partir do [portal Azure,](https://portal.azure.com/)__ utilize os seguintes passos:

        1. Selecione __políticas de acesso partilhado__e, em seguida, selecione o __serviço__.
        2. Copie o valor __da chave primária.__
        3. Copie o valor da chave de __ligação- principal.__

    * __A partir do [Azure CLI,](/cli/azure/get-started-with-azure-cli)__ utilize o seguinte comando:

        1. Para obter o valor da chave primária, utilize o seguinte comando:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            `myhubname`Substitua-o pelo nome do seu hub IoT. A resposta é a chave principal para a `service` política deste centro.

        2. Para obter o fio de ligação para a `service` apólice, utilize o seguinte comando:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            `myhubname`Substitua-o pelo nome do seu hub IoT. A resposta é a cadeia de ligação para a `service` apólice.

## <a name="configure-the-source-connection"></a>Configure a ligação de origem

Para configurar a fonte para trabalhar com o seu Hub IoT, execute as seguintes ações a partir de uma ligação SSH ao nó de borda:

1. Crie uma cópia do `connect-iot-source.properties` ficheiro no `/usr/hdp/current/kafka-broker/config/` diretório. Para descarregar o ficheiro do projeto toketi-kafka-connect-iothub, utilize o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Para editar o `connect-iot-source.properties` ficheiro e adicionar as informações do hub IoT, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. No editor, encontre e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotin`. As mensagens recebidas do hub IoT são colocadas no `iotin` tópico.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|`PLACEHOLDER`Substitua-o pelo nome compatível com o Event Hub.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|`PLACEHOLDER`Substitua-a pelo ponto de final compatível com o Centro de Eventos.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Substitua `PLACEHOLDER` por `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|`PLACEHOLDER`Substitua-a pela chave principal da `service` apólice.|
    |`IotHub.Partitions=PLACEHOLDER`|`PLACEHOLDER`Substitua-as pelo número de divisórias dos passos anteriores.|
    |`IotHub.StartTime=PLACEHOLDER`|Substitua `PLACEHOLDER` por uma data UTC. Esta data é quando o conector começa a verificar se há mensagens. O formato da data é `yyyy-mm-ddThh:mm:ssZ` .|
    |`BatchSize=100`|Substitua `100` por `5`. Esta alteração faz com que o conector leia mensagens em Kafka uma vez que há cinco novas mensagens no hub IoT.|

    Para obter uma configuração de exemplo, consulte [o Conector de Fonte de Ligação Kafka para O Hub IoT de Azure](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Para guardar alterações, utilize __Ctrl + X,__ __Y__, e, em seguida, __Insira__.

Para obter mais informações sobre a configuração da fonte do conector, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) .

## <a name="configure-the-sink-connection"></a>Configure a ligação da pia

Para configurar a ligação da pia para trabalhar com o seu Hub IoT, execute as seguintes ações a partir de uma ligação SSH ao nó de borda:

1. Crie uma cópia do `connect-iothub-sink.properties` ficheiro no `/usr/hdp/current/kafka-broker/config/` diretório. Para descarregar o ficheiro do projeto toketi-kafka-connect-iothub, utilize o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Para editar o `connect-iothub-sink.properties` ficheiro e adicionar as informações do hub IoT, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. No editor, encontre e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`topics=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotout`. As mensagens escritas para `iotout` o tópico são reencaminhadas para o hub IoT.|
    |`IotHub.ConnectionString=PLACEHOLDER`|`PLACEHOLDER`Substitua-a pela cadeia de ligação para a `service` apólice.|

    Para obter uma configuração de exemplo, consulte [o Conector de Pia Kafka Connect para Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Para guardar alterações, utilize __Ctrl + X,__ __Y__, e, em seguida, __Insira__.

Para obter mais informações sobre a configuração da pia do conector, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="start-the-source-connector"></a>Inicie o conector de origem

1. Para iniciar o conector de origem, utilize o seguinte comando a partir de uma ligação SSH ao nó de borda:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Assim que o conector começar, envie mensagens para o hub IoT a partir do(s) do seu dispositivo. À medida que o conector lê mensagens do hub IoT e as armazena no tópico Kafka, regista informações para a consola:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Pode ver vários avisos à medida que o conector começa. Estes avisos não causam problemas ao receber mensagens do hub IoT.

1. Pare o conector após alguns minutos utilizando **ctrl + C** duas vezes. Levará alguns minutos para o conector parar.

## <a name="start-the-sink-connector"></a>Inicie o conector da pia

A partir de uma ligação SSH ao nó de borda, utilize o seguinte comando para iniciar o conector da pia no modo autónomo:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

À medida que o conector é executado, são apresentadas informações semelhantes ao seguinte texto:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Pode notar vários avisos à medida que o conector começa. Pode ignorar com segurança estes avisos.

## <a name="send-messages"></a>Enviar mensagens

Para enviar mensagens através do conector, utilize os seguintes passos:

1. Abra *uma segunda* sessão de SSH para o cluster Kafka:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Obtenha o endereço dos corretores Kafka para a nova sessão de ssh. Substitua a PASSWORD pela palavra-passe de login do cluster e, em seguida, insira o comando:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Para enviar mensagens para o `iotout` tópico, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Este comando não o devolve ao pedido normal de Bash. Em vez disso, envia a entrada do teclado para o `iotout` tópico.

1. Para enviar uma mensagem para o seu dispositivo, cole um documento JSON na sessão SSH para o `kafka-console-producer` .

    > [!IMPORTANT]  
    > Deve definir o valor da `"deviceId"` entrada na identificação do seu dispositivo. No exemplo seguinte, o dispositivo é `myDeviceId` nomeado:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    O esquema deste documento JSON é descrito mais detalhadamente em [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Se estiver a utilizar o dispositivo Raspberry Pi simulado e estiver a funcionar, a seguinte mensagem é registada pelo dispositivo:

    ```output
    Receive message: Turn On
    ```

    Reencaenda o documento JSON, mas altere o valor da `"message"` entrada. O novo valor é registado pelo dispositivo.

Para obter mais informações sobre a utilização do conector da pia, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar a API Apache Kafka Connect para iniciar o IoT Kafka Connector em HDInsight. Use os seguintes links para descobrir outras formas de trabalhar com Kafka:

* [Use Apache Spark com Apache Kafka em HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Use a Tempestade Apache com Apache Kafka em HDInsight](../hdinsight-apache-storm-with-kafka.md)

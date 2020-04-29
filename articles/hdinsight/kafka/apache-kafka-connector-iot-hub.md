---
title: Use Apache Kafka no HDInsight com Hub Azure IoT
description: Aprenda a usar Apache Kafka no HDInsight com o Hub Azure IoT. O projeto Kafka Connect Azure IoT Hub fornece uma fonte e um conector de pia para Kafka. O conector de origem pode ler dados do IoT Hub, e o conector da pia escreve para o IoT Hub.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 48a72b5ba3819712b9e1d2536ae2dd3a06eaf3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80238824"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Use Apache Kafka no HDInsight com Hub Azure IoT

Aprenda a utilizar o conector [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) para mover dados entre Apache Kafka no HDInsight e Azure IoT Hub. Neste documento, aprende-se a executar o conector IoT Hub a partir de um nó de borda no cluster.

A API Kafka Connect permite-lhe implementar conectores que puxam continuamente dados para Kafka, ou empurre dados de Kafka para outro sistema. O [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) é um conector que retira dados do Hub Azure IoT para Kafka. Também pode empurrar dados de Kafka para o IoT Hub.

Ao retirar do Centro IoT, utilize um conector __de origem.__ Ao empurrar para o Hub IoT, utilize um conector de __pia.__ O conector IoT Hub fornece conectores de origem e pia.

O diagrama seguinte mostra o fluxo de dados entre o Hub Azure IoT e o Kafka no HDInsight ao utilizar o conector.

![Imagem mostrando dados que fluem do IoT Hub para Kafka através do conector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Para mais informações sobre a [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)API connect, consulte .

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Kafka no HDInsight. Para obter mais informações, veja o documento [Início rápido do Kafka no HDInsight](apache-kafka-get-started.md).

* Um nó de borda no aglomerado de Kafka. Para mais informações, consulte os nós de borda de utilização com o documento [HDInsight.](../hdinsight-apps-use-edge-node.md)

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Um Hub Azure IoT e um dispositivo. Para este artigo, considere usar o [simulador online Connect Raspberry Pi para o Hub Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* Ferramenta de [construção Scala.](https://www.scala-sbt.org/)

## <a name="build-the-connector"></a>Construa o conector

1. Descarregue a fonte [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) do conector para o seu ambiente local.

2. De um pedido de `toketi-kafka-connect-iothub-master` comando, navegue para o diretório. Em seguida, use o seguinte comando para construir e embalar o projeto:

    ```cmd
    sbt assembly
    ```

    A construção levará alguns minutos para ser concluída. O comando cria `kafka-connect-iothub-assembly_2.11-0.7.0.jar` um `toketi-kafka-connect-iothub-master\target\scala-2.11` ficheiro nomeado no diretório para o projeto.

## <a name="install-the-connector"></a>Instale o conector

1. Faça upload do ficheiro .jar para o nó de borda do seu Kafka no cluster HDInsight. Edite o comando `CLUSTERNAME` abaixo substituindo pelo nome real do seu cluster. Os valores predefinidos para a conta de utilizador SSH e o nome do [nó de borda](../hdinsight-apps-use-edge-node.md#access-an-edge-node) são utilizados abaixo, modificando-os conforme necessário.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Assim que a cópia do ficheiro estiver concluída, ligue-se ao nó de borda utilizando o SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para instalar o conector no diretório kafka, `libs` utilize o seguinte comando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Mantenha a ligação SSH ativa durante os passos restantes.

## <a name="configure-apache-kafka"></a>Configure Apache Kafka

Da ligação SSH ao nó de borda, utilize os seguintes passos para configurar kafka para executar o conector em modo autónomo:

1. Configurar a variável de senha. Substitua a PASSWORD pela senha de login do cluster e introduza o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Instale o utilitário [jq.](https://stedolan.github.io/jq/) jq torna mais fácil processar documentos JSON devolvidos de consultas Ambari. Introduza o seguinte comando:

    ```bash
    sudo apt -y install jq
    ```

1. Pegue o endereço dos corretores Kafka. Pode haver muitos corretores no seu cluster, mas só precisa de referência de um ou dois. Para obter o endereço de dois anfitriões corretores, use o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Copie os valores para posterior utilização. O valor devolvido é semelhante ao seguinte texto:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Pegue o endereço dos narizes do Zookeeper Apache. Há vários nós zookeeper no aglomerado, mas você só precisa fazer referência a um ou dois. Utilize o seguinte comando para a loja `KAFKAZKHOSTS`os endereços na variável:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Ao executar o conector `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` em modo autónomo, o ficheiro é utilizado para comunicar com os corretores Kafka. Para editar `connect-standalone.properties` o ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Faça as seguintes edições:

    |Valor atual |Valor novo | Comentário |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Substitua `localhost:9092` o valor com os anfitriões corretores do passo anterior|Configura a configuração autónoma para o nó de borda para encontrar os corretores Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Esta alteração permite-lhe testar usando o produtor de consola incluído com Kafka. Pode precisar de conversores diferentes para outros produtores e consumidores. Para obter informações sobre [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)a utilização de outros valores de conversor, consulte .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|O mesmo que acima.|
    |N/D|`consumer.max.poll.records=10`|Adicione ao fim do ficheiro. Esta alteração é para evitar intervalos no conector da pia limitando-o a 10 registos de cada vez. Para mais informações, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Para guardar o ficheiro, utilize __Ctrl + X,__ __Y,__ e depois __Enter__.

1. Para criar os tópicos utilizados pelo conector, utilize os seguintes comandos:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Para verificar `iotin` se `iotout` existem os tópicos e tópicos, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    O `iotin` tópico é usado para receber mensagens do IoT Hub. O `iotout` tópico é usado para enviar mensagens para o IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Obtenha informações de ligação ioT hub

Para recuperar as informações do hub IoT utilizadas pelo conector, utilize os seguintes passos:

1. Obtenha o ponto final compatível com o Event Hub e o nome final compatível com o Event Hub para o seu hub IoT. Para obter esta informação, utilize um dos seguintes métodos:

   * __A partir do [portal Azure,](https://portal.azure.com/)__ utilize os seguintes passos:

     1. Navegue para o seu Hub IoT e selecione __Pontos Finais__.
     2. A partir de __pontos finais incorporados,__ selecione __Eventos__.
     3. A partir de __Propriedades,__ copie o valor dos seguintes campos:

         * __Nome compatível com hub de evento__
         * __Ponto final compatível com o Hub de Eventos__
         * __Partições__

        > [!IMPORTANT]  
        > O valor final do portal pode conter texto extra que não é necessário neste exemplo. Extrair o texto que `sb://<randomnamespace>.servicebus.windows.net/`corresponde a este padrão .

   * __A partir do [Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ utilize o seguinte comando:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Substitua-o `myhubname` pelo nome do seu hub ioT. A resposta é semelhante ao seguinte texto:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Obtenha a política de __acesso partilhado__ e __a chave.__ Para este exemplo, utilize a chave de __serviço.__ Para obter esta informação, utilize um dos seguintes métodos:

    * __A partir do [portal Azure,](https://portal.azure.com/)__ utilize os seguintes passos:

        1. Selecione as políticas de __acesso partilhados__e, em seguida, selecione __o serviço__.
        2. Copie o __valor-chave primário.__
        3. Copiar o __valor-chave da ligação- principal.__

    * __A partir do [Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ utilize o seguinte comando:

        1. Para obter o valor-chave primário, utilize o seguinte comando:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Substitua-o `myhubname` pelo nome do seu hub ioT. A resposta é a chave `service` principal da política para este centro.

        2. Para obter a cadeia `service` de ligação para a apólice, utilize o seguinte comando:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Substitua-o `myhubname` pelo nome do seu hub ioT. A resposta é a `service` corda de ligação para a apólice.

## <a name="configure-the-source-connection"></a>Configure a ligação de origem

Para configurar a fonte para trabalhar com o seu Hub IoT, execute as seguintes ações a partir de uma ligação SSH ao nó de borda:

1. Crie uma `connect-iot-source.properties` cópia do `/usr/hdp/current/kafka-broker/config/` ficheiro no diretório. Para descarregar o ficheiro do projeto toketi-kafka-connect-iothub, utilize o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Para editar `connect-iot-source.properties` o ficheiro e adicionar as informações do hub IoT, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. No editor, encontre e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotin`. As mensagens recebidas do centro `iotin` ioT são colocadas no tópico.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Substitua-a `PLACEHOLDER` com o nome compatível com o Event Hub.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Substitua-a `PLACEHOLDER` com o ponto final compatível com o Event Hub.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Substitua `PLACEHOLDER` por `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Substitua-a `PLACEHOLDER` com a `service` chave principal da apólice.|
    |`IotHub.Partitions=PLACEHOLDER`|Substitua-a `PLACEHOLDER` pelo número de divisórias dos passos anteriores.|
    |`IotHub.StartTime=PLACEHOLDER`|Substitua-a `PLACEHOLDER` por uma data UTC. Esta data é quando o conector começa a verificar se há mensagens. O formato `yyyy-mm-ddThh:mm:ssZ`da data é .|
    |`BatchSize=100`|Substitua `100` por `5`. Esta mudança faz com que o conector leia mensagens em Kafka uma vez que há cinco novas mensagens no centro ioT.|

    Para uma configuração de exemplo, consulte [o Ligador fonte de ligação kafka para o Hub Azure IoT](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Para evitar alterações, utilize __Ctrl + X,__ __Y,__ e depois __Enter__.

Para obter mais informações sobre a [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)configuração da fonte do conector, consulte .

## <a name="configure-the-sink-connection"></a>Configure a ligação do lavatório

Para configurar a ligação do lavatório para trabalhar com o seu Hub IoT, execute as seguintes ações a partir de uma ligação SSH ao nó de borda:

1. Crie uma `connect-iothub-sink.properties` cópia do `/usr/hdp/current/kafka-broker/config/` ficheiro no diretório. Para descarregar o ficheiro do projeto toketi-kafka-connect-iothub, utilize o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Para editar `connect-iothub-sink.properties` o ficheiro e adicionar as informações do hub IoT, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. No editor, encontre e altere as seguintes entradas:

    |Valor atual |Editar|
    |---|---|
    |`topics=PLACEHOLDER`|Substitua `PLACEHOLDER` por `iotout`. As mensagens `iotout` escritas para o tópico são reencaminhadas para o centro ioT.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Substitua-a `PLACEHOLDER` com `service` a corda de ligação para a apólice.|

    Para uma configuração de exemplo, consulte [o Ligador De Sumo Kafka Connect para o Hub Azure IoT](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Para evitar alterações, utilize __Ctrl + X,__ __Y,__ e depois __Enter__.

Para obter mais informações sobre a [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)configuração do afundatório do conector, consulte .

## <a name="start-the-source-connector"></a>Inicie o conector de origem

1. Para iniciar o conector de origem, utilize o seguinte comando a partir de uma ligação SSH ao nó de borda:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Assim que o conector começar, envie mensagens para o hub IoT a partir do seu ou dispositivo. À medida que o conector lê mensagens do hub IoT e as armazena no tópico kafka, regista informações para a consola:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Pode ver vários avisos à medida que o conector começa. Estes avisos não causam problemas ao receber mensagens do centro IoT.

1. Pare o conector após alguns minutos utilizando **CTRL + C** duas vezes. Levará alguns minutos para o conector parar.

## <a name="start-the-sink-connector"></a>Inicie o conector da pia

A partir de uma ligação SSH ao nó de borda, utilize o seguinte comando para iniciar o conector da pia em modo autónomo:

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

1. Pegue o endereço dos corretores Kafka para a nova sessão de SSH. Substitua a PASSWORD pela senha de login do cluster e introduza o comando:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Para enviar mensagens para o `iotout` tópico, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Este comando não te devolve ao pedido normal de Bash. Em vez disso, envia `iotout` entrada de teclado para o tópico.

1. Para enviar uma mensagem para o seu dispositivo, colhe um documento `kafka-console-producer`JSON na sessão SSH para o .

    > [!IMPORTANT]  
    > Deve definir o valor `"deviceId"` da entrada na identificação do seu dispositivo. No exemplo seguinte, o `myDeviceId`dispositivo é nomeado:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    O esquema para este documento JSON é [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)descrito com mais detalhes em .

    Se estiver a utilizar o dispositivo Raspberry Pi simulado, e estiver em funcionamento, a seguinte mensagem é registada pelo dispositivo:

    ```output
    Receive message: Turn On
    ```

    Reenvie o documento JSON, mas `"message"` altere o valor da entrada. O novo valor é registado pelo dispositivo.

Para obter mais informações sobre [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)a utilização do conector da pia, consulte .

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar a API Apache Kafka Connect para iniciar o Conector IoT Kafka no HDInsight. Use os seguintes links para descobrir outras formas de trabalhar com Kafka:

* [Use Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Use Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

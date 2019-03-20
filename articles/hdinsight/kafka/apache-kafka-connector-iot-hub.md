---
title: Utilizar o Apache Kafka no HDInsight com o Hub IoT do Azure
description: Saiba como utilizar o Apache Kafka no HDInsight com o IoT Hub do Azure. O projeto de Kafka ligar IoT Hub do Azure fornece um conector de origem e sink para o Kafka. O conector de origem pode ler dados a partir do IoT Hub e o conector de sink escreve para o IoT Hub.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: e64490517603687684617ce915e0d3f3e35298e9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093393"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Utilizar o Apache Kafka no HDInsight com o Hub IoT do Azure

Saiba como utilizar o [Apache Kafka ligar o Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) conector para mover dados entre o Apache Kafka no HDInsight e o IoT Hub do Azure. Neste documento, irá aprender a executar o conector do IoT Hub a partir de um nó de extremidade do cluster.

A API Kafka de ligar permite-lhe implementar os conectores que continuamente extrair dados para o Kafka, ou enviar dados por push do Kafka para outro sistema. O [Apache Kafka ligar o Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) é um conector que obtém os dados do IoT Hub do Azure para o Kafka. Ele também pode enviar dados do Kafka para o IoT Hub. 

Ao extrair a partir do IoT Hub, usa um __origem__ conector. Ao enviar para o IoT Hub, usa um __sink__ conector. O conector do IoT Hub fornece conectores de origem e sink.

O diagrama seguinte mostra o fluxo de dados entre o IoT Hub do Azure e o Kafka no HDInsight ao utilizar o conector.

![Imagem que mostra o fluxo de dados do IoT Hub para Kafka através do conector](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Para obter mais informações sobre a API de ligar, consulte [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Um cluster do Kafka no HDInsight. Para obter mais informações, veja o documento [Início rápido do Kafka no HDInsight](apache-kafka-get-started.md).

* Um nó de extremidade do cluster do Kafka. Para obter mais informações, consulte a [utilizar nós de extremidade com o HDInsight](../hdinsight-apps-use-edge-node.md) documento.

* Um Hub IoT do Azure. Para este tutorial, recomendo o [simulador online de ligar o Raspberry Pi hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) documento.

* Um cliente SSH. Os passos neste documento utilizam SSH para se ligar ao cluster. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Instalar o conector

1. Baixe a versão mais recente de ligar o Kafka para Azure IoT Hub a partir [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Para carregar o ficheiro. JAR ao nó de extremidade do seu cluster do Kafka no HDInsight, utilize o seguinte comando:

    > [!NOTE]  
    > Substitua `sshuser` com a conta de utilizador SSH para o seu cluster do HDInsight. Substitua `new-edgenode` com o nome do nó de extremidade. Substitua `clustername` com o nome do cluster. Para obter mais informações sobre o ponto final SSH para o nó de extremidade, consulte a [utilizar nós de extremidade com o HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) documento.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Uma vez concluída a cópia do ficheiro, ligue-se ao nó de extremidade através de SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Para instalar o conector para o Kafka `libs` directory, utilize o seguinte comando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]  
> Caso se depare com problemas com o resto dos passos neste documento ao utilizar um ficheiro. JAR criados previamente, tente criar o pacote de origem.
>
> Para criar o conector, tem de ter um ambiente de desenvolvimento Scala com o [Scala criar ferramenta](https://www.scala-sbt.org/).
>
> 1. Transferir a origem para o conector do [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) ao seu ambiente de desenvolvimento.
>
> 2. A partir de uma linha de comandos no diretório do projeto, utilize o seguinte comando para criar e empacotar o projeto:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Este comando cria um ficheiro denominado `kafka-connect-iothub-assembly_2.11-0.6.jar` no `target/scala-2.11` diretório para o projeto.

## <a name="configure-apache-kafka"></a>Configure Apache Kafka

A partir de uma ligação SSH ao nó de extremidade, utilize os seguintes passos para configurar o Kafka para executar o conector no modo autônomo:

1. Guarde o nome de cluster a uma variável. Usar uma variável torna mais fácil copiar/colar os outros comandos nesta secção:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Instalar o `jq` utilitário. Esse utilitário torna mais fácil de processar documentos JSON retornados de consultas do Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Obtenha o endereço do Kafka mediadores. Pode haver vários mediadores no seu cluster, mas só precisa de fazer referência a um ou dois. Para obter o endereço de dois anfitriões de Mediador, utilize o seguinte comando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster. O valor devolvido é semelhante ao seguinte texto:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Obtenha o endereço de nós do Apache Zookeeper. Existem vários nós do Zookeeper no cluster, mas só precisa de fazer referência a um ou dois. Para obter o endereço de dois nós do Zookeeper, utilize o seguinte comando:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Ao executar o conector no modo autônomo, o `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` ficheiro é utilizado para comunicar com os mediadores Kafka. Para editar o `connect-standalone.properties` de ficheiros, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Para configurar a configuração autónoma para o nó de extremidade localizar o Kafka mediadores, encontre a linha que começa com `bootstrap.servers=`. Substitua o `localhost:9092` valor com os hosts de Mediador do passo anterior.

    * Alteração da `key.converter=` e `value.converter=` linhas para os seguintes valores:

        ```ini
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]  
        > Esta alteração permite que teste a utilizar o produtor de consola incluído com o Kafka. Poderá ter diferentes conversores para outros produtores e consumidores. Para obter informações sobre como utilizar os outros valores de conversor, consulte [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Adicione uma linha no final do ficheiro que contém o seguinte texto:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]  
        > Esta alteração serve para impedir que os tempos limite no conector do sink ao limitá-la para 10 registos ao mesmo tempo. Para mais informações, consulte [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Para guardar o ficheiro, utilize __Ctrl + X__, __Y__e, em seguida __Enter__.

7. Para criar os tópicos utilizados pelo conector do, utilize os seguintes comandos:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Para verificar se o `iotin` e `iotout` tópicos existir, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    O `iotin` tópico é utilizado para receber mensagens do IoT Hub. O `iotout` tópico é utilizado para enviar mensagens para o IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Obter informações de ligação do IoT Hub

Para obter informações do hub IoT utilizadas pelo conector do, utilize os seguintes passos:

1. Obtenha o ponto final de compatível com o Event Hub e o nome do ponto final compatível com o Event Hub do hub IoT. Para obter essas informações, utilize um dos seguintes métodos:

   * __Partir do [portal do Azure](https://portal.azure.com/)__, utilize os seguintes passos:

     1. Navegue até ao seu IoT Hub e selecione __pontos de extremidade__.
     2. Partir __pontos finais incorporados__, selecione __eventos__.
     3. Partir __propriedades__, copie o valor dos seguintes campos:

         * __Nome compatível com o Hub de eventos__
         * __Ponto final compatível com o Hub de eventos__
         * __Partições__

        > [!IMPORTANT]  
        > O valor do ponto final do portal pode conter texto extra que não é necessária neste exemplo. Extraia o texto que corresponde a este padrão `sb://<randomnamespace>.servicebus.windows.net/`.

   * __Partir do [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, utilize o seguinte comando:

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Substitua `myhubname` com o nome do IoT hub. A resposta é semelhante ao seguinte texto:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Obter o __partilhado a política de acesso__ e __chave__. Neste exemplo, utilizar o __serviço__ chave. Para obter essas informações, utilize um dos seguintes métodos:

    * __Partir do [portal do Azure](https://portal.azure.com/)__, utilize os seguintes passos:

        1. Selecione __políticas de acesso partilhado__e, em seguida, selecione __serviço__.
        2. Copiar o __chave primária__ valor.
        3. Copiar o __cadeia de ligação – chave primária__ valor.

    * __Partir do [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, utilize o seguinte comando:

        1. Para obter o valor da chave primário, utilize o seguinte comando:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Substitua `myhubname` com o nome do IoT hub. A resposta é a chave primária para o `service` política para este hub.

        2. Para obter a cadeia de ligação para o `service` política, utilize o seguinte comando:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Substitua `myhubname` com o nome do IoT hub. A resposta é a cadeia de ligação para o `service` política.

## <a name="configure-the-source-connection"></a>Configurar a ligação de origem

Para configurar a origem para trabalhar com o seu IoT Hub, execute as seguintes ações a partir de uma ligação SSH ao nó de extremidade:

1. Criar uma cópia do `connect-iot-source.properties` de ficheiros a `/usr/hdp/current/kafka-broker/config/` diretório. Para transferir o ficheiro do projeto toketi-kafka-connect-iothub, utilize o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Para editar o `connect-iot-source.properties` de ficheiros e adicione as informações do hub IoT, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    No editor, localizar e alterar as seguintes entradas:

   * `Kafka.Topic=PLACEHOLDER`: Substitua  por `iotin`. Mensagens recebidas a partir do IoT hub são colocadas no `iotin` tópico.
   * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Substitua `PLACEHOLDER` com o nome compatível com o Event Hub.
   * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Substitua `PLACEHOLDER` com o ponto final compatível com o Event Hub.
   * `IotHub.Partitions=PLACEHOLDER`: Substitua `PLACEHOLDER` com o número de partições dos passos anteriores.
   * `IotHub.AccessKeyName=PLACEHOLDER`: Substitua  por `service`.
   * `IotHub.AccessKeyValue=PLACEHOLDER`: Substitua `PLACEHOLDER` com a chave primária do `service` política.
   * `IotHub.StartType=PLACEHOLDER`: Substitua `PLACEHOLDER` com uma data UTC. Esta data é quando o conector começa a verificar a existência de mensagens. O formato de data é `yyyy-mm-ddThh:mm:ssZ`.
   * `BatchSize=100`: Substitua  por `5`. Esta alteração faz com que o conector permite ler mensagens para o Kafka, uma vez que existem cinco novas mensagens do IoT hub.

     Para um exemplo de configuração, consulte [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Para guardar as alterações, utilize __Ctrl + X__, __Y__e, em seguida __Enter__.

Para obter mais informações sobre como configurar a origem do conector, consulte [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Configurar a ligação de sink

Para configurar a ligação de sink para trabalhar com o seu IoT Hub, execute as seguintes ações a partir de uma ligação de SSH ao nó de extremidade:

1. Criar uma cópia do `connect-iothub-sink.properties` de ficheiros a `/usr/hdp/current/kafka-broker/config/` diretório. Para transferir o ficheiro do projeto toketi-kafka-connect-iothub, utilize o seguinte comando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Para editar o `connect-iothub-sink.properties` de ficheiros e adicione as informações do hub IoT, utilize o seguinte comando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    No editor, localizar e alterar as seguintes entradas:

   * `topics=PLACEHOLDER`: Substitua  por `iotout`. Mensagens escritas para `iotout` tópico são reencaminhados para o hub IoT.
   * `IotHub.ConnectionString=PLACEHOLDER`: Substitua `PLACEHOLDER` pela cadeia de ligação para o `service` política.

     Para um exemplo de configuração, consulte [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Para guardar as alterações, utilize __Ctrl + X__, __Y__e, em seguida __Enter__.

Para obter mais informações sobre como configurar o sink de conector, consulte [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Iniciar o conector de origem

Para iniciar o conector de origem, utilize o comando seguinte a partir de uma ligação SSH ao nó de extremidade:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Assim que o conector for iniciada, envie mensagens para o hub IoT a partir de seu dispositivo ou dispositivos. À medida que o conector lê mensagens do IoT hub e armazena-os no tópico do Kafka, regista informações para a consola:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]  
> Poderá ver vários avisos que o conector é inicializado. Esses avisos não causam problemas com o recebimento de mensagens do IoT hub.

Para parar o conector, utilize __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Iniciar o conector de sink

A partir de uma ligação SSH ao nó de extremidade, utilize o seguinte comando para iniciar o conector do coletor no modo autônomo:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Como o conector seja executado, são apresentadas informações semelhantes ao seguinte texto:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Pode observar vários avisos que o conector é inicializado. Pode ignorar com segurança estes avisos.

Para enviar mensagens através do conector, utilize os seguintes passos:

1. Abra outra sessão SSH para o cluster de Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Para enviar mensagens para o `iotout` tópico, utilize o seguinte comando:

    > [!WARNING]  
    > Como se trata de uma nova ligação de SSH, o `$KAFKABROKERS` variável contém quaisquer informações. Para configurá-lo, utilize um dos seguintes métodos:
    >
    > * Utilize os três primeiros passos na [configurar o Apache Kafka](#configure-apache-kafka) secção.
    > * Uso `echo $KAFKABROKERS` a partir da ligação de SSH anterior para obter os valores e, em seguida, substitua `$KAFKABROKERS` no comando seguinte com os valores reais.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Este comando não regressar a linha de comandos de Bash normal. Em vez disso, ele envia a entrada de teclado para o `iotout` tópico.

3. Para enviar uma mensagem para o seu dispositivo, cole um documento JSON para a sessão SSH para o `kafka-console-producer`.

    > [!IMPORTANT]  
    > Tem de definir o valor da `"deviceId"` entrada para o ID do seu dispositivo. No exemplo seguinte, o dispositivo tem o nome `fakepi`:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    O esquema para este documento JSON é descrito mais detalhadamente [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Se estiver a utilizar o dispositivo Raspberry Pi e está a ser executado, a seguinte mensagem de erro é registada pelo dispositivo:

    ```text
    Receive message: Turn On
    ```

    Reenviar o documento JSON, mas altere o valor da `"message"` entrada. O novo valor é registado pelo dispositivo.

Para obter mais informações sobre como utilizar o conector de sink, consulte [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a utilizar a API do Apache Kafka ligar para iniciar o conector do IoT Kafka no HDInsight. Utilize as seguintes ligações para descobrir outras maneiras de trabalhar com o Kafka:

* [Utilizar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utilizar o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

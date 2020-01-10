---
title: Tópicos de Apache Kafka de espelho – Azure HDInsight
description: Saiba como usar o recurso de espelhamento do Apache Kafka para manter uma réplica de um Kafka no cluster HDInsight espelhando tópicos para um cluster secundário.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 0f444838c87e14fa88f2785030c29915df637cf8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552207"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Use MirrorMaker para replicar tópicos do Apache Kafka com o Kafka no HDInsight

Saiba como usar o recurso de espelhamento do Apache Kafka para replicar tópicos para um cluster secundário. O espelhamento pode ser executado como um processo contínuo ou usado de forma intermitente como um método de migração de dados de um cluster para outro.

Neste exemplo, o espelhamento é usado para replicar tópicos entre dois clusters HDInsight. Ambos os clusters estão em diferentes redes virtuais em data centers diferentes.

> [!WARNING]  
> O espelhamento não deve ser considerado como um meio de obter tolerância a falhas. O deslocamento dos itens em um tópico é diferente entre os clusters primário e secundário, para que os clientes não possam usar os dois de maneira intercambiável.
>
> Se você estiver preocupado com a tolerância a falhas, defina a replicação para os tópicos em seu cluster. Para obter mais informações, consulte Introdução [ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Como funciona Apache Kafka o espelhamento

O espelhamento funciona usando a ferramenta [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (parte do Apache Kafka) para consumir registros de tópicos no cluster primário e, em seguida, criar uma cópia local no cluster secundário. O MirrorMaker usa um (ou mais) *consumidores* que lêem do cluster primário e um *produtor* que grava no cluster local (secundário).

A configuração de espelhamento mais útil para a recuperação de desastre utiliza clusters Kafka em diferentes regiões do Azure. Para conseguir isso, as redes virtuais nas quais os clusters residem são emparelhadas.

O diagrama a seguir ilustra o processo de espelhamento e como a comunicação flui entre os clusters:

![Diagrama do processo de espelhamento](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Os clusters primário e secundário podem ser diferentes no número de nós e partições, e os deslocamentos nos tópicos também são diferentes. O espelhamento mantém o valor de chave usado para particionamento, portanto, a ordem de registro é preservada por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhamento entre limites de rede

Se você precisar espelhar entre clusters Kafka em redes diferentes, haverá as seguintes considerações adicionais:

* **Gateways**: as redes devem ser capazes de se comunicar no nível de TCP/IP.

* **Endereçamento de servidor**: você pode optar por endereçar os nós de cluster usando seus endereços IP ou nomes de domínio totalmente qualificados.

    * **Endereços IP**: se você configurar seus clusters Kafka para usar publicidade de endereço IP, poderá prosseguir com a configuração de espelhamento usando os endereços IP dos nós do agente e nós Zookeeper.
    
    * **Nomes de domínio**: se você não configurar os clusters Kafka para publicidade de endereço IP, os clusters deverão ser capazes de se conectar entre si usando nomes de domínio totalmente qualificados (FQDNs). Isso requer um servidor DNS (sistema de nomes de domínio) em cada rede configurada para encaminhar solicitações para outras redes. Ao criar uma rede virtual do Azure, em vez de usar o DNS automático fornecido com a rede, você deve especificar um servidor DNS personalizado e o endereço IP para o servidor. Depois que a rede virtual tiver sido criada, você deverá criar uma máquina virtual do Azure que usa esse endereço IP e, em seguida, instalar e configurar o software DNS nele.

    > [!WARNING]  
    > Crie e configure o servidor DNS personalizado antes de instalar o HDInsight na rede virtual. Não há nenhuma configuração adicional necessária para o HDInsight usar o servidor DNS configurado para a rede virtual.

Para obter mais informações sobre como conectar duas redes virtuais do Azure, consulte [Configurar uma conexão de vnet para vnet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Arquitetura de espelhamento

Essa arquitetura apresenta dois clusters em diferentes grupos de recursos e redes virtuais: um **primário** e um **secundário**.

### <a name="creation-steps"></a>Etapas de criação

1. Crie dois novos grupos de recursos:

    |Grupo de Recursos | Localização |
    |---|---|
    | Kafka-principal-RG | Centro dos E.U.A. |
    | Kafka-secundário-RG | E.U.A. Centro-Norte |

1. Crie uma nova rede virtual **Kafka-Primary-vnet** em **Kafka-Primary-RG**. Deixe as configurações padrão.
1. Crie uma nova rede virtual **Kafka-Secondary-vnet** em **Kafka-Secondary-RG**, também com as configurações padrão.

1. Crie dois novos clusters Kafka:

    | Nome do cluster | Grupo de Recursos | Rede Virtual | Conta de Armazenamento |
    |---|---|---|---|
    | Kafka-primário-cluster | Kafka-principal-RG | Kafka-Primary-vnet | kafkaprimarystorage |
    | Kafka-secundário-cluster | Kafka-secundário-RG | Kafka-secundário-vnet | kafkasecondarystorage |

1. Criar emparelhamentos de rede virtual. Esta etapa criará dois emparelhamentos: um de **Kafka-Primary-vnet** para **Kafka-Secondary-vnet** e um de volta de **Kafka-Secondary-vnet** para **Kafka-Primary-vnet**.
    1. Selecione a rede virtual **Kafka-Primary-vnet** .
    1. Selecione **emparelhamentos** em **configurações**.
    1. Selecione **Adicionar**.
    1. Na tela **Adicionar emparelhamento** , insira os detalhes, conforme mostrado na captura abaixo.

        ![Adicionar emparelhamento vnet Kafka do HDInsight](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Configurar publicidade de IP

Configure o anúncio de IP para permitir que um cliente se conecte usando endereços IP do agente em vez de nomes de domínio.

1. Vá para o painel do Ambari para o cluster primário: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
1. Selecione **serviços** > **Kafka**. CliSelectck a guia **configurações** .
1. Adicione as seguintes linhas de configuração à seção de **modelo Kafka-env** inferior. Selecione **Guardar**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Insira uma observação na tela **salvar configuração** e clique em **salvar**.
1. Se você for solicitado com o aviso de configuração, clique em **continuar mesmo assim**.
1. Selecione **OK** em **salvar alterações de configuração**.
1. Selecione **reiniciar** > **reiniciar todos os afetados** na notificação **reinicialização necessária** . Selecione **confirmar reiniciar tudo**.

    ![O Apache Ambari reinicia todos os afetados](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configure o Kafka para escutar em todas as interfaces de rede.
    
1. Permaneça na guia **configurações** em **Serviços** > **Kafka**. Na seção **agente Kafka** , defina a propriedade **listeners** como `PLAINTEXT://0.0.0.0:9092`.
1. Selecione **Guardar**.
1. Selecione **reiniciar**e **confirme reiniciar tudo**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Endereços IP do agente de registro e endereços Zookeeper para o cluster primário.

1. Selecione **hosts** no painel do Ambari.
1. Anote os endereços IP para os agentes e zookeepers. Os nós do agente têm o **WN** como as duas primeiras letras do nome do host e os nós Zookeeper têm **ZK** como as duas primeiras letras do nome do host.

    ![Endereços IP do nó de exibição do Apache Ambari](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Repita as três etapas anteriores para o segundo cluster **Kafka-Secondary-cluster**: Configure o anúncio de IP, defina os ouvintes e anote os endereços IP do agente e do Zookeeper.

## <a name="create-topics"></a>Criar tópicos

1. Conecte-se ao cluster **primário** usando SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** pelo nome de usuário SSH usado ao criar o cluster. Substitua **PRIMARYCLUSTER** pelo nome base usado ao criar o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Use o comando a seguir para criar uma variável com os hosts Apache Zookeeper para o cluster primário. As cadeias de caracteres como `ZOOKEEPER_IP_ADDRESS1` devem ser substituídas pelos endereços IP reais gravados anteriormente, como `10.23.0.11` e `10.23.0.7`. Se você estiver usando a resolução de FQDN com um servidor DNS personalizado, siga [estas etapas](apache-kafka-get-started.md#getkafkainfo) para obter os nomes do agente e do Zookeeper.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Para criar um tópico chamado `testtopic`, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Use o seguinte comando para verificar se o tópico foi criado:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A resposta contém `testtopic`.

1. Use o seguinte para exibir as informações do host Zookeeper para este (o **primário**) cluster:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Isso retorna informações semelhantes ao seguinte texto:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Salve essas informações. Ele é usado na próxima seção.

## <a name="configure-mirroring"></a>Configurar o espelhamento

1. Conecte-se ao cluster **secundário** usando uma sessão SSH diferente:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** pelo nome de usuário SSH usado ao criar o cluster. Substitua **SECONDARYCLUSTER** pelo nome usado ao criar o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Um arquivo de `consumer.properties` é usado para configurar a comunicação com o cluster **primário** . Para criar o arquivo, use o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Use o texto a seguir como o conteúdo do arquivo de `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **PRIMARY_ZKHOSTS** pelos endereços IP do Zookeeper do cluster **primário** .

    Esse arquivo descreve as informações do consumidor a serem usadas ao ler o cluster primário Kafka. Para obter mais informações sobre a configuração do consumidor, consulte Configurações do [consumidor](https://kafka.apache.org/documentation#consumerconfigs) em Kafka.Apache.org.

    Para salvar o arquivo, use **Ctrl + X**, **Y**e, em seguida, **Enter**.

1. Antes de configurar o produtor que se comunica com o cluster secundário, configure uma variável para os endereços IP do agente do cluster **secundário** . Use os seguintes comandos para criar essa variável:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    O comando `echo $SECONDARY_BROKERHOSTS` deve retornar informações semelhantes ao seguinte texto:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Um arquivo de `producer.properties` é usado para comunicar o cluster **secundário** . Para criar o arquivo, use o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Use o texto a seguir como o conteúdo do arquivo de `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Substitua **SECONDARY_BROKERHOSTS** pelos endereços IP do agente usados na etapa anterior.

    Para obter mais informações sobre a configuração do produtor, consulte Configurações do [produtor](https://kafka.apache.org/documentation#producerconfigs) em Kafka.Apache.org.

1. Use os comandos a seguir para criar uma variável de ambiente com os endereços IP dos hosts Zookeeper para o cluster secundário:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. A configuração padrão para Kafka no HDInsight não permite a criação automática de tópicos. Você deve usar uma das seguintes opções antes de iniciar o processo de espelhamento:

    * **Criar os tópicos no cluster secundário**: essa opção também permite que você defina o número de partições e o fator de replicação.

        Você pode criar tópicos antecipadamente usando o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Substitua `testtopic` pelo nome do tópico a ser criado.

    * **Configurar o cluster para a criação automática de tópicos**: essa opção permite que o MirrorMaker crie tópicos automaticamente, no entanto, ele pode criá-los com um número diferente de partições ou fator de replicação do que o tópico primário.

        Para configurar o cluster secundário para criar tópicos automaticamente, execute estas etapas:

        1. Vá para o painel do Ambari para o cluster secundário: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Clique em **serviços** > **Kafka**. Clique na guia **configurações** .
        1. No campo __filtro__ , insira um valor de `auto.create`. Isso filtra a lista de propriedades e exibe a configuração `auto.create.topics.enable`.
        1. Altere o valor de `auto.create.topics.enable` para true e, em seguida, selecione __salvar__. Adicione uma observação e, em seguida, selecione __salvar__ novamente.
        1. Selecione o serviço __Kafka__ , selecione __reiniciar__e, em seguida, selecione __reiniciar todos os afetados__. Quando solicitado, selecione __confirmar reiniciar tudo__.

        ![Kafka habilitar tópicos de criação automática](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Iniciar MirrorMaker

1. Na conexão SSH para o cluster **secundário** , use o seguinte comando para iniciar o processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros usados neste exemplo são:

    |Parâmetro |Descrição |
    |---|---|
    |--Consumer. config|Especifica o arquivo que contém as propriedades do consumidor. Essas propriedades são usadas para criar um consumidor que lê o cluster *primário* Kafka.|
    |--produtor. config|Especifica o arquivo que contém propriedades de produtor. Essas propriedades são usadas para criar um produtor que grava no cluster Kafka *secundário* .|
    |--lista de permissões|Uma lista de tópicos que MirrorMaker Replica do cluster primário para o secundário.|
    |--num. streams|O número de threads de consumidor a serem criados.|

    O consumidor no nó secundário agora está aguardando para receber mensagens.

2. Na conexão SSH ao cluster **primário** , use o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando você chegar a uma linha em branco com um cursor, digite algumas mensagens de texto. As mensagens são enviadas para o tópico no cluster **primário** . Quando terminar, use **Ctrl + C** para finalizar o processo de produtor.

3. Na conexão SSH para o cluster **secundário** , use **Ctrl + C** para finalizar o processo MirrorMaker. Pode levar vários segundos para encerrar o processo. Para verificar se as mensagens foram replicadas para o secundário, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A lista de tópicos agora inclui `testtopic`, que é criado quando o Espelhador espelha o tópico do cluster primário para o secundário. As mensagens recuperadas do tópico são iguais àquelas que você inseriu no cluster primário.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

As etapas neste documento criaram clusters em diferentes grupos de recursos do Azure. Para excluir todos os recursos criados, você pode excluir os dois grupos de recursos criados: **Kafka-Primary-RG** e **Kafka-secondary_rg**. A exclusão dos grupos de recursos remove todos os recursos criados com o seguinte documento, incluindo clusters, redes virtuais e contas de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a usar o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para criar uma réplica de um cluster [Apache Kafka](https://kafka.apache.org/) . Use os links a seguir para descobrir outras maneiras de trabalhar com o Kafka:

* [Apache Kafka documentação do MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) em cwiki.Apache.org.
* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Usar Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usar Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conectar-se a Apache Kafka por meio de uma rede virtual do Azure](apache-kafka-connect-vpn-gateway.md)

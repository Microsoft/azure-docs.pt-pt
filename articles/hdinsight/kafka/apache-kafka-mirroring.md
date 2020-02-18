---
title: Tópicos De Apache Kafka do Espelho - Azure HDInsight
description: Aprenda a usar a funcionalidade de espelhamento de Apache Kafka para manter uma réplica de um aglomerado de Kafka no HDInsight, espelhando tópicos para um cluster secundário.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425889"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Use MirrorMaker para replicar tópicos Apache Kafka com Kafka no HDInsight

Aprenda a usar o recurso de espelhamento de Apache Kafka para replicar tópicos para um cluster secundário. O espelhamento pode ser usado como um processo contínuo, ou usado intermitentemente como um método de migrar dados de um cluster para outro.

Neste exemplo, o espelhamento é usado para replicar tópicos entre dois clusters HDInsight. Ambos os clusters estão em diferentes redes virtuais em diferentes datacenters.

> [!WARNING]  
> O espelhamento não deve ser considerado como um meio para alcançar a tolerância à falta. A compensação para itens dentro de um tópico é diferente entre os clusters primário e secundário, pelo que os clientes não podem usar os dois intercambiavelmente.
>
> Se está preocupado com a tolerância a falhas, deve definir a replicação para os tópicos dentro do seu cluster. Para mais informações, consulte [Começar com Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Como funciona o espelho de Apache Kafka

O espelhamento funciona utilizando a ferramenta [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (parte do Apache Kafka) para consumir registos de tópicos no cluster primário e, em seguida, criar uma cópia local no cluster secundário. O MirrorMaker utiliza um (ou mais) *consumidores* que lêem do cluster primário, e um *produtor* que escreve para o cluster local (secundário).

A configuração espelhada mais útil para a recuperação de desastres utiliza aglomerados kafka em diferentes regiões de Azure. Para isso, as redes virtuais onde os clusters residem são observadas em conjunto.

O diagrama que se segue ilustra o processo de espelhamento e como a comunicação flui entre aglomerados:

![Diagrama do processo de espelhamento](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Os aglomerados primários e secundários podem ser diferentes no número de nós e divisórias, e as compensações dentro dos tópicos também são diferentes. O espelhamento mantém o valor-chave que é usado para a partilha, pelo que a ordem de gravação é preservada numa base por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhando através dos limites da rede

Se precisa de se espelhar entre aglomerados de Kafka em diferentes redes, existem as seguintes considerações adicionais:

* **Gateways**: As redes devem poder comunicar ao nível DoTCP/IP.

* **Endereço do servidor:** Pode optar por endereçar os seus nós de cluster utilizando os seus endereços IP ou nomes de domínio totalmente qualificados.

    * **Endereços IP**: Se configurar os seus clusters Kafka para utilizar publicidade de endereçoIP, pode proceder com a configuração de espelhamento utilizando os endereços IP dos nós do corretor e dos nós do zookeeper.
    
    * **Nomes de domínio**: Se não configurar os seus clusters Kafka para publicidade de endereçoip, os clusters devem ser capazes de se conectar em conjunto utilizando nomes de domínio totalmente qualificados (FQDNs). Isto requer um servidor do Sistema de Nome de Domínio (DNS) em cada rede que esteja configurado para encaminhar pedidos para outras redes. Ao criar uma Rede Virtual Azure, em vez de utilizar o DNS automático fornecido com a rede, deve especificar um servidor DNS personalizado e o endereço IP para o servidor. Depois de criada a Rede Virtual, terá de criar uma Máquina Virtual Azure que utilize esse endereço IP, depois instale e configure o software DNS na seleção.

    > [!WARNING]  
    > Crie e configure o servidor DNS personalizado antes de instalar o HDInsight na Rede Virtual. Não existe uma configuração adicional necessária para que o HDInsight utilize o servidor DNS configurado para a Rede Virtual.

Para obter mais informações sobre a ligação de duas redes virtuais Azure, consulte [Configurar uma ligação VNet-to-VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Arquitetura espelhada

Esta arquitetura apresenta dois clusters em diferentes grupos de recursos e redes virtuais: uma **primária** e **secundária.**

### <a name="creation-steps"></a>Passos de criação

1. Criar dois novos grupos de recursos:

    |Grupo de Recursos | Localização |
    |---|---|
    | kafka-primário-rg | E.U.A. Central |
    | kafka-secundário-rg | E.U.A. Centro-Norte |

1. Crie uma nova rede virtual **kafka-primary-vnet** em **kafka-primary-rg**. Deixe as definições predefinidas.
1. Crie uma nova rede virtual **kafka-secondary-vnet** em **kafka-secondary-rg,** também com configurações padrão.

1. Crie dois novos aglomerados kafka:

    | Nome do cluster | Grupo de Recursos | Rede Virtual | Conta de Armazenamento |
    |---|---|---|---|
    | kafka-aglomerado primário | kafka-primário-rg | kafka-primário-vnet | kafkaprimarystorage |
    | kafka-secundário aglomerado | kafka-secundário-rg | kafka-secundário-vnet | kafkasecondarystorage |

1. Criar os pares de rede virtuais. Este passo criará dois pares: um de **kafka-primário-vnet** a **kafka-secondary-vnet** e um de volta de **kafka-secondary-vnet** para **kafka-primary-vnet**.
    1. Selecione a rede virtual **kafka-primary-vnet.**
    1. Selecione **Peerings** em **Definições**.
    1. Selecione **Adicionar**.
    1. No ecrã **de peering Add,** introduza os detalhes como mostrado na imagem abaixo.

        ![HDInsight Kafka adicionar vnet peering](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Configurar publicidade IP

Configure a publicidade IP para permitir que um cliente se conecte usando endereços IP do corretor em vez de nomes de domínio.

1. Vá ao painel de ambari para o cluster primário: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
1. Selecione **Serviços** > **Kafka.** CliSelectck o separador **Configs.**
1. Adicione as seguintes linhas de config à secção de **modelo kafka-env** inferior. Selecione **Guardar**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Introduza uma nota no ecrã **'Guardar Configuração'** e clique em **Guardar**.
1. Se for solicitado com aviso de configuração, clique em **"Proceder de qualquer maneira".**
1. Selecione **Ok** nas alterações de **configuração de poupança**.
1. Selecione **Reiniciar** > **reiniciar todos os afetados** na notificação requerida de **reiniciar.** Selecione **Confirmar Reiniciar tudo**.

    ![Apache Ambari reinicia todos os afetados](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configure kafka para ouvir em todas as interfaces de rede.
    
1. Fique no separador **Configs** ao abrigo dos **Serviços** > **Kafka.** Na secção **Kafka Broker** definiu a propriedade dos **ouvintes** para `PLAINTEXT://0.0.0.0:9092`.
1. Selecione **Guardar**.
1. Selecione **Reiniciar**e **confirmar reiniciar tudo**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Endereços IP do Corretor de Registos e endereços zookeeper para cluster primário.

1. Selecione **Hosts** no painel Ambari.
1. Tome nota dos endereços IP para os Corretores e Zookeepers. Os nós de corretor têm **wn** como as duas primeiras letras do nome anfitrião, e os nós do zookeeper têm **zk** como as duas primeiras letras do nome anfitrião.

    ![Apache Ambari vê endereços ip do nó](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Repita os três passos anteriores para o segundo **cluster kafka-secundário**: configure publicidade IP, coloque os ouvintes e tome nota dos endereços IP broker e Zookeeper.

## <a name="create-topics"></a>Criar tópicos

1. Ligue-se ao cluster **primário** utilizando SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua o **sshuser** pelo nome de utilizador SSH utilizado na criação do cluster. Substitua o **PRIMARYCLUSTER** pelo nome base utilizado na criação do cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Use o seguinte comando para criar uma variável com os anfitriões do Zookeeper Apache para o aglomerado primário. As cordas como `ZOOKEEPER_IP_ADDRESS1` devem ser substituídas por endereços IP reais registados anteriormente, tais como `10.23.0.11` e `10.23.0.7`. Se estiver a usar a resolução FQDN com um servidor DNS personalizado, siga [estes passos](apache-kafka-get-started.md#getkafkainfo) para obter nomes de corretor e zookeeper.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Para criar um tópico chamado `testtopic`, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Utilize o seguinte comando para verificar se o tópico foi criado:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A resposta contém `testtopic`.

1. Utilize o seguinte para visualizar as informações do hospedeiro zookeeper para este (o **principal)** cluster:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Isto devolve informações semelhantes ao seguinte texto:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Guarde esta informação. É usado na próxima secção.

## <a name="configure-mirroring"></a>Configure espelhamento

1. Ligue-se ao cluster **secundário** utilizando uma sessão sSH diferente:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua o **sshuser** pelo nome de utilizador SSH utilizado na criação do cluster. Substitua o **SECONDCLUSTER** pelo nome utilizado na criação do cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Um ficheiro `consumer.properties` é usado para configurar a comunicação com o cluster **primário.** Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Utilize o seguinte texto como conteúdo do ficheiro `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **PRIMARY_ZKHOSTS** pelos endereços IP do Zookeeper do cluster **primário.**

    Este ficheiro descreve a informação do consumidor a utilizar ao ler do cluster principal de Kafka. Para obter mais informações sobre a configuração do consumidor, consulte o [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) em kafka.apache.org.

    Para guardar o ficheiro, utilize **Ctrl + X,** **Y,** e depois **Enter**.

1. Antes de configurar o produtor que comunica com o cluster secundário, configure uma variável para os endereços IP do corretor do cluster **secundário.** Utilize os seguintes comandos para criar esta variável:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    O comando `echo $SECONDARY_BROKERHOSTS` deve devolver informações semelhantes ao seguinte texto:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Um ficheiro `producer.properties` é usado para comunicar o cluster **secundário.** Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Utilize o seguinte texto como conteúdo do ficheiro `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Substitua **SECONDARY_BROKERHOSTS** com os endereços IP do corretor utilizados no passo anterior.

    Para obter mais informações, consulte o [Produtor Configs](https://kafka.apache.org/documentation#producerconfigs) em kafka.apache.org.

1. Utilize os seguintes comandos para criar uma variável ambiental com os endereços IP dos anfitriões do Zookeeper para o cluster secundário:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. A configuração padrão para Kafka no HDInsight não permite a criação automática de tópicos. Deve utilizar uma das seguintes opções antes de iniciar o processo de espelhamento:

    * **Crie os tópicos no cluster secundário**: Esta opção também permite definir o número de divisórias e o fator de replicação.

        Pode criar tópicos com antecedência utilizando o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Substitua `testtopic` com o nome do tópico para criar.

    * **Configure o cluster para a criação automática**de tópicos : Esta opção permite ao MirrorMaker criar automaticamente tópicos, no entanto pode criá-los com um número diferente de divisórias ou fator de replicação do que o tópico principal.

        Para configurar o cluster secundário para criar automaticamente tópicos, execute estes passos:

        1. Vá ao painel ambari para o cluster secundário: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Clique em **Serviços** > **Kafka.** Clique no separador **Configs.**
        1. No campo __Filtro,__ introduza um valor de `auto.create`. Isto filtra a lista de propriedades e exibe a definição `auto.create.topics.enable`.
        1. Mude o valor da `auto.create.topics.enable` para verdade e, em seguida, selecione __Guardar__. Adicione uma nota e, em seguida, selecione __Guardar__ novamente.
        1. Selecione o serviço __Kafka,__ selecione __Reiniciar__, e, em seguida, selecione __Reiniciar todos os afetados__. Quando solicitado, selecione __Confirmar reiniciar tudo__.

        ![kafka permitir auto criar tópicos](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Iniciar MirrorMaker

1. Da ligação SSH ao cluster **secundário,** utilize o seguinte comando para iniciar o processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros utilizados neste exemplo são:

    |Parâmetro |Descrição |
    |---|---|
    |-consumidor.config|Especifica o ficheiro que contém propriedades de consumo. Estas propriedades são usadas para criar um consumidor que lê do aglomerado *primário* de Kafka.|
    |-produtor.config|Especifica o ficheiro que contém propriedades do produtor. Estas propriedades são usadas para criar um produtor que escreve para o aglomerado *secundário* de Kafka.|
    |-whitelist|Uma lista de tópicos que o MirrorMaker replica do cluster primário para o secundário.|
    |--num.streams|O número de fios de consumo a criar.|

    O consumidor no nó secundário está agora à espera de receber mensagens.

2. Da ligação SSH ao cluster **primário,** utilize o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando chegar a uma linha em branco com um cursor, escreva algumas mensagens de texto. As mensagens são enviadas para o tema no cluster **primário.** Quando terminar, utilize **CTRL + C** para pôr fim ao processo de produtor.

3. Da ligação SSH ao cluster **secundário,** utilize **ctrl + C** para terminar o processo MirrorMaker. Pode levar alguns segundos para terminar o processo. Para verificar se as mensagens foram replicadas para o secundário, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A lista de tópicos inclui agora `testtopic`, que é criada quando o MirrorMaster espelha o tema do cluster primário para o secundário. As mensagens recuperadas do tópico são as mesmas que inseriu no cluster primário.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Os passos deste documento criaram clusters em diferentes grupos de recursos Azure. Para eliminar todos os recursos criados, pode eliminar os dois grupos de recursos criados: **kafka-primary-rg** e **kafka-secondary_rg**. A eliminação dos grupos de recursos remove todos os recursos criados através do seguimento deste documento, incluindo clusters, redes virtuais e contas de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para criar uma réplica de um aglomerado [Apache Kafka.](https://kafka.apache.org/) Use os seguintes links para descobrir outras formas de trabalhar com Kafka:

* [Documentação apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) em cwiki.apache.org.
* [Kafka Mirror Maker Melhores Práticas](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Começa com Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Use Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Ligue-se a Apache Kafka através de uma Rede Virtual Azure](apache-kafka-connect-vpn-gateway.md)

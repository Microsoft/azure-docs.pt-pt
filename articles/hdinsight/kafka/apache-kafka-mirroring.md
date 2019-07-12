---
title: Espelhar tópicos do Apache Kafka - Azure HDInsight
description: Saiba como utilizar a funcionalidade de espelhamento do Apache Kafka para manter uma réplica de um cluster do Kafka no HDInsight ao espelhamento tópicos para um cluster secundário.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657179"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Utilizar MirrorMaker para replicar tópicos do Apache Kafka com o Kafka no HDInsight

Aprenda a usar o recurso de espelhamento do Apache Kafka para replicar tópicos para um cluster secundário. Espelhamento podem ser executadas como um processo contínuo ou intermitentemente utilizado como um método de migração de dados de um cluster para outro.

Neste exemplo, o espelhamento é utilizado para replicar tópicos entre dois clusters do HDInsight. Ambos os clusters estão em diferentes redes virtuais em diferentes centros de dados.

> [!WARNING]  
> Espelhamento não deve ser considerado como um meio para alcançar a tolerância a falhas. O deslocamento para itens dentro de um tópico são diferentes entre clusters primários e secundários, para que os clientes não podem utilizar os dois alternadamente.
>
> Se estiver preocupado com tolerância a falhas, deve definir os replicação para os tópicos no seu cluster. Para obter mais informações, consulte [introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Como funciona o espelhamento do Apache Kafka

Espelhamento funciona utilizando o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ferramenta (parte do Apache Kafka) para consumir registos dos tópicos no cluster principal e, em seguida, criar uma cópia local no cluster secundário. MirrorMaker utiliza uma (ou mais) *consumidores* de leitura a partir do cluster principal e um *produtor* que escreve para o cluster local (secundário).

A configuração de espelhamento mais úteis para a recuperação após desastre utiliza clusters do Kafka em diferentes regiões do Azure. Para conseguir isso, as redes virtuais onde residem os clusters em modo de peering em conjunto.

O diagrama seguinte ilustra o processo de espelhamento e como a comunicação flui entre clusters:

![Diagrama do processo de espelhamento](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Os clusters primários e secundários podem ser diferentes no número de nós e partições e deslocamentos dentro os tópicos são também diferentes. Espelhamento mantém o valor da chave que é utilizado para criação de partições, para que o pedido de registo é preservado numa base por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhamento em limites de rede

Se precisar de espelhar entre clusters do Kafka em redes diferentes, existem as seguintes considerações adicionais:

* **Gateways**: As redes tem de ser capazes de comunicar ao nível do TCP/IP.

* **Servidor de endereçamento**: Pode optar por resolver os nós do cluster utilizando os respetivos endereços IP ou nomes de domínio completamente qualificado.

    * **Endereços IP**: Se configurar seus clusters Kafka para usar a publicidade de endereço IP, poderá continuar a configuração de espelhamento usando os endereços IP de nós do Mediador e nós zookeeper.
    
    * **Nomes de domínio**: Se não configurar seus clusters Kafka para fins de publicidade de endereço IP, os clusters tem de ser capazes de se ligar entre si utilizando totalmente qualificado nomes de domínio (FQDN). Isto requer um servidor de sistema de nomes de domínio (DNS) em cada rede que está configurada para encaminhar pedidos para as outras redes. Ao criar uma rede Virtual do Azure, em vez de utilizar o DNS automática fornecido com a rede, tem de especificar um servidor DNS personalizado e o endereço IP para o servidor. Depois de ter sido criada a rede Virtual, tem, em seguida, criar uma Máquina Virtual que utilize esse endereço IP, em seguida, instalar e configurar software DNS no mesmo.

    > [!WARNING]  
    > Criar e configurar o servidor DNS personalizado antes de instalar HDInsight numa rede Virtual. Não existe nenhuma configuração adicional necessária para o HDInsight utilizar o servidor DNS configurado para a rede Virtual.

Para obter mais informações sobre como ligar duas redes virtuais do Azure, consulte [configurar uma ligação VNet a VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Arquitetura do espelhamento

Esta arquitetura apresenta dois clusters em diferentes grupos de recursos e redes virtuais: uma **primário** e **secundário**.

### <a name="creation-steps"></a>Passos de criação

1. Crie dois novos grupos de recursos:

    |Grupo de Recursos | Location |
    |---|---|
    | kafka-primary-rg | EUA Central |
    | kafka-secondary-rg | EUA Centro-Norte |

1. Criar uma nova rede virtual **kafka-principal-vnet** na **kafka-principal-rg**. Deixe as predefinições.
1. Criar uma nova rede virtual **kafka secundário vnet** na **kafka-secundário-rg**, também com as predefinições.

1. Crie dois novos clusters de Kafka:

    | Nome do cluster | Grupo de Recursos | Rede virtual | Conta de Armazenamento |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Crie peerings de rede virtual. Este passo irá criar dois peerings: um da **kafka-principal-vnet** para **kafka secundário vnet** e uma cópia do **kafka secundário vnet** para  **kafka-principal-vnet**.
    1. Selecione o **kafka-principal-vnet** rede virtual.
    1. Clique em **Peerings** sob **definições**.
    1. Clique em **Adicionar**.
    1. Sobre o **adicionar peering** ecrã, introduza os detalhes, conforme mostrado na captura de ecrã abaixo.

        ![Adicionar peering de vnet](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. Configure a publicidade de IP:
    1. Aceda ao dashboard do Ambari do cluster principal: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Clique em **serviços** > **Kafka**. Clique nas **configurações** separador.
    1. Adicione as seguintes linhas de configuração para a parte inferior **modelo de kafka-env** secção. Clique em **Guardar**.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Introduza uma nota o **Guardar configuração** ecrã e clique em **guardar**.
    1. Se lhe for pedido com o aviso de configuração, clique em **continuar mesmo assim**.
    1. Clique em **Ok** sobre o **guardar alterações à configuração**.
    1. Clique em **reinicie** > **reiniciar todos os afetados** no **reinício necessário** notificação. Clique em **confirmar reinício todos os**.

        ![Reinicie nós de kafka](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Configure o Kafka para escutar em todas as interfaces de rede.
    1. Mantenha-se a **configurações** separador sob **serviços** > **Kafka**. Na **mediador Kafka** secção conjunto a **serviços de escuta** propriedade `PLAINTEXT://0.0.0.0:9092`.
    1. Clique em **Guardar**.
    1. Clique em **reinicie**, e **confirmar reinício todos os**.

1. Registe os endereços IP do Mediador e Zookeeper para o cluster principal.
    1. Clique em **anfitriões** no dashboard do Ambari.
    1. Tome nota dos endereços IP para os corretores e Zookeepers. Os nós do Mediador ter **baixo** como as primeiras duas letras do nome do anfitrião e o zookeeper nós têm **zk** como as primeiras duas letras do nome do anfitrião.

        ![endereços de ip do Vista](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Repita os três passos anteriores para o segundo cluster **cluster de kafka secundário**: configurar a publicidade de IP, serviços de escuta de definir e tome nota dos endereços IP de Zookeeper e de Mediador.

## <a name="create-topics"></a>Crie tópicos

1. Ligar para o **primário** através de SSH do cluster:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** com o nome de utilizador do SSH utilizado ao criar o cluster. Substitua **NOME_DE_BASE** com o nome de base utilizado ao criar o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para criar uma variável com os hosts de Apache Zookeeper para o cluster principal. Como as cadeias de caracteres `ZOOKEEPER_IP_ADDRESS1` têm de ser substituídas com os endereços IP reais registrados anteriormente, como `10.23.0.11` e `10.23.0.7`. Se estiver a utilizar a resolução FQDN com um servidor DNS personalizado, siga [essas etapas](apache-kafka-get-started.md#getkafkainfo) obter broker e zookeeper nomes.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Para criar um tópico com o nome `testtopic`, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Utilize o seguinte comando para verificar se o tópico foi criado:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A resposta contém `testtopic`.

4. Utilize o seguinte para ver as informações do anfitrião Zookeeper para que isso (a **primário**) cluster:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Esta ação devolve informações semelhantes ao seguinte texto:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Guarde estas informações. É utilizado na secção seguinte.

## <a name="configure-mirroring"></a>Configurar o espelhamento

1. Ligar para o **secundário** cluster com uma sessão SSH diferente:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** com o nome de utilizador do SSH utilizado ao criar o cluster. Substitua **SECONDARYCLUSTER** com o nome que utilizou quando criou o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. R `consumer.properties` ficheiro é utilizado para configurar a comunicação com o **primário** cluster. Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Utilize o seguinte texto como conteúdo do `consumer.properties` ficheiro:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **PRIMARY_ZKHOSTS** com os endereços de IP do Zookeeper do **primário** cluster.

    Este ficheiro descreve as informações de consumidor para utilizar durante a leitura do cluster de Kafka primário. Para a configuração de consumidor obter mais informações, consulte [configurações de consumidor](https://kafka.apache.org/documentation#consumerconfigs) em kafka.apache.org.

    Para guardar o ficheiro, utilize **Ctrl + X**, **Y**e, em seguida **Enter**.

3. Antes de configurar o produtor que comunica com o cluster secundário, de uma variável para os endereços IP de Mediador da configuração do **secundário** cluster. Utilize os seguintes comandos para criar esta variável:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    O comando `echo $SECONDARY_BROKERHOSTS` deve devolver informações semelhantes ao seguinte texto:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. R `producer.properties` ficheiro é utilizado para comunicar o **secundário** cluster. Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Utilize o seguinte texto como conteúdo do `producer.properties` ficheiro:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Substitua **SECONDARY_BROKERHOSTS** com os endereços IP de Mediador utilizados no passo anterior.

    Para mais informações produtor de configuração, consulte [configurações de produtor](https://kafka.apache.org/documentation#producerconfigs) em kafka.apache.org.

5. Utilize os seguintes comandos para criar uma variável de ambiente com os endereços IP dos anfitriões Zookeeper para o cluster secundário:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. A configuração predefinida para o Kafka no HDInsight não permite a criação automática de tópicos. Tem de utilizar uma das seguintes opções antes de iniciar o processo de espelhamento:

    * **Criar os tópicos no cluster secundário**: Esta opção permite-lhe definir o número de partições e o fator de replicação.

        Pode criar tópicos antes do tempo com o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Substitua `testtopic` com o nome do tópico para criar.

    * **Configurar o cluster para a criação de tópico automática**: Esta opção permite MirrorMaker criar automaticamente os tópicos, no entanto, ele poderá criá-los com um número de partições ou o fator de replicação que o tópico principal diferente.

        Para configurar o cluster secundário, a criação automática de tópicos, execute estes passos:

        1. Aceda ao dashboard do Ambari do cluster secundário: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Clique em **serviços** > **Kafka**. Clique nas **configurações** separador.
        5. Na __filtro__ , insira um valor de `auto.create`. Este procedimento filtra a lista de propriedades e apresenta o `auto.create.topics.enable` definição.
        6. Alterar o valor de `auto.create.topics.enable` como true e, em seguida, selecione __guardar__. Adicionar uma nota e, em seguida, selecione __guardar__ novamente.
        7. Selecione o __Kafka__ serviço, selecione __reiniciar__e, em seguida, selecione __reiniciar as__. Quando lhe for pedido, selecione __confirmar reinicie todos__.

        ![configurar a criação automática de tópico](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Iniciar o MirrorMaker

1. A ligação de SSH para o **secundário** do cluster, utilize o seguinte comando para iniciar o processo de MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros utilizados neste exemplo são:

    * **--consumer.config**: Especifica o ficheiro que contém as propriedades de consumidor. Essas propriedades são utilizadas para criar um consumidor que lê a partir da *primário* cluster do Kafka.

    * **--producer.config**: Especifica o ficheiro que contém as propriedades de produtor. Essas propriedades são utilizadas para criar um produtor que escreve para o *secundário* cluster do Kafka.

    * **--whitelist**: Uma lista de tópicos que MirrorMaker replica do cluster principal para o secundário.

    * **--num.streams**: O número de threads de consumidor para criar.

    O consumidor no nó secundário agora está à espera para receber mensagens.

2. A ligação de SSH para o **primário** do cluster, utilize o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando chegar a uma linha em branco com um cursor, escreva algumas mensagens de texto. As mensagens são enviadas para o tópico o **primário** cluster. Quando terminar, utilize **Ctrl + C** para terminar o processo de produtor.

3. A ligação de SSH para o **secundário** do cluster, utilize **Ctrl + C** para terminar o processo de MirrorMaker. Pode demorar vários segundos para terminar o processo. Para verificar que as mensagens foram replicadas para o secundário, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A lista de tópicos inclui agora `testtopic`, qual é criado quando MirrorMaster Espelha o tópico do cluster principal para o secundário. As mensagens obtidas a partir do tópico são os mesmos que aqueles que introduziu no cluster principal.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Os passos neste documento criado clusters em grupos de recursos do Azure diferente. Para eliminar todos os recursos que criou, pode eliminar os grupos de dois recursos criados: **kafka-principal-rg** e **kafka secondary_rg**. Eliminar os grupos de recursos remove todos os recursos criados através dos seguintes neste documento, incluindo clusters, redes virtuais e contas de armazenamento.

## <a name="next-steps"></a>Próximos Passos

Neste documento, aprendeu a usar [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para criar uma réplica de um [Apache Kafka](https://kafka.apache.org/) cluster. Utilize as seguintes ligações para descobrir outras maneiras de trabalhar com o Kafka:

* [Documentação do Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) em cwiki.apache.org.
* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Utilizar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utilizar o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ligar ao Apache Kafka através de uma rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)

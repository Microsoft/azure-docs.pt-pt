---
title: Temas do Mirror Apache Kafka - Azure HDInsight
description: Aprenda a usar a função de espelhamento de Apache Kafka para manter uma réplica de um kafka no cluster HDInsight, espelhando tópicos para um cluster secundário.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 633f01d813fe4e6c56d88052cbc7440c43f350dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870505"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Use MirrorMaker to replicate Apache Kafka topics with Kafka on HDInsight (Utilizar o MirrorMaker para replicar tópicos do Apache Kafka com o Kafka no HDInsight)

Aprenda a usar a função de espelhamento de Apache Kafka para replicar tópicos para um cluster secundário. O espelhamento pode ser funcionado como um processo contínuo, ou usado intermitentemente como um método de migração de dados de um cluster para outro.

> [!NOTE]
> Este artigo contém referências ao termo *whitelist*, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.

Neste exemplo, o espelhamento é usado para replicar tópicos entre dois clusters HDInsight. Ambos os clusters estão em diferentes redes virtuais em diferentes datacenters.

> [!WARNING]  
> A espelhamento não deve ser considerada como um meio para alcançar a tolerância à falha. A compensação para itens dentro de um tópico são diferentes entre os clusters primário e secundário, para que os clientes não possam usar os dois intercambiavelmente.
>
> Se está preocupado com a tolerância a falhas, deve definir a replicação para os tópicos dentro do seu cluster. Para mais informações, consulte [Começar com Apache Kafka na HDInsight.](apache-kafka-get-started.md)

## <a name="how-apache-kafka-mirroring-works"></a>Como funciona o espelhamento de Apache Kafka

O espelhamento funciona utilizando a ferramenta [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (parte de Apache Kafka) para consumir registos de tópicos no cluster primário e, em seguida, criar uma cópia local no cluster secundário. O MirrorMaker usa um (ou mais) *consumidores* que lêem do cluster primário, e um *produtor* que escreve para o cluster local (secundário).

A configuração de espelhamento mais útil para a recuperação de desastres utiliza clusters Kafka em diferentes regiões de Azure. Para isso, as redes virtuais onde residem os clusters são espreitadas em conjunto.

O diagrama a seguir ilustra o processo de espelhamento e como a comunicação flui entre clusters:

:::image type="content" source="./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png" alt-text="Diagrama do processo de espelhamento" border="false":::

Os aglomerados primários e secundários podem ser diferentes no número de nós e divisórias, e as compensações dentro dos tópicos também são diferentes. O espelhamento mantém o valor-chave que é utilizado para a partilha, pelo que a ordem de registo é preservada numa base por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhando através dos limites da rede

Se precisar de se espelhar entre clusters kafka em diferentes redes, existem as seguintes considerações adicionais:

* **Gateways**: As redes devem poder comunicar ao nível TCP/IP.

* **Endereço do servidor**: Pode optar por endereçar os seus nós de cluster utilizando os seus endereços IP ou nomes de domínio totalmente qualificados.

    * **Endereços IP**: Se configurar os seus clusters Kafka para utilizar a publicidade de endereço IP, pode proceder com a configuração de espelhamento utilizando os endereços IP dos nós de corretor e dos nós do zookeeper.
    
    * **Nomes de domínio**: Se não configurar os seus clusters Kafka para publicidade de endereços IP, os clusters devem ser capazes de se conectar uns com os outros utilizando nomes de domínio totalmente qualificados (FQDNs). Isto requer um servidor do Sistema de Nome de Domínio (DNS) em cada rede que esteja configurado para encaminhar pedidos para as outras redes. Ao criar uma Rede Virtual Azure, em vez de utilizar o DNS automático fornecido com a rede, deve especificar um servidor DNS personalizado e o endereço IP para o servidor. Depois de a Rede Virtual ter sido criada, deve então criar uma Máquina Virtual Azure que utilize esse endereço IP e, em seguida, instale e configuure o software DNS nele.

    > [!WARNING]  
    > Crie e configuure o servidor DNS personalizado antes de instalar o HDInsight na Rede Virtual. Não é necessária nenhuma configuração adicional para que o HDInsight utilize o servidor DNS configurado para a Rede Virtual.

Para obter mais informações sobre a ligação de duas Redes Virtuais Azure, consulte [configurar uma ligação VNet-to-VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Arquitetura espelhada

Esta arquitetura apresenta dois clusters em diferentes grupos de recursos e redes virtuais: um **primário** e **secundário.**

### <a name="creation-steps"></a>Passos de criação

1. Criar dois novos grupos de recursos:

    |Grupo de Recursos | Localização |
    |---|---|
    | kafka-primary-rg | E.U.A. Central |
    | kafka-secundário-rg | E.U.A. Centro-Norte |

1. Crie uma nova rede virtual **kafka-primary-vnet** em **kafka-primary-rg**. Deixe as definições predefinidos.
1. Crie uma nova rede virtual **kafka-secondary-vnet** em **kafka-secondary-rg**, também com configurações padrão.

1. Criar dois novos clusters Kafka:

    | Nome do cluster | Grupo de Recursos | Rede Virtual | Conta de Armazenamento |
    |---|---|---|---|
    | kafka-primário-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secundário-cluster | kafka-secundário-rg | kafka-secundário-vnet | kafkasecondarystorage |

1. Crie os seus olhos de rede virtuais. Este passo criará dois pares: um de **kafka-primary-vnet** a **kafka-secondary-vnet** e outro de volta de **kafka-secondary-vnet** a **kafka-primary-vnet**.
    1. Selecione a rede virtual **kafka-primar-vnet.**
    1. **Selecione Peerings** em **Definições**.
    1. Selecione **Adicionar**.
    1. No ecrã **de observação Add,** insira os detalhes como mostrado na imagem abaixo.

        :::image type="content" source="./media/apache-kafka-mirroring/hdi-add-vnet-peering.png" alt-text="HDInsight Kafka adicionar vnet peering" border="true":::

### <a name="configure-ip-advertising"></a>Configure publicidade IP

Configure a publicidade IP para permitir que um cliente se conecte usando endereços IP corretor em vez de nomes de domínio.

1. Vá ao painel Ambari para o cluster primário: `https://PRIMARYCLUSTERNAME.azurehdinsight.net` .
1. Selecione **Serviços**  >  **Kafka**. Selecione o **separador Configs.**
1. Adicione as seguintes linhas config à secção de **modelo kafka-env** inferior. Selecione **Guardar**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Introduza uma nota no ecrã **de Configuração de Salvamento** e clique em **Guardar**.
1. Se for solicitado com aviso de configuração, clique em **Proceder de qualquer forma**.
1. Selecione **Ok** nas **alterações de configuração de poupança**.
1. Selecione **reiniciar**  >  **tudo o que é afetado** na notificação necessária para **reiniciar.** **Selecione Confirmar Reiniciar tudo**.

    :::image type="content" source="./media/apache-kafka-mirroring/ambari-restart-notification.png" alt-text="Apache Ambari reinicia todos os afetados" border="true":::

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configure a Kafka para ouvir em todas as interfaces de rede.
    
1. Mantenha-se no **separador Configs** em **Serviços**  >  **Kafka**. Na secção **Kafka Broker** definir a propriedade dos **ouvintes** para `PLAINTEXT://0.0.0.0:9092` .
1. Selecione **Guardar**.
1. **Selecione Reiniciar** e **confirmar reiniciar tudo**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Endereços IP do Corretor de Registos e endereços zookeeper para cluster primário.

1. Selecione **Anfitriões** no painel Ambari.
1. Tome nota dos endereços IP para os Corretores e Zookeepers. Os nós corretores têm **wn** como as duas primeiras letras do nome hospedeiro, e os nós do zookeeper têm **zk** como as duas primeiras letras do nome hospedeiro.

    :::image type="content" source="./media/apache-kafka-mirroring/view-node-ip-addresses2.png" alt-text="Apache Ambari visualiza endereços ip de nó" border="true":::

1. Repita os três passos anteriores para o segundo **cluster kafka-secundário-cluster**: configurar a publicidade IP, definir os ouvintes e tomar nota dos endereços IP do Corretor e zookeeper.

## <a name="create-topics"></a>Criar tópicos

1. Ligue-se ao cluster **primário** utilizando SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua o **sshuser** pelo nome de utilizador SSH utilizado na criação do cluster. Substitua **o PRIMARYCLUSTER** pelo nome base utilizado na criação do cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Use o seguinte comando para criar uma variável com os hospedeiros Apache Zookeeper para o cluster primário. As cordas devem `ZOOKEEPER_IP_ADDRESS1` ser substituídas pelos endereços IP reais registados anteriormente, tais como `10.23.0.11` `10.23.0.7` . Se estiver a utilizar a resolução FQDN com um servidor DNS personalizado, siga [estes passos](apache-kafka-get-started.md#getkafkainfo) para obter nomes de corretor e zookeeper.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Para criar um tópico chamado `testtopic` , use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Utilize o seguinte comando para verificar se o tópico foi criado:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A resposta `testtopic` contém.

1. Utilize o seguinte para visualizar as informações do anfitrião do Zookeeper para este cluster (o **primário):**

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Esta informação devolve informações semelhantes ao seguinte texto:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Guarde esta informação. É usado na próxima secção.

## <a name="configure-mirroring"></a>Espelhamento configurante

1. Ligue-se ao cluster **secundário** utilizando uma sessão SSH diferente:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Substitua o **sshuser** pelo nome de utilizador SSH utilizado na criação do cluster. Substitua **o SECONDARYCLUSTER** pelo nome utilizado na criação do cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Um `consumer.properties` ficheiro é usado para configurar a comunicação com o cluster **primário.** Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Utilize o seguinte texto como o conteúdo do `consumer.properties` ficheiro:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **PRIMARY_ZKHOSTS** pelos endereços IP do Zookeeper do cluster **primário.**

    Este ficheiro descreve as informações do consumidor a utilizar ao ler do cluster primário de Kafka. Para obter mais informações sobre a configuração do consumidor, consulte [a Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) em kafka.apache.org.

    Para guardar o ficheiro, utilize **Ctrl + X,** **Y,** e, em seguida, **Insira**.

1. Antes de configurar o produtor que comunica com o cluster secundário, crie uma variável para os endereços IP corretor do cluster **secundário.** Utilize os seguintes comandos para criar esta variável:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    O comando `echo $SECONDARY_BROKERHOSTS` deve devolver informações semelhantes ao seguinte texto:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Um `producer.properties` ficheiro é usado para comunicar o aglomerado **secundário.** Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Utilize o seguinte texto como o conteúdo do `producer.properties` ficheiro:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Substitua **SECONDARY_BROKERHOSTS** pelos endereços IP do corretor utilizados no passo anterior.

    Para obter mais informações, consulte [a Produtora Configs](https://kafka.apache.org/documentation#producerconfigs) em kafka.apache.org.

1. Utilize os seguintes comandos para criar uma variável ambiental com os endereços IP dos anfitriões zookeeper para o cluster secundário:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. A configuração padrão para Kafka no HDInsight não permite a criação automática de tópicos. Deve utilizar uma das seguintes opções antes de iniciar o processo de espelhamento:

    * **Crie os tópicos no cluster secundário**: Esta opção permite-lhe também definir o número de divisórias e o fator de replicação.

        Pode criar tópicos com antecedência utilizando o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        `testtopic`Substitua-o pelo nome do tópico a criar.

    * **Configure o cluster para a criação automática** de tópicos : Esta opção permite ao MirrorMaker criar automaticamente tópicos, no entanto pode criá-los com um número diferente de divisões ou fator de replicação do que o tópico primário.

        Para configurar o cluster secundário para criar automaticamente tópicos, execute estes passos:

        1. Vá ao tablier Ambari para o agrupamento secundário: `https://SECONDARYCLUSTERNAME.azurehdinsight.net` .
        1. Clique **em Serviços**  >  **Kafka**. Clique no **separador Configs.**
        1. No campo __Filtro,__ insira um valor de `auto.create` . Isto filtra a lista de propriedades e apresenta a `auto.create.topics.enable` definição.
        1. Mude o valor de `auto.create.topics.enable` verdade e, em seguida, selecione __Guardar__. Adicione uma nota e, em seguida, __selecione Guardar__ novamente.
        1. Selecione o serviço __Kafka,__ __selecione Restart__ e, em seguida, selecione __Reiniciar todos os afetados__. Quando solicitado, __selecione Confirme reiniciar tudo__.

        :::image type="content" source="./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png" alt-text="kafka permitir criar automaticamente tópicos" border="true":::

## <a name="start-mirrormaker"></a>Iniciar MirrorMaker

1. Da ligação SSH ao cluster **secundário,** utilize o seguinte comando para iniciar o processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros utilizados neste exemplo são:

    |Parâmetro |Descrição |
    |---|---|
    |--consumer.config|Especifica o ficheiro que contém propriedades de consumidores. Estas propriedades são usadas para criar um consumidor que lê a partir do cluster *primário* de Kafka.|
    |--producer.config|Especifica o ficheiro que contém propriedades do produtor. Estas propriedades são usadas para criar um produtor que escreve para o cluster *kafka secundário.*|
    |--whitelist|Uma lista de tópicos que o MirrorMaker replica do cluster primário para o secundário.|
    |--num.streams|O número de fios de consumo para criar.|

    O consumidor do nó secundário está agora à espera de receber mensagens.

2. Da ligação SSH ao cluster **primário,** utilize o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando chegar a uma linha em branco com um cursor, escreva algumas mensagens de texto. As mensagens são enviadas para o tópico no aglomerado **primário.** Quando terminar, utilize **ctrl + C** para terminar o processo de produtor.

3. Da ligação SSH ao cluster **secundário,** utilize **ctrl + C** para terminar o processo MirrorMaker. Pode levar alguns segundos para terminar o processo. Para verificar se as mensagens foram replicadas para o secundário, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A lista de tópicos agora inclui `testtopic` , que é criado quando o MirrorMaster espelha o tópico do cluster primário para o secundário. As mensagens recuperadas do tópico são as mesmas que inseriu no cluster primário.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Os passos neste documento criaram clusters em diferentes grupos de recursos Azure. Para eliminar todos os recursos criados, pode eliminar os dois grupos de recursos criados: **kafka-primary-rg** e **kafka-secondary_rg**. A eliminação dos grupos de recursos remove todos os recursos criados seguindo este documento, incluindo clusters, redes virtuais e contas de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar [o MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para criar uma réplica de um cluster [Apache Kafka.](https://kafka.apache.org/) Use os seguintes links para descobrir outras formas de trabalhar com Kafka:

* [Documentação apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.apache.org.
* [As melhores práticas do Fabricante de Espelhos Kafka](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Começa com o Apache Kafka na HDInsight](apache-kafka-get-started.md)
* [Use Apache Spark com Apache Kafka em HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Conecte-se a Apache Kafka através de uma Rede Virtual Azure](apache-kafka-connect-vpn-gateway.md)

---
title: Espelhar tópicos do Apache Kafka - Azure HDInsight
description: Saiba como utilizar a funcionalidade de espelhamento do Apache Kafka para manter uma réplica de um cluster do Kafka no HDInsight ao espelhamento tópicos para um cluster secundário.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 0c37ad6de867c4abe4ebf0e6c7a40b5cf27c4541
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079644"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Utilizar MirrorMaker para replicar tópicos do Apache Kafka com o Kafka no HDInsight

Aprenda a usar o recurso de espelhamento do Apache Kafka para replicar tópicos para um cluster secundário. Espelhamento podem ser executadas como um processo contínuo ou intermitentemente utilizado como um método de migração de dados de um cluster para outro.

Neste exemplo, o espelhamento é utilizado para replicar tópicos entre dois clusters do HDInsight. Ambos os clusters estão numa rede Virtual do Azure na mesma região.

> [!WARNING]  
> Espelhamento não deve ser considerado como um meio para alcançar a tolerância a falhas. O deslocamento para itens dentro de um tópico são diferentes entre os clusters de origem e de destino, para que os clientes não podem utilizar os dois alternadamente.
>
> Se estiver preocupado com tolerância a falhas, deve definir os replicação para os tópicos no seu cluster. Para obter mais informações, consulte [introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Como funciona o espelhamento do Apache Kafka

Espelhamento funciona utilizando o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ferramenta (parte do Apache Kafka) para consumir registos dos tópicos no cluster de origem e, em seguida, criar uma cópia local no cluster de destino. MirrorMaker utiliza uma (ou mais) *consumidores* de leitura a partir do cluster de origem e um *produtor* que escreve para o cluster local (destino).

O diagrama seguinte ilustra o processo de espelhamento:

![Diagrama do processo de espelhamento](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka no HDInsight não fornece acesso ao serviço Kafka através da internet pública. Os produtores de Kafka ou consumidores tem de estar na mesma rede virtual do Azure como nós do cluster do Kafka. Neste exemplo, tanto as origem e destino clusters do Kafka estão localizados numa rede virtual do Azure. O diagrama seguinte mostra como a comunicação flui entre os clusters:

![Diagrama de origem e destino Kafka clusters numa rede virtual do Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Os clusters de origem e de destino podem ser diferentes do número de nós e partições e deslocamentos dentro os tópicos são também diferentes. Espelhamento mantém o valor da chave que é utilizado para criação de partições, para que o pedido de registo é preservado numa base por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhamento em limites de rede

Se precisar de espelhar entre clusters do Kafka em redes diferentes, existem as seguintes considerações adicionais:

* **Gateways**: As redes tem de ser capazes de comunicar no nível de TCPIP.

* **Resolução de nomes**: Os clusters de Kafka em cada rede tem de conseguir ligar-se entre si utilizando nomes de anfitrião. Isso pode exigir um servidor de sistema de nomes de domínio (DNS) em cada rede que está configurada para encaminhar pedidos para as outras redes.

    Ao criar uma rede Virtual do Azure, em vez de utilizar o DNS automática fornecido com a rede, tem de especificar um servidor DNS personalizado e o endereço IP para o servidor. Depois de ter sido criada a rede Virtual, tem, em seguida, criar uma Máquina Virtual que utilize esse endereço IP, em seguida, instalar e configurar software DNS no mesmo.

    > [!WARNING]  
    > Criar e configurar o servidor DNS personalizado antes de instalar HDInsight numa rede Virtual. Não existe nenhuma configuração adicional necessária para o HDInsight utilizar o servidor DNS configurado para a rede Virtual.

Para obter mais informações sobre como ligar duas redes virtuais do Azure, consulte [configurar uma ligação VNet a VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-apache-kafka-clusters"></a>Criar clusters do Apache Kafka

Embora pode criar uma rede virtual do Azure e clusters do Kafka manualmente, é mais fácil de usar um modelo Azure Resource Manager. Utilize os seguintes passos para implementar uma rede virtual do Azure e dois clusters de Kafka à sua subscrição do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]  
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster do Kafka que contém três nós de trabalho.

2. Utilize as seguintes informações para preencher as entradas a **implementação personalizada** painel:
    
    ![Implantação personalizada do HDInsight](./media/apache-kafka-mirroring/parameters.png)
    
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Este grupo contém o cluster do HDInsight.

    * **Localização**: Selecione um local geograficamente próximo de.
     
    * **Nome do Cluster de base**: Este valor é utilizado como o nome de base para os clusters do Kafka. Por exemplo, introduzir **hdi** cria clusters com o nome **origem hdi** e **dest hdi**.

    * **Nome de utilizador de início de sessão do cluster**: O nome de utilizador de administrador para a origem e destino clusters do Kafka.

    * **Palavra-passe de início de sessão do cluster**: A palavra-passe de utilizador do administrador para a origem e destino clusters do Kafka.

    * **Nome de utilizador SSH**: O utilizador SSH para criar para a origem e destino Kafka clusters.

    * **SSH palavra-passe**: A palavra-passe do utilizador SSH para a origem e destino clusters do Kafka.

3. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

4. Por fim, marque **Afixar ao dashboard** e, em seguida, selecione **Compra**. Demora cerca de 20 minutos para criar os clusters.

> [!IMPORTANT]  
> O nome dos HDInsight clusters estão **origem NOME_DE_BASE** e **dest NOME_DE_BASE**, onde NOME_DE_BASE é o nome que indicou para o modelo. Utilize esses nomes em passos posteriores ao estabelecer ligação com os clusters.

## <a name="create-topics"></a>Crie tópicos

1. Ligar para o **origem** através de SSH do cluster:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** com o nome de utilizador do SSH utilizado ao criar o cluster. Substitua **NOME_DE_BASE** com o nome de base utilizado ao criar o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize os seguintes comandos para encontrar os anfitriões de Apache Zookeeper para o cluster de origem:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de origem. Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster.

3. Para criar um tópico com o nome `testtopic`, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Utilize o seguinte comando para verificar se o tópico foi criado:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    A resposta contém `testtopic`.

4. Utilize o seguinte para ver as informações do anfitrião Zookeeper para que isso (a **origem**) cluster:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Esta ação devolve informações semelhantes ao seguinte texto:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Guarde estas informações. É utilizado na secção seguinte.

## <a name="configure-mirroring"></a>Configurar o espelhamento

1. Ligar para o **destino** cluster com uma sessão SSH diferente:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** com o nome de utilizador do SSH utilizado ao criar o cluster. Substitua **NOME_DE_BASE** com o nome de base utilizado ao criar o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. R `consumer.properties` ficheiro é utilizado para configurar a comunicação com o **origem** cluster. Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Utilize o seguinte texto como conteúdo do `consumer.properties` ficheiro:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **SOURCE_ZKHOSTS** com as informações de anfitriões Zookeeper do **origem** cluster.

    Este ficheiro descreve as informações de consumidor para utilizar durante a leitura a partir da origem de cluster do Kafka. Para a configuração de consumidor obter mais informações, consulte [configurações de consumidor](https://kafka.apache.org/documentation#consumerconfigs) em kafka.apache.org.

    Para guardar o ficheiro, utilize **Ctrl + X**, **Y**e, em seguida **Enter**.

3. Antes de configurar o produtor que comunica com o cluster de destino, tem de encontrar o Mediador de anfitriões para o **destino** cluster. Utilize os seguintes comandos para obter estas informações:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de destino. Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster.

    O `echo` comando devolve informações semelhantes ao seguinte texto:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. R `producer.properties` ficheiro é utilizado para comunicar o __destino__ cluster. Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Utilize o seguinte texto como conteúdo do `producer.properties` ficheiro:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Substitua **DEST_BROKERS** com as informações de Mediador do passo anterior.

    Para mais informações produtor de configuração, consulte [configurações de produtor](https://kafka.apache.org/documentation#producerconfigs) em kafka.apache.org.

5. Utilize os seguintes comandos para encontrar os anfitriões Zookeeper para o cluster de destino:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de destino. Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster.

7. A configuração predefinida para o Kafka no HDInsight não permite a criação automática de tópicos. Tem de utilizar uma das seguintes opções antes de iniciar o processo de espelhamento:

    * **Criar os tópicos no cluster de destino**: Esta opção permite-lhe definir o número de partições e o fator de replicação.

        Pode criar tópicos antes do tempo com o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Substitua `testtopic` com o nome do tópico para criar.

    * **Configurar o cluster para a criação de tópico automática**: Esta opção permite MirrorMaker criar automaticamente os tópicos, no entanto, ele poderá criá-los com um número diferente de partições ou o fator de replicação que o tópico de origem.

        Para configurar o cluster de destino para criar automaticamente os tópicos, execute estes passos:

        1. Partir do [portal do Azure](https://portal.azure.com), selecione o destino de cluster do Kafka.
        2. Desde a descrição geral do cluster, selecione __dashboard de clusters__. Em seguida, selecione __dashboard de clusters do HDInsight__. Quando lhe for pedido, autentique com as credenciais de início de sessão (admin) para o cluster.
        3. Selecione o __Kafka__ serviço a partir da lista à esquerda da página.
        4. Selecione __configurações__ no meio da página.
        5. Na __filtro__ , insira um valor de `auto.create`. Este procedimento filtra a lista de propriedades e apresenta o `auto.create.topics.enable` definição.
        6. Alterar o valor de `auto.create.topics.enable` como true e, em seguida, selecione __guardar__. Adicionar uma nota e, em seguida, selecione __guardar__ novamente.
        7. Selecione o __Kafka__ serviço, selecione __reiniciar__e, em seguida, selecione __reiniciar as__. Quando lhe for pedido, selecione __confirmar reinicie todos__.

## <a name="start-mirrormaker"></a>Iniciar o MirrorMaker

1. A ligação de SSH para o **destino** do cluster, utilize o seguinte comando para iniciar o processo de MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros utilizados neste exemplo são:

    * **--consumer.config**: Especifica o ficheiro que contém as propriedades de consumidor. Essas propriedades são utilizadas para criar um consumidor que lê a partir da *origem* cluster do Kafka.

    * **--producer.config**: Especifica o ficheiro que contém as propriedades de produtor. Essas propriedades são utilizadas para criar um produtor que escreve para o *destino* cluster do Kafka.

    * **--whitelist**: Uma lista de tópicos que MirrorMaker replica a partir do cluster de origem para o destino.

    * **--num.streams**: O número de threads de consumidor para criar.

   Na inicialização, MirrorMaker devolve informações semelhantes ao seguinte texto:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. A ligação de SSH para o **origem** do cluster, utilize o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de origem. Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster.

     Quando chegar a uma linha em branco com um cursor, escreva algumas mensagens de texto. As mensagens são enviadas para o tópico o **origem** cluster. Quando terminar, utilize **Ctrl + C** para terminar o processo de produtor.

3. A ligação de SSH para o **destino** do cluster, utilize **Ctrl + C** para terminar o processo de MirrorMaker. Pode demorar vários segundos para terminar o processo. Para verificar que as mensagens foram replicadas para o destino, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de destino. Quando lhe for pedido, introduza a palavra-passe para o início de sessão (administrador) do cluster.

    A lista de tópicos inclui agora `testtopic`, qual é criado quando MirrorMaster Espelha o tópico do cluster de origem para o destino. As mensagens obtidas a partir do tópico são os mesmos tal como foi introduzido no cluster de origem.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Uma vez que os passos neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, pode eliminar o grupo de recursos no portal do Azure. A eliminar o grupo de recursos remove todos os recursos criados através dos seguintes deste documento, a rede Virtual do Azure e conta de armazenamento utilizada pelos clusters.

## <a name="next-steps"></a>Próximos Passos

Neste documento, aprendeu a usar [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para criar uma réplica de um [Apache Kafka](https://kafka.apache.org/) cluster. Utilize as seguintes ligações para descobrir outras maneiras de trabalhar com o Kafka:

* [Documentação do Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) em cwiki.apache.org.
* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Utilizar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utilizar o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ligar ao Apache Kafka através de uma rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)

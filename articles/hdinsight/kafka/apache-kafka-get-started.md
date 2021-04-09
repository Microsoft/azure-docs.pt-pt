---
title: 'Quickstart: Configurar Apache Kafka em HDInsight usando o portal Azure'
description: Neste guia de início rápido, irá saber como criar um cluster do Apache Kafka no Azure HDInsight através do portal do Azure. Também irá saber mais sobre tópicos, subscritores e consumidores do Kafka.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: acb497b2f0111d36650ec415c8f1f580d116b55e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863297"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Criar cluster Apache Kafka em Azure HDInsight usando o portal Azure

[Apache Kafka](./apache-kafka-introduction.md) é uma plataforma de streaming distribuída e de código aberto. É frequentemente utilizado como mediador de mensagens, uma vez que fornece funcionalidades semelhantes a uma fila de mensagens de publicação-subscrição.

Neste Quickstart, aprende-se a criar um cluster Apache Kafka utilizando o portal Azure. Também vai saber como utilizar utilitários incluídos para enviar e receber mensagens com o Apache Kafka. Para obter explicações aprofundadas sobre as configurações disponíveis, consulte [Configurar clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter informações adicionais sobre a utilização do portal para criar clusters, consulte [Criar clusters no portal.](../hdinsight-hadoop-create-linux-clusters-portal.md)

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Só os recursos dentro da mesma rede virtual podem aceder à API do Apache Kafka. Neste Quickstart, acede diretamente ao cluster utilizando sSH. Para ligar outros serviços, redes ou máquinas virtuais ao Apache Kafka, tem primeiro de criar uma rede virtual e, em seguida, criar os recursos dentro da rede. Para obter mais informações, veja o documento [Ligar ao Apache Kafka com uma rede virtual](apache-kafka-connect-vpn-gateway.md). Para obter informações mais gerais sobre o planeamento de redes virtuais para HDInsight, consulte [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Criar um cluster do Apache Kafka

Para criar um cluster Apache Kafka em HDInsight, utilize os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu superior, selecione **+ Crie um recurso.**

    :::image type="content" source="./media/apache-kafka-get-started/azure-portal-create-resource.png" alt-text="Portal Azure criar recurso HDInsight" border="true":::

1. Selecione **Analytics**  >  **Azure HDInsight** para ir à página de **cluster Create HDInsight.**

1. A partir **do separador Básicos,** forneça as seguintes informações:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscrição    |  A partir da lista de drop-down, selecione a subscrição Azure que é usada para o cluster. |
    |Grupo de recursos     | Crie um grupo de recursos ou selecione crie um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |Nome do cluster   | Introduza um nome globalmente exclusivo. O nome pode consistir em até 59 caracteres, incluindo letras, números e hífenes. O primeiro e último carateres do nome não podem ser hífenes. |
    |Region    | A partir da lista de drop-down, selecione uma região onde o cluster é criado.  Escolha uma região mais próxima de si para um melhor desempenho. |
    |Tipo de cluster| **Selecione Selecionar o tipo de cluster** para abrir uma lista. A partir da lista, selecione **Kafka** como o tipo de cluster.|
    |Versão|A versão padrão para o tipo de cluster será especificada. Selecione a partir da lista de drop-down se desejar especificar uma versão diferente.|
    |Nome de utilizador e palavra-passe de início de sessão do cluster    | O nome de login predefinido é **administrador**. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula, e uma letra minúscula, um carácter não alfanumérico (exceto os caracteres ' \) . Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |Nome de utilizador de Secure Shell (SSH) | O nome de utilizador predefinido é **sshuser**.  Pode indicar outro nome de utilizador SSH. |
    |Use a palavra-passe de login do cluster para SSH| Selecione esta caixa de verificação para utilizar a mesma palavra-passe para o utilizador SSH que forneceu para o utilizador de login do cluster.|

   :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-basics.png" alt-text="Portal Azure cria básicos de cluster" border="true":::

    Cada região do Azure (localização) fornece _domínios de falha_. Um domínio de falha é um agrupamento lógico de hardware subjacente num centro de dados do Azure. Cada domínio de falha partilha um comutador de rede e uma fonte de alimentação. As máquinas virtuais e os discos geridos que implementam os nós num cluster HDInsight são distribuídos por esses domínios de falha. Esta arquitetura limita o possível impacto de falhas físicas de hardware.

    Para obter uma elevada disponibilidade de dados, selecione uma região (localização) que contenha __três domínios de falha__. Para obter informações sobre o número de domínios de falha numa região, consulte o documento [Disponibilidade das máquinas virtuais Linux](../../virtual-machines/availability.md).

    Selecione o **Seguinte: Separador de >>de armazenamento** para avançar para as definições de armazenamento.

1. A partir do separador **Armazenamento,** forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Tipo de armazenamento primário|Utilize o valor predefinido **Azure Storage**.|
    |Método de seleção|Utilizar o valor predefinido **Selecione da lista**.|
    |Conta de armazenamento primária|Utilize a lista de drop-down para selecionar uma conta de armazenamento existente ou selecione **Criar novo**. Se criar uma nova conta, o nome deve ter entre 3 e 24 caracteres de comprimento, e pode incluir apenas números e letras minúsculas|
    |Contentor|Utilize o valor auto-povoado.|

    :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-storage.png " alt-text="HDInsight Linux começa a fornecer valores de armazenamento de cluster" border="true":::

    Selecione o separador **Segurança + rede.**

1. Para este Quickstart, deixe as definições de segurança predefinidos. Para saber mais sobre o pacote de Segurança Enterprise, visite [Configurar um cluster do HDInsight com Pacote de Segurança Enterprise com o Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Para aprender a usar a sua própria chave para encriptação de disco Apache Kafka, visite [a encriptação do disco gerido pelo Cliente](../disk-encryption.md)

   Se quiser ligar o seu cluster a uma rede virtual, selecione uma rede virtual na lista pendente **Rede Virtual**.

   :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png" alt-text="Adicionar o cluster a uma rede virtual" border="true":::

    Selecione o **separador configuração + preços.**

1. Para garantir a disponibilidade de Apache Kafka em HDInsight, o __número de__ entradas de nós para nó de **trabalhador** deve ser definido para 3 ou superior. O valor predefinido é 4.

    Os discos Standard por entrada **no nó de trabalhador** configuram a escalabilidade de Apache Kafka em HDInsight. O Apache Kafka no HDInsight utiliza o disco local das máquinas virtuais no cluster para armazenar dados. O Apache Kafka recebe um fluxo intensivo de dados de E/S, pelo que o [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md) é utilizado para garantir um elevado débito e uma maior capacidade de armazenamento por nó. O tipo de disco gerido pode ser __Standard__ (HDD) ou __Premium__ (SSD). O tipo de disco depende do tamanho da VM utilizado pelos nós de trabalho (mediadores do Apache Kafka). Os discos Premium são utilizados automaticamente com as VMs das séries DS e GS. Todos os outros tipos de VM utilizam discos Standard.

   :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png" alt-text="Definir o tamanho do cluster do Apache Kafka" border="true":::

    Selecione o **separador 'Rever +' para criar.**

1. Reveja a configuração do cluster. Altere quaisquer definições incorretas. Por fim, **selecione Criar** para criar o cluster.

    :::image type="content" source="./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png" alt-text="resumo da configuração do cluster kafka" border="true":::

    A criação do cluster pode demorar até 20 minutos.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

    Quando estiver ligado, verá informações semelhantes ao texto seguinte:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Obtenha informações sobre o hospedeiro Apache Zookeeper e Broker

Ao trabalhar com Kafka, deve conhecer os anfitriões *apache zookeeper* e *broker.* Estes anfitriões são utilizados com a API do Apache Kafka e com muitos dos utilitários que são enviados com o Kafka.

Nesta secção, obtém-se a informação do anfitrião da API Apache Ambari REST no cluster.

1. Instale [o JQ](https://stedolan.github.io/jq/), um processador JSON de linha de comando. Este utilitário é usado para analisar documentos JSON, e é útil para analisar as informações do anfitrião. A partir da ligação SSH aberta, insira o seguinte comando para `jq` instalar:

    ```bash
    sudo apt -y install jq
    ```

1. Configurar a variável de senha. `PASSWORD`Substitua-se pela palavra-passe de login do cluster e, em seguida, introduza o comando:

    ```bash
    export password='PASSWORD'
    ```

1. Extrair o nome do cluster corretamente arquivado. O invólucro real do nome do cluster pode ser diferente do que se espera, dependendo da forma como o cluster foi criado. Este comando obterá o invólucro real e, em seguida, armazená-lo-á numa variável. Introduza o seguinte comando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Se você está fazendo este processo de fora do cluster, há um procedimento diferente para armazenar o nome do cluster. Obtenha o nome do cluster em minúsculas a partir do portal Azure. Em seguida, substitua o nome do cluster pelo `<clustername>` seguinte comando e execute-o: `export clusterName='<clustername>'` .


1. Para definir uma variável ambiental com informações de anfitrião zookeeper, use o comando abaixo. O comando recupera todos os anfitriões do Zookeeper, e depois devolve apenas as duas primeiras entradas. Isto acontece porque é desejável que exista alguma redundância para o caso de um anfitrião estar inacessível.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso a Ambari. Se o seu cluster estiver por trás de um NSG, executar este comando a partir de uma máquina que pode aceder a Ambari.

1. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Para definir uma variável de ambiente com as informações do anfitrião do mediador do Apache Kafka, utilize o comando seguinte:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requer acesso a Ambari. Se o seu cluster estiver por trás de um NSG, executar este comando a partir de uma máquina que pode aceder a Ambari.

1. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

    ```bash
    echo $KAFKABROKERS
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Gerir tópicos do Apache Kafka

O Kafka armazena fluxos de dados em *tópicos*. Pode utilizar o utilitário `kafka-topics.sh` para gerir os tópicos.

* **Para criar um tópico**, utilize o seguinte comando na ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Este comando liga ao Zookeeper com as informações do anfitrião armazenadas em `$KAFKAZKHOSTS`. Em seguida, cria um tópico do Apache Kafka denominado **test**.

    * Os dados armazenados neste tópico estão particionados em oito partições.

    * Cada partição é replicada em três nós de trabalho no cluster.

        * Se tiver criado o cluster numa região do Azure que fornece três domínios de falha, utilize um fator de replicação de 3. Caso contrário, utilize um fator de replicação de 4.
        
        * Nas regiões com três domínios de falha, um fator de replicação de 3 permite que as réplicas sejam distribuídas pelos domínios de falha. Nas regiões com dois domínios de falha, um fator de replicação de 4 distribui as réplicas uniformemente pelos domínios.
        
        * Para obter informações sobre o número de domínios de falha numa região, consulte o documento [Disponibilidade das máquinas virtuais Linux](../../virtual-machines/availability.md).

        * O Apache Kafka não está ciente dos domínios de falha do Azure. Durante a criação de réplicas de partição para tópicos, poderá não distribuir as réplicas corretamente para fins de elevada disponibilidade.

        * Para garantir uma elevada disponibilidade, utilize a [ferramenta de reequilíbrio de partição Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Esta ferramenta deve ser executada a partir de uma ligação SSH ao nó principal do cluster do Apache Kafka.

        * Para garantir a maior disponibilidade dos seus dados do Apache Kafka, deve reequilibrar as réplicas de partições do tópico quando:

            * Criar um novo tópico ou partição

            * Aumentar verticalmente um cluster

* **Para listar os tópicos**, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Este comando apresenta uma lista de tópicos disponíveis no cluster do Apache Kafka.

* **Para eliminar um tópico**, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Este comando elimina o tópico com o nome `topicname`.

    > [!WARNING]  
    > Se eliminar o tópico `test` criado anteriormente, tem de recriá-lo. O mesmo é utilizado por passos mais adiante neste documento.

Para obter mais informações sobre os comandos disponíveis com o utilitário `kafka-topics.sh`, utilize o seguinte comando:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produzir e consumir registos

O Kafka armazena *registos* nos tópicos. Os registos são produzidos por *produtores* e consumidos por *consumidores*. Os produtores e consumidores comunicam com o serviço do *mediador do Kafka*. Cada nó de trabalho no cluster do HDInsight é um anfitrião do mediador do Apache Kafka.

Utilize os seguintes passos para armazenar os registos no tópico de teste criado anteriormente e, em seguida, leia-os através de um consumidor:

1. Para escrever registos no tópico, utilize o utilitário `kafka-console-producer.sh` a partir da ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Após este comando, chega a uma linha vazia.

2. Introduza uma mensagem de texto na linha vazia e carregue em Enter. Introduza algumas mensagens desta forma e, em seguida, utilize **Ctrl + C** para regressar à linha de comandos normal. Cada linha é enviada como um registo separado para o tópico do Apache Kafka.

3. Para ler registos do tópico, utilize o utilitário `kafka-console-consumer.sh` a partir da ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Este comando obtém os registos do tópico e apresenta-os. A utilização de `--from-beginning` indica ao consumidor para iniciar a partir do início da transmissão em fluxo, para que todos os registos sejam obtidos.

    Se estiver a utilizar uma versão antiga do Kafka, substitua `--bootstrap-server $KAFKABROKERS` por `--zookeeper $KAFKAZKHOSTS`.

4. Utilize __Ctrl + C__ para parar o consumidor.

Também podem criar programaticamente produtores e consumidores. Para um exemplo de utilização desta API, consulte o Apache Kafka Producer and Consumer API com o documento [HDInsight.](apache-kafka-producer-consumer-api.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados por este início rápido, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

> [!WARNING]  
> A eliminação de um cluster Apache Kafka no HDInsight elimina todos os dados armazenados em Kafka.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Use a Faísca Apache com Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
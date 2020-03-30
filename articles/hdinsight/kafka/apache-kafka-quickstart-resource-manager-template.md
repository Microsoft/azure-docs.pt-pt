---
title: 'Quickstart: Apache Kafka usando O Gestor de Recursos Azure - HDInsight'
description: Neste arranque rápido, aprende-se a criar um cluster Apache Kafka no Azure HDInsight utilizando o modelo do Gestor de Recursos Azure. Também irá saber mais sobre tópicos, subscritores e consumidores do Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: f5f92044a0274b809388eeb164be9f1587013e0b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064589"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Quickstart: Criar o cluster Apache Kafka no Azure HDInsight usando o modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um cluster [Apache Kafka](./apache-kafka-introduction.md) em Azure HDInsight. O Kafka é uma plataforma open source de transmissão em fluxo distribuída. É frequentemente utilizado como mediador de mensagens, uma vez que fornece funcionalidades semelhantes a uma fila de mensagens de publicação-subscrição.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Só os recursos dentro da mesma rede virtual podem aceder à API Kafka. Neste guia de início rápido, irá aceder ao cluster diretamente através de SSH. Para ligar outros serviços, redes ou máquinas virtuais ao Kafka, tem primeiro de criar uma rede virtual e, em seguida, criar os recursos dentro da rede. Para obter mais informações, veja o documento [Ligar ao Apache Kafka com uma rede virtual](apache-kafka-connect-vpn-gateway.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-apache-kafka-cluster"></a>Criar um cluster do Apache Kafka

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-kafka).

:::code language="json" source="~/quickstart-templates/101-hdinsight-kafka/azuredeploy.json" range="1-150":::

Dois recursos Azure são definidos no modelo:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): crie uma conta de armazenamento Azure.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): crie um cluster HDInsight.

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o botão **Deploy para Azure** abaixo para iniciar sessão no Azure e abra o modelo de Gestor de Recursos.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-kafka-java-get-started%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/apache-kafka-quickstart-resource-manager-template/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Introduza ou selecione os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|A partir da lista de lançamentos, selecione a subscrição Azure que é usada para o cluster.|
    |Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente, ou selecione **Criar novo**.|
    |Localização|O valor irá autopovoar-se com a localização utilizada para o grupo de recursos.|
    |Nome do Cluster|Introduza um nome globalmente exclusivo. Para este modelo, utilize apenas letras minúsculas e números.|
    |Nome de Utilizador de Início de Sessão do Cluster|Forneça o nome de utilizador, o predefinido é **o administrador**.|
    |Palavra-passe de Início de Sessão do Cluster|Forneça uma senha. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula e uma letra minúscula, um caracteres não alfanuméricos (exceto os caracteres ' ' ). |
    |Nome do utilizador SSH|Forneça o nome de utilizador, o padrão é **sshuser**|
    |Palavra-passe ssh|Forneça a senha.|

    ![Uma captura de ecrã das propriedades do modelo](./media/apache-kafka-quickstart-resource-manager-template/resource-manager-template-kafka.png)

1. Reveja os **TERMOS E CONDIÇÕES.** Em seguida, selecione **Concordo com os termos e condições acima indicados,** em **seguida, comprar**. Receberá uma notificação de que a sua implantação está em andamento. A criação de um cluster demora cerca de 20 minutos.

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Assim que o cluster for criado, receberá uma notificação **de Implementação bem sucedida** com uma ligação de recurso **Go.** A página do grupo Recursos listará o seu novo cluster HDInsight e o armazenamento predefinido associado ao cluster. Cada cluster tem uma conta [de Armazenamento Azure](../hdinsight-hadoop-use-blob-storage.md) ou uma dependência da conta de armazenamento de [lagos de dados Azure.](../hdinsight-hadoop-use-data-lake-store.md) É referida como a conta de armazenamento padrão. O cluster HDInsight e a sua conta de armazenamento predefinido devem ser colocalizados na mesma região de Azure. Eliminar aglomerados não apaga a conta de armazenamento.

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a>Obtenha a informação do anfitrião do Zoológico apache e corretor

Ao trabalhar com Kafka, deve conhecer os anfitriões do *Zoológico* Apache e do *Corretor.* Estes anfitriões são utilizados com a API Kafka e com muitos dos utilitários que são enviados com o Kafka.

Nesta secção, irá obter as informações do anfitrião da API REST do Ambari no cluster.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da ligação SSH, `jq` utilize o seguinte comando para instalar o utilitário. Este utilitário é utilizado para analisar documentos JSON e é útil para obter as informações do anfitrião:

    ```bash
    sudo apt -y install jq
    ```

1. Para definir uma variável de ambiente para o nome do cluster, utilize o comando seguinte:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Quando lhe for pedido, introduza o nome do cluster do Kafka.

1. Para definir uma variável ambiental com informações do anfitrião zookeeper, use o comando abaixo. O comando recupera todos os anfitriões do Zookeeper, e depois devolve apenas as duas primeiras entradas. Isto acontece porque é desejável que exista alguma redundância para o caso de um anfitrião estar inacessível.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Quando lhe for pedido, introduza a palavra-passe da conta do início de sessão do cluster (não a conta SSH).

1. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Para definir uma variável de ambiente com as informações do anfitrião do mediador do Kafka, utilize o comando seguinte:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Quando lhe for pedido, introduza a palavra-passe da conta do início de sessão do cluster (não a conta SSH).

1. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

    ```bash
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Gerir tópicos do Apache Kafka

O Kafka armazena fluxos de dados em *tópicos*. Pode utilizar o utilitário `kafka-topics.sh` para gerir os tópicos.

* **Para criar um tópico**, utilize o seguinte comando na ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Este comando liga ao Zookeeper com as informações do anfitrião armazenadas em `$KAFKAZKHOSTS`. Em seguida, cria um tópico do Kafka denominado **teste**.

    * Os dados armazenados neste tópico estão particionados em oito partições.

    * Cada partição é replicada em três nós de trabalho no cluster.

        Se tiver criado o cluster numa região do Azure que fornece três domínios de falha, utilize um fator de replicação de 3. Caso contrário, utilize um fator de replicação de 4.
        
        Nas regiões com três domínios de falha, um fator de replicação de 3 permite que as réplicas sejam distribuídas pelos domínios de falha. Nas regiões com dois domínios de falha, um fator de replicação de 4 distribui as réplicas uniformemente pelos domínios.
        
        Para obter informações sobre o número de domínios de falha numa região, consulte o documento [Disponibilidade das máquinas virtuais Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        Kafka não sabe dos domínios de falha do Azure. Durante a criação de réplicas de partição para tópicos, poderá não distribuir as réplicas corretamente para fins de elevada disponibilidade.

        Para garantir uma elevada disponibilidade, utilize a ferramenta de reequilíbrio da [divisória Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Esta ferramenta deve ser executada a partir de uma ligação SSH para o nó principal do cluster do Kafka.

        Para garantir a maior disponibilidade dos seus dados do Kafka, deve reequilibrar as réplicas de partições do tópico ao:

        * Criar um novo tópico ou partição

        * Aumentar verticalmente um cluster

* **Para listar os tópicos**, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Este comando apresenta uma lista de tópicos disponíveis no cluster do Kafka.

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

O Kafka armazena *registos* nos tópicos. Os registos são produzidos por *produtores* e consumidos por *consumidores*. Os produtores e consumidores comunicam com o serviço do *mediador do Kafka*. Cada nó de trabalho no cluster do HDInsight é um anfitrião do mediador do Kafka.

Utilize os seguintes passos para armazenar os registos no tópico de teste criado anteriormente e, em seguida, leia-os através de um consumidor:

1. Para escrever registos no tópico, utilize o utilitário `kafka-console-producer.sh` a partir da ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Após este comando, chega a uma linha vazia.

1. Introduza uma mensagem de texto na linha vazia e carregue em Enter. Introduza algumas mensagens desta forma e, em seguida, utilize **Ctrl + C** para regressar à linha de comandos normal. Cada linha é enviada como um registo separado para o tópico do Kafka.

1. Para ler registos do tópico, utilize o utilitário `kafka-console-consumer.sh` a partir da ligação SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Este comando obtém os registos do tópico e apresenta-os. A utilização de `--from-beginning` indica ao consumidor para iniciar a partir do início da transmissão em fluxo, para que todos os registos sejam obtidos.

    Se estiver a usar uma versão `--bootstrap-server $KAFKABROKERS` mais `--zookeeper $KAFKAZKHOSTS`antiga de Kafka, substitua-o por .

1. Utilize __Ctrl + C__ para parar o consumidor.

Também podem criar programaticamente produtores e consumidores. Para um exemplo de utilização desta API, consulte o Apache Kafka Producer e a Consumer API com o documento [HDInsight.](apache-kafka-producer-consumer-api.md)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

A partir do portal Azure, navegue até ao seu cluster e selecione **Eliminar**.

![Modelo de gestor de recursos HBase](./media/apache-kafka-quickstart-resource-manager-template/azure-portal-delete-kafka.png)

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um cluster Apache Kafka no HDInsight usando um modelo de Gestor de Recursos. No próximo artigo, aprende-se a criar uma aplicação que utiliza a Apache Kafka Streams API e executa-a com kafka no HDInsight.

> [!div class="nextstepaction"]
> [Use Apache Kafka streams API em Azure HDInsight](./apache-kafka-streams-api.md)

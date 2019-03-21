---
title: Introdução ao Apache Kafka – Início Rápido do Azure HDInsight
description: Neste guia de início rápido, irá saber como criar um cluster do Apache Kafka no Azure HDInsight através do portal do Azure. Também irá saber mais sobre tópicos, subscritores e consumidores do Kafka.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: mvc,hdinsightactive
ms.topic: quickstart
ms.date: 04/16/2018
ms.openlocfilehash: b359716db5a30f8a77b9f54c2b0bc7b4d00a7f39
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225761"
---
# <a name="quickstart-create-an-apache-kafka-on-hdinsight-cluster"></a>Início rápido: Criar um Apache Kafka no cluster do HDInsight

[Apache Kafka](https://kafka.apache.org/) é uma plataforma de transmissão em fluxo distribuída e de código-fonte aberto. É frequentemente utilizado como mediador de mensagens, uma vez que fornece funcionalidades semelhantes a uma fila de mensagens de publicação-subscrição. 

Neste guia de início rápido, irá saber como criar um cluster do [Apache Kafka](https://kafka.apache.org) com um modelo do Azure Resource Manager. Também vai saber como utilizar utilitários incluídos para enviar e receber mensagens com o Kafka. Modelos semelhantes podem ser visualizados no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). A referência de modelo pode ser encontrada [aqui](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]  
> Só os recursos dentro da mesma rede virtual podem aceder à API Kafka. Neste guia de início rápido, irá aceder ao cluster diretamente através de SSH. Para ligar outros serviços, redes ou máquinas virtuais ao Kafka, tem primeiro de criar uma rede virtual e, em seguida, criar os recursos dentro da rede.
>
> Para obter mais informações, veja o documento [Ligar ao Apache Kafka com uma rede virtual](apache-kafka-connect-vpn-gateway.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Um cliente SSH. Os passos neste documento utilizam SSH para se ligar ao cluster.

    O comando `ssh` é fornecido por predefinição nos sistemas Linux, Unix e macOS. No Windows 10, utilize um dos seguintes métodos para instalar o comando `ssh`:

  * Utilize o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart). O cloud shell fornece o comando `ssh` e pode ser configurado para utilizar o Bash ou o PowerShell como ambiente de shell.

  * [Instale o Subsistema Windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10). As distribuições de Linux disponíveis na Microsoft Store fornecem o comando `ssh`.

    > [!IMPORTANT]  
    > Os passos neste documento pressupõem que está a utilizar um dos clientes SSH mencionados acima. Se estiver a utilizar outro cliente SSH e encontrar problemas, consulte a documentação do seu cliente SSH.
    >
    > Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Criar um cluster do Apache Kafka

1. Clique na imagem seguinte para abrir o modelo no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-kafka-java-get-started%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/apache-kafka-quickstart-resource-manager-template/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Para criar o cluster do Kafka, utilize os seguintes valores:

    | Propriedade | Value |
    | --- | --- |
    | Subscrição | A sua subscrição do Azure. |
    | Grupo de recursos | O grupo de recursos em que o cluster é criado. |
    | Localização | A região do Azure em que o cluster é criado. |
    | Nome do Cluster | O nome do cluster do Kafka. |
    | Nome de Utilizador de Início de Sessão do Cluster | O nome da conta utilizado para iniciar sessão em serviços baseados em HTTPs alojados no cluster. |
    | Palavra-passe de Início de Sessão do Cluster | A palavra-passe do nome de utilizador de início de sessão. |
    | Nome de Utilizador SSH | O nome de utilizador SSH. Esta conta pode aceder ao cluster através de SSH. |
    | Palavra-passe do SSH | A palavra-passe do utilizador SSH. |

    ![Uma captura de ecrã das propriedades do modelo](./media/apache-kafka-quickstart-resource-manager-template/kafka-template-parameters.png)

3. Selecione **Concordo com os termos e condições indicados acima**, selecione **Afixar ao dashboard** e, em seguida, clique em **Comprar**.

> [!NOTE]  
> A criação do cluster pode demorar até 20 minutos.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

1. Para ligar ao nó principal primário do cluster do Kafka, utilize o seguinte comando. Substitua `sshuser` pelo nome de utilizador SSH. Substitua `mykafka` pelo nome do seu cluster do Kafka

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Quando se liga pela primeira vez ao cluster, o cliente SSH pode apresentar um aviso a indicar que não é possível estabelecer a autenticidade do anfitrião. Quando lhe for pedido, escreva __sim__ e, em seguida, prima __Enter__ para adicionar o anfitrião à lista de servidores fidedignos do seu cliente SSH.

3. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

Quando estiver ligado, verá informações semelhantes ao texto seguinte:

```text
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Obter as informações do anfitrião Apache Zookeeper e Mediador

Ao trabalhar com o Kafka, tem de saber o *Apache Zookeeper* e *Broker* anfitriões. Estes anfitriões são utilizados com a API Kafka e com muitos dos utilitários que são enviados com o Kafka.

Nesta secção, irá obter as informações do anfitrião da API REST do Ambari no cluster.

1. A partir da ligação SSH ao cluster, utilize o comando seguinte para instalar o utilitário `jq`. Este utilitário é utilizado para analisar documentos JSON e é útil para obter as informações do anfitrião:
   
    ```bash
    sudo apt -y install jq
    ```

2. Para definir uma variável de ambiente para o nome do cluster, utilize o comando seguinte:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Quando lhe for pedido, introduza o nome do cluster do Kafka.

3. Para definir uma variável de ambiente com as informações do anfitrião Zookeeper, utilize o comando seguinte:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Quando lhe for pedido, introduza a palavra-passe da conta do início de sessão do cluster (não a conta SSH).

    > [!NOTE]  
    > Este comando obtém todos os anfitriões do Zookeeper e devolve apenas as primeiras duas entradas. Isto acontece porque é desejável que exista alguma redundância para o caso de um anfitrião estar inacessível.

4. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Para definir uma variável de ambiente com as informações do anfitrião do mediador do Kafka, utilize o comando seguinte:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Quando lhe for pedido, introduza a palavra-passe da conta do início de sessão do cluster (não a conta SSH).

6. Para verificar se a variável de ambiente está definida corretamente, utilize o comando seguinte:

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

        > [!IMPORTANT]  
        > Se tiver criado o cluster numa região do Azure que fornece três domínios de falha, utilize um fator de replicação de 3. Caso contrário, utilize um fator de replicação de 4.
        
        Nas regiões com três domínios de falha, um fator de replicação de 3 permite que as réplicas sejam distribuídas pelos domínios de falha. Nas regiões com dois domínios de falha, um fator de replicação de 4 distribui as réplicas uniformemente pelos domínios.
        
        Para obter informações sobre o número de domínios de falha numa região, consulte o documento [Disponibilidade das máquinas virtuais Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        > [!IMPORTANT]   
        > O Kafka não está ciente dos domínios de falha do Azure. Durante a criação de réplicas de partição para tópicos, poderá não distribuir as réplicas corretamente para fins de elevada disponibilidade.

        Para garantir a elevada disponibilidade, utilize o [ferramenta de reequilíbrio de partições do Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Esta ferramenta deve ser executada a partir de uma ligação SSH para o nó principal do cluster do Kafka.

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

2. Introduza uma mensagem de texto na linha vazia e carregue em Enter. Introduza algumas mensagens desta forma e, em seguida, utilize **Ctrl + C** para regressar à linha de comandos normal. Cada linha é enviada como um registo separado para o tópico do Kafka.

3. Para ler registos do tópico, utilize o utilitário `kafka-console-consumer.sh` a partir da ligação SSH:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Este comando obtém os registos do tópico e apresenta-os. A utilização de `--from-beginning` indica ao consumidor para iniciar a partir do início da transmissão em fluxo, para que todos os registos sejam obtidos.

    > [!NOTE]  
    > Se estiver a utilizar uma versão antiga do Kafka, substitua `--bootstrap-server $KAFKABROKERS` por `--zookeeper $KAFKAZKHOSTS`.

4. Utilize __Ctrl + C__ para parar o consumidor.

Também podem criar programaticamente produtores e consumidores. Para obter um exemplo de utilização desta API, consulte a [produtor do Apache Kafka e API de consumidor com o HDInsight](apache-kafka-producer-consumer-api.md) documento.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar os recursos criados por este guia de início rápido, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

> [!WARNING]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado.
> 
> Eliminar um cluster do Kafka no HDInsight elimina quaisquer dados armazenados no Kafka.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Utilizar o Apache Spark com o Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)


---
title: Criar clusters HBase em uma rede virtual – Azure
description: Comece a usar o HBase no Azure HDInsight. Saiba como criar clusters HBase do HDInsight na rede virtual do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972800"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Criar clusters do Apache HBase no HDInsight na rede virtual do Azure

Saiba como criar clusters do Apache HBase do Azure HDInsight em uma [rede virtual do Azure](https://azure.microsoft.com/services/virtual-network/).

Com a integração de rede virtual, os clusters do Apache HBase podem ser implantados na mesma rede virtual que seus aplicativos para que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

* Conectividade direta do aplicativo Web para os nós do cluster HBase, que permite a comunicação por meio de APIs de RPC (chamada de procedimento remoto) Java do HBase.
* Desempenho aprimorado sem o tráfego passar por vários gateways e balanceadores de carga.
* A capacidade de processar informações confidenciais de maneira mais segura sem expor um ponto de extremidade público.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Criar um cluster apache HBase na rede virtual

Nesta seção, você cria um cluster apache HBase baseado em Linux com a conta de armazenamento do Azure dependente em uma rede virtual do Azure usando um [modelo de Azure Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Para obter outros métodos de criação de cluster e entender as configurações, confira [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar um modelo para criar Apache Hadoop clusters no HDInsight, consulte [Criar clusters Apache Hadoop no hdinsight usando modelos de Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Algumas propriedades são embutidas em código no modelo. Por exemplo:
>
> * **Local**: leste dos EUA 2
> * **Versão do cluster**: 3.6
> * **Contagem de nós de trabalho do cluster**: 2
> * **Conta de armazenamento padrão**: uma cadeia de caracteres exclusiva
> * **Nome da rede virtual**: ClusterName-vnet
> * **Espaço de endereço de rede virtual**: 10.0.0.0/16
> * **Nome da sub-rede**: subnet1
> * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
>
> `CLUSTERNAME` é substituído pelo nome do cluster que você fornece ao usar o modelo.

1. Selecione a imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Na caixa de diálogo **implantação personalizada** , selecione **Editar modelo**.

1. Na linha 165, altere o valor `Standard_A3` para `Standard_A4_V2`. Em seguida, selecione **Guardar**.

1. Conclua o modelo restante com as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Subscrição|Selecione uma assinatura do Azure usada para criar o cluster HDInsight, a conta de armazenamento dependente e a rede virtual do Azure.|
    Grupo de recursos|Selecione **criar novo**e especifique um novo nome de grupo de recursos.|
    |Localização|Selecione uma localização para o grupo de recursos.|
    |Nome do cluster|Insira um nome para o cluster Hadoop a ser criado.|
    |Nome de usuário e senha de logon no cluster|O nome de usuário padrão é **admin**. Forneça uma senha.|
    |Nome de usuário e senha ssh|O nome de usuário padrão é **sshuser**.  Forneça uma senha.|

    Selecione **concordo com os termos e as condições declaradas acima**.

1. Selecione **Comprar**. A criação de um cluster demora cerca de 20 minutos. Depois que o cluster for criado, você poderá selecionar o cluster no portal para abri-lo.

Depois de concluir o artigo, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Para obter as instruções de exclusão de um cluster, consulte [gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Para começar a trabalhar com o novo cluster HBase, você pode usar os procedimentos encontrados em Introdução ao [uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Conectar-se ao cluster do Apache HBase usando APIs RPC do Apache HBase Java

### <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

Crie uma máquina virtual de IaaS (infraestrutura como serviço) na mesma rede virtual do Azure e na mesma sub-rede. Para obter instruções sobre como criar uma nova máquina virtual IaaS, consulte [criar uma máquina virtual executando o Windows Server](../../virtual-machines/windows/quick-create-portal.md). Ao seguir as etapas deste documento, você deve usar os seguintes valores para a configuração de rede:

* **Rede virtual**: ClusterName-vnet
* **Sub-rede**: subnet1

> [!IMPORTANT]  
> Substitua `CLUSTERNAME` pelo nome usado ao criar o cluster HDInsight nas etapas anteriores.

Usando esses valores, a máquina virtual é colocada na mesma rede virtual e sub-rede que o cluster HDInsight. Essa configuração permite que eles se comuniquem diretamente entre si. Há uma maneira de criar um cluster HDInsight com um nó de borda vazio. O nó de borda pode ser usado para gerenciar o cluster.  Para obter mais informações, consulte [usar nós de borda vazios no HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Obter nome de domínio totalmente qualificado

Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o FQDN (nome de domínio totalmente qualificado). Para determinar isso, você deve obter o sufixo DNS específico da conexão do cluster HBase. Para fazer isso, você pode usar um dos seguintes métodos:

* Use um navegador da Web para fazer uma chamada do [Apache Ambari](https://ambari.apache.org/) :

    Navegue para `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Ele retorna um arquivo JSON com os sufixos DNS.

* Use o site do Ambari:

    1. Navegue para `https://CLUSTERNAME.azurehdinsight.net`.
    2. Selecione **hosts** no menu superior.

* Use a ondulação para fazer chamadas REST:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Nos dados de JavaScript Object Notation (JSON) retornados, localize a entrada "host_name". Ele contém o FQDN para os nós no cluster. Por exemplo:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

A parte do nome de domínio que começa com o nome do cluster é o sufixo DNS. Por exemplo, `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>Verificar a comunicação dentro da rede virtual

Para verificar se a máquina virtual pode se comunicar com o cluster HBase, use o comando `ping headnode0.<dns suffix>` da máquina virtual. Por exemplo, `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Para usar essas informações em um aplicativo Java, você pode seguir as etapas em [usar o Apache Maven para criar aplicativos Java que usam o Apache HBase com o HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) para criar um aplicativo. Para que o aplicativo se conecte a um servidor HBase remoto, modifique o arquivo **HBase-site. xml** neste exemplo para usar o FQDN para Zookeeper. Por exemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Para obter mais informações sobre a resolução de nomes em redes virtuais do Azure, incluindo como usar seu próprio servidor DNS, consulte [resolução de nomes (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar um cluster do Apache HBase. Para saber mais, consulte:

* [Introdução ao HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Usar nós de borda vazios no HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configurar a replicação do Apache HBase no HDInsight](apache-hbase-replication.md)
* [Criar clusters Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Introdução ao uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Virtual Network Overview](../../virtual-network/virtual-networks-overview.md) (Descrição Geral da Rede Virtual)

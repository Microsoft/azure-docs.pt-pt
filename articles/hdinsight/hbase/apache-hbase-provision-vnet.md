---
title: Criar clusters HBase numa Rede Virtual - Azure
description: Começa a usar hbase em Azure HDInsight. Saiba como criar clusters HDInsight HBase na Rede Virtual Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 82e3374491aa119d9985ea7ef31e180c920511d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087746"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Criar clusters Apache HBase em HDInsight na Rede Virtual Azure

Saiba como criar clusters Apache HBase Azure HDInsight numa [Rede Virtual Azure.](https://azure.microsoft.com/services/virtual-network/)

Com a integração virtual da rede, os clusters Apache HBase podem ser implantados na mesma rede virtual que as suas aplicações para que as aplicações possam comunicar diretamente com o HBase. Os benefícios incluem:

* Conectividade direta da aplicação web aos nós do cluster HBase, que permite a comunicação através de APIs de procedimento remoto HBase Java (RPC).
* Melhor desempenho por não ter o seu tráfego passar por vários gateways e equilibradores de carga.
* A capacidade de processar informação sensível de uma forma mais segura sem expor um ponto final público.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Criar cluster Apache HBase em rede virtual

Nesta secção, você cria um cluster Apache HBase baseado em Linux com a conta de armazenamento Azure dependente em uma rede virtual Azure usando um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/deploy-powershell.md). Para outros métodos de criação de clusters e compreensão das definições, consulte [criar clusters HdInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre a utilização de um modelo para criar clusters Apache Hadoop em HDInsight, consulte [crie clusters Apache Hadoop em HDInsight usando modelos de Gestor de Recursos Azure](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Algumas propriedades são codificadas no modelo. Por exemplo:
>
> * **Localização**: Leste DOS EUA 2
> * **Versão do cluster**: 3.6
> * **Contagem de nó de trabalhador cluster**: 2
> * **Conta de armazenamento padrão**: uma cadeia única
> * **Nome da rede virtual**: CLUSTERNAME-vnet
> * **Espaço de endereço de rede virtual**: 10.0.0.0/16
> * **Nome da sub-rede**: sub-rede1
> * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
>
> `CLUSTERNAME` é substituído pelo nome de cluster que fornece ao utilizar o modelo.

1. Selecione a seguinte imagem para abrir o modelo no portal Azure. O modelo está localizado em [modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. A partir do diálogo **de implementação personalizado,** selecione **o modelo de edição.**

1. Na linha 165, altere o valor `Standard_A3` para `Standard_A4_V2` . Em seguida, selecione **Guardar**.

1. Complete o modelo restante com as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Subscrição|Selecione uma subscrição Azure usada para criar o cluster HDInsight, a conta de Armazenamento dependente e a rede virtual Azure.|
    Grupo de recursos|Selecione **Criar novo**e especifique um novo nome de grupo de recursos.|
    |Localização|Selecione uma localização para o grupo de recursos.|
    |Nome do Cluster|Insira um nome para o cluster Hadoop a ser criado.|
    |Nome de utilizador e senha de login do cluster|O nome de utilizador predefinido é **administrador.** Forneça uma senha.|
    |Nome de utilizador e senha de utilização Ssh|O nome de utilizador predefinido é **sshuser**.  Forneça uma senha.|

    **Selecione Concordo com os termos e as condições acima indicados.**

1. Selecione **Comprar**. A criação de um cluster demora cerca de 20 minutos. Uma vez criado o cluster, pode selecionar o cluster no portal para o abrir.

Depois de completar o artigo, é melhor apagar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Para obter as instruções de eliminação de um cluster, consulte [os clusters Da Gestão de Apache Hadoop em HDInsight utilizando o portal Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Para começar a trabalhar com o seu novo cluster HBase, pode utilizar os procedimentos encontrados em [Get start using Apache HBase with Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Ligue-se ao cluster Apache HBase usando Apache HBase Java RPC APIs

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma infraestrutura como máquina virtual de serviço (IaaS) na mesma rede virtual Azure e na mesma sub-rede. Para obter instruções sobre a criação de uma nova máquina virtual IaaS, consulte [Criar uma máquina virtual que executa o Servidor Windows](../../virtual-machines/windows/quick-create-portal.md). Ao seguir os passos deste documento, deve utilizar os seguintes valores para a configuração da Rede:

* **Rede virtual**: CLUSTERNAME-vnet
* **Sub-rede**: sub-rede1

> [!IMPORTANT]  
> `CLUSTERNAME`Substitua-o pelo nome utilizado ao criar o cluster HDInsight em etapas anteriores.

Utilizando estes valores, a máquina virtual é colocada na mesma rede virtual e sub-rede que o cluster HDInsight. Esta configuração permite-lhes comunicar diretamente uns com os outros. Há uma maneira de criar um cluster HDInsight com um nó de borda vazia. O nó de borda pode ser usado para gerir o cluster.  Para obter mais informações, consulte [Utilize nós de borda vazio em HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Obtenha o nome de domínio totalmente qualificado

Ao utilizar uma aplicação Java para ligar remotamente à HBase, deve utilizar o nome de domínio totalmente qualificado (FQDN). Para determinar isto, tem de obter o sufixo DNS específico de ligação do cluster HBase. Para isso, pode utilizar um dos seguintes métodos:

* Utilize um navegador Web para fazer uma chamada [Apache Ambari:](https://ambari.apache.org/)

    Navegue para `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Devolve um ficheiro JSON com os sufixos DNS.

* Utilize o site Ambari:

    1. Navegue para `https://CLUSTERNAME.azurehdinsight.net`.
    2. Selecione **Anfitriões** no menu superior.

* Use Curl para fazer chamadas DE REST:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Nos dados de Notação de Objetos JavaScript (JSON) devolvidos, encontre a entrada "host_name". Contém o FQDN para os nós no cluster. Por exemplo:

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

### <a name="verify-communication-inside-virtual-network"></a>Verificar comunicação dentro da rede virtual

Para verificar se a máquina virtual pode comunicar com o cluster HBase, utilize o comando `ping headnode0.<dns suffix>` a partir da máquina virtual. Por exemplo, `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Para utilizar estas informações numa aplicação Java, pode seguir os passos em [Use Apache Maven para construir aplicações Java que usam Apache HBase com HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) para criar uma aplicação. Para que a aplicação se conecte a um servidor HBase remoto, modifique o ficheiro **hbase-site.xml** neste exemplo para utilizar o FQDN para Zookeeper. Por exemplo:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Para obter mais informações sobre a resolução de nomes em redes virtuais Azure, incluindo como utilizar o seu próprio servidor DNS, consulte [a Resolução de Nomes (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar um cluster Apache HBase. Para saber mais, veja:

* [Introdução ao HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Use os nódes de borda vazios em HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configure a replicação apache HBase em HDInsight](apache-hbase-replication.md)
* [Criar clusters Apache Hadoop em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Começar a usar Apache HBase com Apache Hadoop em HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)

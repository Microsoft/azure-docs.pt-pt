---
title: Criar clusters HBase numa Rede Virtual - Azure
description: Começar a usar HBase em Azure HDInsight. Saiba como criar clusters HBase HDInsight na Rede Virtual Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972800"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Crie clusters Apache HBase no HDInsight na Rede Virtual Azure

Saiba como criar clusters HBase HDInsight HBase Azure numa [rede virtual Azure](https://azure.microsoft.com/services/virtual-network/).

Com a integração virtual da rede, os clusters Apache HBase podem ser implantados na mesma rede virtual que as suas aplicações para que as aplicações possam comunicar diretamente com a HBase. Os benefícios incluem:

* Conectividade direta da aplicação web aos nós do cluster HBase, que permite a comunicação através de APIs de chamada de procedimento remoto HBase Java (RPC).
* Melhor desempenho ao não ter o seu tráfego passar por vários gateways e equilibradores de carga.
* A capacidade de processar informação sensível de uma forma mais segura sem expor um ponto final público.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Criar o cluster Apache HBase em rede virtual

Nesta secção, cria-se um cluster Apache HBase baseado em Linux com a conta de Armazenamento Azure dependente numa rede virtual Azure utilizando um modelo de Gestor de [Recursos Azure](../../azure-resource-manager/templates/deploy-powershell.md). Para outros métodos de criação de clusters e compreensão das definições, consulte [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre o uso de um modelo para criar clusters Apache Hadoop no HDInsight, consulte [Create Apache Hadoop clusters em HDInsight usando modelos de Gestor](../hdinsight-hadoop-create-linux-clusters-arm-templates.md) de Recursos Azure

> [!NOTE]  
> Algumas propriedades são codificadas no modelo. Por exemplo:
>
> * **Localização**: East US 2
> * **Versão do cluster**: 3.6
> * **Contagem do nó do trabalhador do cluster:** 2
> * **Conta de armazenamento padrão**: uma cadeia única
> * **Nome de rede virtual**: CLUSTERNAME-vnet
> * **Espaço**de endereço de rede virtual : 10.0.0.0.0/16
> * **Nome da sub-rede**: subnet11
> * Gama de **endereços de sub-rede**: 10.0.0.0/24
>
> `CLUSTERNAME`é substituído pelo nome do cluster que fornece ao utilizar o modelo.

1. Selecione a seguinte imagem para abrir o modelo no portal Azure. O modelo está localizado em [modelos de quickstart Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. A partir do diálogo de **implementação personalizado,** selecione **modelo de edição**.

1. Na linha 165, `Standard_A3` `Standard_A4_V2`alterar valor para . Em seguida, selecione **Guardar**.

1. Complete o modelo restante com as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Subscrição|Selecione uma subscrição Azure utilizada para criar o cluster HDInsight, a conta de Armazenamento dependente e a rede virtual Azure.|
    Grupo de recursos|Selecione **Criar novo**e especificar um novo nome de grupo de recursos.|
    |Localização|Selecione uma localização para o grupo de recursos.|
    |Nome do Cluster|Introduza um nome para o aglomerado hadoop a ser criado.|
    |Nome e palavra-passe do utilizador de login do cluster|O Nome de Utilizador predefinido é **administrador**. Forneça uma senha.|
    |Nome e senha de utilizador ssh|O nome de utilizador predefinido é **sshuser**.  Forneça uma senha.|

    Selecione **Concordo com os termos e as condições acima referidas**.

1. Selecione **Comprar**. A criação de um cluster demora cerca de 20 minutos. Uma vez criado o cluster, pode selecionar o cluster no portal para o abrir.

Depois de completar o artigo, talvez queira apagar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Para obter as instruções de abater um cluster, consulte [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Para começar a trabalhar com o seu novo cluster HBase, pode utilizar os procedimentos encontrados no [Get começou a usar Apache HBase com Apache Hadoop em HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Ligue-se ao cluster Apache HBase utilizando apis Apache HBase Java RPC

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma infraestrutura como uma máquina virtual de serviço (IaaS) na mesma rede virtual Azure e na mesma subrede. Para obter instruções sobre a criação de uma nova máquina virtual IaaS, consulte [Criar uma máquina virtual executando o servidor windows](../../virtual-machines/windows/quick-create-portal.md). Ao seguir os passos deste documento, deve utilizar os seguintes valores para a configuração da Rede:

* **Rede virtual**: CLUSTERNAME-vnet
* **Subnet**: subnet11

> [!IMPORTANT]  
> Substitua-o `CLUSTERNAME` pelo nome utilizado ao criar o cluster HDInsight em passos anteriores.

Utilizando estes valores, a máquina virtual é colocada na mesma rede virtual e sub-rede que o cluster HDInsight. Esta configuração permite-lhes comunicar diretamente uns com os outros. Existe uma maneira de criar um cluster HDInsight com um nó de borda vazia. O nó de borda pode ser usado para gerir o cluster.  Para mais informações, consulte [Utilize nós de borda vazia no HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Obtenha nome de domínio totalmente qualificado

Ao utilizar uma aplicação Java para ligar remotamente à HBase, deve utilizar o nome de domínio totalmente qualificado (FQDN). Para determinar isto, tem de obter o sufixo DNS específico da ligação do cluster HBase. Para isso, pode utilizar um dos seguintes métodos:

* Use um navegador Web para fazer uma chamada [Apache Ambari:](https://ambari.apache.org/)

    Navegue para `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Devolve um ficheiro JSON com os sufixos DNS.

* Utilize o site ambari:

    1. Navegue para `https://CLUSTERNAME.azurehdinsight.net`.
    2. Selecione **Hosts** a partir do menu superior.

* Use curl para fazer chamadas DE REPOUSO:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Nos dados de notação de objetos JavaScript (JSON) devolvidos, encontre a entrada "host_name". Contém o FQDN para os nós do cluster. Por exemplo:

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

Para verificar se a máquina virtual pode comunicar com `ping headnode0.<dns suffix>` o cluster HBase, utilize o comando da máquina virtual. Por exemplo, `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Para utilizar esta informação numa aplicação Java, pode seguir os passos em [Use Apache Maven para construir aplicações Java que usam Apache HBase com HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) para criar uma aplicação. Para que a aplicação se ligue a um servidor HBase remoto, modifique o ficheiro **hbase-site.xml** neste exemplo para utilizar o FQDN para Zookeeper. Por exemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Para obter mais informações sobre a resolução de nomes nas redes virtuais do Azure, incluindo como utilizar o seu próprio servidor DNS, consulte [a Resolução de Nomes (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar um cluster Apache HBase. Para saber mais, consulte:

* [Introdução ao HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Utilize nódeos de borda vazio sintetizá-lo em HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configure a replicação de Apache HBase no HDInsight](apache-hbase-replication.md)
* [Criar clusters Apache Hadoop em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Começar a usar Apache HBase com Apache Hadoop em HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Descrição Geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)

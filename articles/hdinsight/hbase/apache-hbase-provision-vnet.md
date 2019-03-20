---
title: Criar HBase clusters numa rede Virtual - Azure
description: Introdução ao HBase no HDInsight do Azure. Saiba como criar clusters de HBase do HDInsight numa rede Virtual do Azure.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 73ac2072a087f0931b6c9c776d3ad0bfedb4320b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199533"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Criar clusters do Apache HBase no HDInsight na rede Virtual do Azure
Saiba como criar clusters do Azure HDInsight Apache HBase numa [rede Virtual do Azure][1].

Com a integração de rede virtual, clusters do Apache HBase podem ser implementados para a mesma rede virtual que seus aplicativos para que os aplicativos podem comunicar diretamente com o HBase. As vantagens incluem:

* Conectividade direta do aplicativo web para os nós do cluster HBase, que permite a comunicação através de procedimento remoto do HBase Java chamar APIs (RPC).
* Desempenho melhorado ao não ter o seu tráfego aceda ao longo de vários gateways e Balanceadores de carga.
* A capacidade de processar informações confidenciais de forma mais segura sem expor um ponto final público.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Uma estação de trabalho com o Azure PowerShell**. Ver [instalar e utilizar o Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Criar cluster do Apache HBase numa rede virtual
Nesta secção, vai criar um cluster do Apache HBase baseado em Linux com a conta de armazenamento do Azure dependente numa rede virtual do Azure utilizando uma [modelo Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Para outros métodos de criação de clusters e compreender as definições, consulte [clusters do HDInsight criar](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como utilizar um modelo para criar clusters do Apache Hadoop no HDInsight, consulte [criar Apache Hadoop clusters no HDInsight utilizando modelos Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Algumas propriedades são codificados no modelo. Por exemplo:
>
> * **Localização**: EUA Leste 2
> * **Versão do cluster**: 3.6
> * **Contagem de nós de trabalho de cluster**: 2
> * **Conta de armazenamento de predefinido**: uma cadeia exclusiva
> * **Nome da rede virtual**: &lt;Nome do cluster >-vnet
> * **Espaço de endereços de rede virtual**: 10.0.0.0/16
> * **Nome da sub-rede**: subnet1
> * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
>
> &lt;Nome do cluster > é substituído com o nome do cluster é fornecer ao utilizar o modelo.
>
>

1. Clique na imagem seguinte para abrir o modelo no portal do Azure. O modelo está localizado em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Partir do **implementação personalizada** painel, introduza as seguintes propriedades:

   * **Subscrição**: Selecione uma subscrição do Azure utilizada para criar o cluster do HDInsight, a conta de armazenamento dependente e a rede virtual do Azure.
   * **Grupo de recursos**: Selecione **criar novo**e especifique um nome de grupo de recursos novo.
   * **Localização**: Selecione uma localização para o grupo de recursos.
   * **ClusterName**: Introduza um nome para o cluster de Hadoop a ser criada.
   * **Nome de início de sessão e palavra-passe do cluster**: O nome de início de sessão predefinido é **admin**.
   * **Nome de utilizador SSH e palavra-passe**: O nome de utilizador predefinido é **sshuser**.  Pode alterá-lo.
   * **Eu concordo com os termos e condições indicados acima**: (Selecionar)
3. Clique em **Comprar**. A criação de um cluster demora cerca de 20 minutos. Assim que o cluster é criado, pode clicar no painel do cluster no portal para abri-lo.

Depois de concluir o tutorial, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Para instruções de eliminação de um cluster, consulte [Apache Hadoop gerir clusters no HDInsight com o portal do Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Para começar a trabalhar com o novo cluster de HBase, pode utilizar os procedimentos encontrados na [começar a utilizar o Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Ligue ao cluster do Apache HBase utilizando APIs do Apache HBase Java RPC
1. Crie uma infraestrutura como uma máquina virtual do serviço (IaaS) na mesma rede virtual do Azure e a mesma sub-rede. Para obter instruções sobre como criar uma nova máquina virtual de IaaS, consulte [criar uma Máquina Virtual a executar o Windows Server](../../virtual-machines/windows/quick-create-portal.md). Ao seguir os passos neste documento, tem de utilizar os seguintes valores para a configuração de rede:

   * **Rede virtual**: &lt;Nome do cluster >-vnet
   * **Sub-rede**: subnet1

   > [!IMPORTANT]  
   > Substitua &lt;nome do Cluster > com o nome que utilizou quando criou o cluster de HDInsight nos passos anteriores.

   Utilizar estes valores, a máquina virtual é colocada na mesma rede virtual e sub-rede que o cluster do HDInsight. Esta configuração permite que comunicam diretamente entre si. Existe uma forma de criar um cluster do HDInsight com um nó de extremidade vazio. Nó de extremidade pode ser utilizado para gerir o cluster.  Para obter mais informações, consulte [utilizar nós de extremidade vazios no HDInsight](../hdinsight-apps-use-edge-node.md).

2. Quando utilizar uma aplicação Java para ligar remotamente ao HBase, tem de utilizar o nome de domínio completamente qualificado (FQDN). Para determinar isso, tem de obter o sufixo DNS específico da ligação do HBase cluster. Para tal, pode utilizar um dos seguintes métodos:

   * Utilize um browser para tornar uma [Apache Ambari](https://ambari.apache.org/) chamar:

     Navegue para https://&lt;ClusterName >.azurehdinsight.net/api/v1/clusters/&lt;ClusterName > / aloja? minimal_response = true. Na verdade um ficheiro JSON com os sufixos DNS.
   * Utilize o site do Ambari:

     1. Navegue para https://&lt;ClusterName >. azurehdinsight.net.
     2. Clique em **anfitriões** no menu superior.
   * Utilize o Curl para fazer chamadas REST:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     Nos dados JavaScript Object Notation (JSON) devolvidos, localize a entrada de "host_name". Ela contém o FQDN para os nós do cluster. Por exemplo:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     A parte do início do nome de domínio com o nome do cluster é o sufixo DNS. Por exemplo, mycluster.b1.cloudapp.net.
   * Utilizar o Azure PowerShell

     Utilize o seguinte script do Azure PowerShell para registar o **Get-ClusterDetail** função, que pode ser utilizada para devolver o sufixo DNS:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Depois de executar o script do PowerShell do Azure, utilize o seguinte comando para devolver o sufixo DNS ao utilizar o **Get-ClusterDetail** função. Especifique o seu nome de cluster do HBase do HDInsight, o nome de administrador e a palavra-passe de administrador ao utilizar este comando.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Este comando devolve o sufixo DNS. Por exemplo, **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Para verificar que a máquina virtual podem comunicar com o cluster do HBase, utilize o comando `ping headnode0.<dns suffix>` da máquina virtual. Por exemplo, enviar um ping headnode0.mycluster.b1.cloudapp.net.

Para utilizar estas informações numa aplicação de Java, pode seguir os passos em [utilizar o Apache Maven para construir aplicações Java que utilizam o HBase do Apache com o HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) para criar uma aplicação. Para que a aplicação ligar a um servidor remoto do HBase, modificar os **hbase-site** ficheiro neste exemplo para utilizar o FQDN para Zookeeper. Por exemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Para obter mais informações sobre resolução de nomes no Azure redes virtuais, incluindo como utilizar o seu próprio servidor DNS, consulte [resolução de nomes (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a criar um cluster do Apache HBase. Para saber mais, consulte:

* [Introdução ao HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Utilizar nós de extremidade vazios no HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configurar a replicação de Apache HBase no HDInsight](apache-hbase-replication.md)
* [Criar clusters do Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Começar a utilizar o Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Virtual Network Overview](../../virtual-network/virtual-networks-overview.md) (Descrição Geral da Rede Virtual)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx


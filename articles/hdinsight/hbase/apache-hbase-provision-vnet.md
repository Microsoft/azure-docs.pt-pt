---
title: Criar clusters HBase em uma rede virtual – Azure
description: Comece a usar o HBase no Azure HDInsight. Saiba como criar clusters HBase do HDInsight na rede virtual do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 60a7afb6e610294ccaa535eaa7371ff8d5015db3
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077193"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Criar clusters do Apache HBase no HDInsight na rede virtual do Azure
Saiba como criar clusters do Apache HBase do Azure HDInsight em uma [rede virtual do Azure][1].

Com a integração de rede virtual, os clusters do Apache HBase podem ser implantados na mesma rede virtual que seus aplicativos para que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

* Conectividade direta do aplicativo Web para os nós do cluster HBase, que permite a comunicação por meio de APIs de RPC (chamada de procedimento remoto) Java do HBase.
* Desempenho aprimorado sem o tráfego passar por vários gateways e balanceadores de carga.
* A capacidade de processar informações confidenciais de maneira mais segura sem expor um ponto de extremidade público.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter os seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Uma estação de trabalho com o Azure PowerShell**. Consulte [instalar e usar Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Criar um cluster apache HBase na rede virtual
Nesta seção, você cria um cluster apache HBase baseado em Linux com a conta de armazenamento do Azure dependente em uma rede virtual do Azure usando um [modelo de Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Para obter outros métodos de criação de cluster e entender as configurações, confira [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar um modelo para criar Apache Hadoop clusters no HDInsight, consulte [Criar clusters Apache Hadoop no hdinsight usando modelos de Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Algumas propriedades são embutidas em código no modelo. Por exemplo:
>
> * **Local**: EUA Leste 2
> * **Versão do cluster**: 3.6
> * **Contagem de nós de trabalho do cluster**: 2
> * **Conta de armazenamento padrão**: uma cadeia de caracteres exclusiva
> * **Nome da rede virtual**: &lt;Nome do cluster >-vnet
> * **Espaço de endereço de rede virtual**: 10.0.0.0/16
> * **Nome da sub-rede**: subnet1
> * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
>
> &lt;O nome do cluster > é substituído pelo nome do cluster que você fornece ao usar o modelo.

1. Clique na imagem seguinte para abrir o modelo no portal do Azure. O modelo está localizado nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Na folha **implantação personalizada** , insira as seguintes propriedades:

   * **Subscrição**: Selecione uma assinatura do Azure usada para criar o cluster HDInsight, a conta de armazenamento dependente e a rede virtual do Azure.
   * **Grupo de recursos**: Selecione **criar novo**e especifique um novo nome de grupo de recursos.
   * **Local**: Selecione uma localização para o grupo de recursos.
   * **ClusterName**: Insira um nome para o cluster Hadoop a ser criado.
   * **Nome e senha de logon do cluster**: O nome de início de sessão predefinido é **admin**.
   * **Nome de usuário e senha ssh**: O nome de utilizador predefinido é **sshuser**.  Pode alterá-lo.
   * **Eu concordo com os termos e as condições declaradas acima**: Não
3. Clique em **Comprar**. A criação de um cluster demora cerca de 20 minutos. Depois que o cluster for criado, você poderá clicar na folha do cluster no portal para abri-lo.

Depois de concluir o artigo, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Para obter as instruções de exclusão de um cluster, consulte [gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Para começar a trabalhar com o novo cluster HBase, você pode usar os procedimentos encontrados em Introdução ao [uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Conectar-se ao cluster do Apache HBase usando APIs RPC do Apache HBase Java
1. Crie uma máquina virtual de IaaS (infraestrutura como serviço) na mesma rede virtual do Azure e na mesma sub-rede. Para obter instruções sobre como criar uma nova máquina virtual IaaS, consulte [criar uma máquina virtual executando o Windows Server](../../virtual-machines/windows/quick-create-portal.md). Ao seguir as etapas deste documento, você deve usar os seguintes valores para a configuração de rede:

   * **Rede virtual**: &lt;Nome do cluster >-vnet
   * **Sub-rede**: subnet1

   > [!IMPORTANT]  
   > Substitua &lt;o nome do cluster > pelo nome usado ao criar o cluster HDInsight nas etapas anteriores.

   Usando esses valores, a máquina virtual é colocada na mesma rede virtual e sub-rede que o cluster HDInsight. Essa configuração permite que eles se comuniquem diretamente entre si. Há uma maneira de criar um cluster HDInsight com um nó de borda vazio. O nó de borda pode ser usado para gerenciar o cluster.  Para obter mais informações, consulte [usar nós de borda vazios no HDInsight](../hdinsight-apps-use-edge-node.md).

2. Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o FQDN (nome de domínio totalmente qualificado). Para determinar isso, você deve obter o sufixo DNS específico da conexão do cluster HBase. Para fazer isso, você pode usar um dos seguintes métodos:

   * Use um navegador da Web para fazer uma chamada do [Apache Ambari](https://ambari.apache.org/) :

     Navegue até https://&lt;ClusterName >. azurehdinsight. NET/API/v1/clusteres/&lt;ClusterName >/hosts? minimal_response = true. Ele transforma um arquivo JSON com os sufixos DNS.
   * Use o site do Ambari:

     1. Navegue até https://&lt;ClusterName >. azurehdinsight. net.
     2. Clique em **hosts** no menu superior.
   * Use a ondulação para fazer chamadas REST:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     Nos dados de JavaScript Object Notation (JSON) retornados, localize a entrada "HOST_NAME". Ele contém o FQDN para os nós no cluster. Por exemplo:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     A parte do nome de domínio que começa com o nome do cluster é o sufixo DNS. Por exemplo, mycluster.b1.cloudapp.net.
   * Utilizar o Azure PowerShell

     Use o seguinte script de Azure PowerShell para registrar a função **Get-ClusterDetail** , que pode ser usada para retornar o sufixo DNS:

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

     Depois de executar o script de Azure PowerShell, use o comando a seguir para retornar o sufixo DNS usando a função **Get-ClusterDetail** . Especifique o nome do cluster HBase do HDInsight, o nome do administrador e a senha de administrador ao usar esse comando.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Esse comando retorna o sufixo DNS. Por exemplo, **yourclustername.B4.Internal.cloudapp.net**.


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

Para verificar se a máquina virtual pode se comunicar com o cluster HBase, use o `ping headnode0.<dns suffix>` comando da máquina virtual. Por exemplo, execute ping em headnode0.mycluster.b1.cloudapp.net.

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

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx


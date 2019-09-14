---
title: Planejar uma rede virtual para o Azure HDInsight
description: Saiba como planejar uma implantação de rede virtual do Azure para conectar o HDInsight a outros recursos de nuvem ou recursos em seu datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0c9c729081da9f6c7b8f549a4906d432af6ecb2
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961656"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planejar uma rede virtual para o Azure HDInsight

Este artigo fornece informações básicas sobre como usar [redes virtuais do Azure](../virtual-network/virtual-networks-overview.md) com o Azure HDInsight. Ele também aborda decisões de design e implementação que devem ser feitas antes que você possa implementar uma rede virtual para o cluster HDInsight. Depois que a fase de planejamento for concluída, você poderá continuar [criando redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md). Para obter mais informações sobre os endereços IP de gerenciamento do HDInsight que são necessários para configurar corretamente grupos de segurança de rede e rotas definidas pelo usuário, consulte [endereços IP de gerenciamento do hdinsight](hdinsight-management-ip-addresses.md).

O uso de uma rede virtual do Azure permite os seguintes cenários:

* Conectar-se ao HDInsight diretamente de uma rede local.
* Conectando o HDInsight a armazenamentos de dados em uma rede virtual do Azure.
* Acessando diretamente os serviços [Apache Hadoop](https://hadoop.apache.org/) que não estão disponíveis publicamente pela Internet. Por exemplo, [Apache Kafka](https://kafka.apache.org/) APIs ou a API Java do [Apache HBase](https://hbase.apache.org/) .

> [!IMPORTANT]
> Criar um cluster HDInsight em uma VNET criará vários recursos de rede, como NICs e balanceadores de carga. Não **exclua** esses recursos de rede, pois eles são necessários para que o cluster funcione corretamente com a VNET.
>
> Após 28 de fevereiro de 2019, os recursos de rede (como NICs, LBs, etc.) para novos clusters HDInsight criados em uma VNET serão provisionados no mesmo grupo de recursos de cluster HDInsight. Anteriormente, esses recursos eram provisionados no grupo de recursos de VNET. Não há nenhuma alteração nos clusters em execução atuais e nos clusters criados sem uma VNET.

## <a name="planning"></a>Planeamento

A seguir estão as perguntas que você deve responder ao planejar a instalação do HDInsight em uma rede virtual:

* Você precisa instalar o HDInsight em uma rede virtual existente? Ou você está criando uma nova rede?

    Se você estiver usando uma rede virtual existente, talvez seja necessário modificar a configuração de rede para poder instalar o HDInsight. Para obter mais informações, consulte a seção [Adicionar o HDInsight a uma rede virtual existente](#existingvnet) .

* Deseja conectar a rede virtual que contém o HDInsight a outra rede virtual ou sua rede local?

    Para trabalhar facilmente com recursos em redes, talvez seja necessário criar um DNS personalizado e configurar o encaminhamento de DNS. Para obter mais informações, consulte a seção [Conectando várias redes](#multinet) .

* Deseja restringir/redirecionar o tráfego de entrada ou de saída para o HDInsight?

    O HDInsight deve ter comunicação irrestrita com endereços IP específicos na data center do Azure. Também há várias portas que devem ser permitidas por meio de firewalls para comunicação do cliente. Para obter mais informações, consulte a seção [controlando o tráfego de rede](#networktraffic) .

## <a id="existingvnet"></a>Adicionar o HDInsight a uma rede virtual existente

Use as etapas nesta seção para descobrir como adicionar um novo HDInsight a uma rede virtual do Azure existente.

> [!NOTE]  
> Você não pode adicionar um cluster HDInsight existente a uma rede virtual.

1. Você está usando um modelo de implantação clássico ou do Resource Manager para a rede virtual?

    O HDInsight 3,4 e posterior requer uma rede virtual do Resource Manager. As versões anteriores do HDInsight exigiam uma rede virtual clássica.

    Se sua rede existente for uma rede virtual clássica, você deverá criar uma rede virtual do Resource Manager e, em seguida, conectar as duas. [Conectando VNets clássicas a novas VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Depois de ingressar, o HDInsight instalado na rede do Gerenciador de recursos pode interagir com os recursos na rede clássica.

2. Você usa grupos de segurança de rede, rotas definidas pelo usuário ou dispositivos de rede virtual para restringir o tráfego para dentro ou fora da rede virtual?

    Como um serviço gerenciado, o HDInsight requer acesso irrestrito a vários endereços IP na data center do Azure. Para permitir a comunicação com esses endereços IP, atualize quaisquer grupos de segurança de rede ou rotas definidas pelo usuário existentes.
    
    O HDInsight hospeda vários serviços, que usam uma variedade de portas. Não bloqueie o tráfego para essas portas. Para obter uma lista de portas a serem permitidas por meio de firewalls de dispositivo virtual, consulte a seção segurança.
    
    Para localizar a configuração de segurança existente, use os comandos a seguir Azure PowerShell ou CLI do Azure:

    * Grupos de segurança de rede

        Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Para obter mais informações, consulte o documento [solucionar problemas de grupos de segurança de rede](../virtual-network/diagnose-network-traffic-filter-problem.md) .

        > [!IMPORTANT]  
        > As regras do grupo de segurança de rede são aplicadas em ordem com base na prioridade da regra. A primeira regra que corresponde ao padrão de tráfego é aplicada e nenhuma outra é aplicada para esse tráfego. Ordene as regras da mais permissiva para a menos permissivas. Para obter mais informações, consulte o documento [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md) .

    * Rotas definidas pelo utilizador

        Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Para obter mais informações, consulte o documento [solucionar problemas de rotas](../virtual-network/diagnose-network-routing-problem.md) .

3. Crie um cluster HDInsight e selecione a rede virtual do Azure durante a configuração. Use as etapas nos documentos a seguir para entender o processo de criação de cluster:

    * [Create HDInsight using the Azure portal (Criar o HDInsight com o portal do Azure)](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Create HDInsight using Azure PowerShell (Criar o HDInsight com o Azure PowerShell)](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Criar o HDInsight usando a CLI clássica do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Criar o HDInsight usando um modelo de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Adicionar o HDInsight a uma rede virtual é uma etapa de configuração opcional. Certifique-se de selecionar a rede virtual ao configurar o cluster.

## <a id="multinet"></a>Conectando várias redes

O maior desafio com uma configuração de várias redes é a resolução de nomes entre as redes.

O Azure fornece resolução de nomes para os serviços do Azure que estão instalados em uma rede virtual. Essa resolução de nomes interna permite que o HDInsight se conecte aos seguintes recursos usando um FQDN (nome de domínio totalmente qualificado):

* Qualquer recurso que esteja disponível na Internet. Por exemplo, microsoft.com, windowsupdate.com.

* Qualquer recurso que esteja na mesma rede virtual do Azure, usando o __nome DNS interno__ do recurso. Por exemplo, ao usar a resolução de nome padrão, os seguintes são exemplos de nomes DNS internos atribuídos aos nós de trabalho do HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Ambos os nós podem se comunicar diretamente entre si e outros nós no HDInsight usando nomes DNS internos.

A resolução de nome padrão __não permite que__ o HDInsight resolva os nomes de recursos em redes que ingressaram na rede virtual. Por exemplo, é comum unir sua rede local à rede virtual. Com apenas a resolução de nomes padrão, o HDInsight não pode acessar recursos na rede local por nome. O oposto também é verdadeiro, os recursos em sua rede local não podem acessar recursos na rede virtual por nome.

> [!WARNING]  
> Você deve criar o servidor DNS personalizado e configurar a rede virtual para usá-lo antes de criar o cluster HDInsight.

Para habilitar a resolução de nomes entre a rede virtual e os recursos em redes Unidas, você deve executar as seguintes ações:

1. Crie um servidor DNS personalizado na rede virtual do Azure em que você planeja instalar o HDInsight.

2. Configure a rede virtual para usar o servidor DNS personalizado.

3. Localize o sufixo DNS atribuído do Azure para sua rede virtual. Esse valor é semelhante a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Para obter informações sobre como localizar o sufixo DNS, [consulte o exemplo: Seção DNS](hdinsight-create-virtual-network.md#example-dns) personalizada.

4. Configure o encaminhamento entre os servidores DNS. A configuração depende do tipo de rede remota.

   * Se a rede remota for uma rede local, configure o DNS da seguinte maneira:
        
     * __DNS personalizado__ (na rede virtual):

         * Encaminhe solicitações para o sufixo DNS da rede virtual para o resolvedor recursivo do Azure (168.63.129.16). O Azure lida com solicitações de recursos na rede virtual

         * Encaminhe todas as outras solicitações para o servidor DNS local. O DNS local trata todas as outras solicitações de resolução de nomes, até mesmo solicitações de recursos da Internet, como Microsoft.com.

     * __DNS local__: Encaminhe solicitações para o sufixo DNS da rede virtual para o servidor DNS personalizado. Em seguida, o servidor DNS personalizado encaminha para o resolvedor recursivo do Azure.

       Essa configuração roteia solicitações de nomes de domínio totalmente qualificados que contêm o sufixo DNS da rede virtual para o servidor DNS personalizado. Todas as outras solicitações (mesmo para endereços públicos da Internet) são tratadas pelo servidor DNS local.

   * Se a rede remota for outra rede virtual do Azure, configure o DNS da seguinte maneira:

     * __DNS personalizado__ (em cada rede virtual):

         * As solicitações para o sufixo DNS das redes virtuais são encaminhadas para os servidores DNS personalizados. O DNS em cada rede virtual é responsável por resolver recursos em sua rede.

         * Encaminhe todas as outras solicitações para o resolvedor recursivo do Azure. O resolvedor recursivo é responsável por resolver recursos locais e da Internet.

       O servidor DNS para cada rede encaminha as solicitações para a outra, com base no sufixo DNS. Outras solicitações são resolvidas usando o resolvedor recursivo do Azure.

     Para obter um exemplo de cada configuração, consulte [o exemplo: Seção DNS](hdinsight-create-virtual-network.md#example-dns) personalizada.

Para obter mais informações, consulte o documento [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) .

## <a name="directly-connect-to-apache-hadoop-services"></a>Conectar-se diretamente a serviços Apache Hadoops

Você pode se conectar ao cluster em `https://CLUSTERNAME.azurehdinsight.net`. Esse endereço usa um IP público, que pode não estar acessível se você tiver usado NSGs para restringir o tráfego de entrada da Internet. Além disso, ao implantar o cluster em uma VNet, você pode acessá-lo usando `https://CLUSTERNAME-int.azurehdinsight.net`o ponto de extremidade privado. Esse ponto de extremidade é resolvido para um IP privado dentro da VNet para acesso ao cluster.

Para se conectar ao Apache Ambari e a outras páginas da Web por meio da rede virtual, use as seguintes etapas:

1. Para descobrir os nomes de domínio totalmente qualificados (FQDN) internos dos nós do cluster HDInsight, use um dos seguintes métodos:

    Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na lista de nós retornada, localize o FQDN para os nós de cabeçalho e use os FQDNs para se conectar ao Ambari e a outros serviços Web. Por exemplo, use `http://<headnode-fqdn>:8080` para acessar o Ambari.

    > [!IMPORTANT]  
    > Alguns serviços hospedados nos nós de cabeçalho estão ativos apenas em um nó por vez. Se você tentar acessar um serviço em um nó de cabeçalho e ele retornar um erro 404, alterne para o outro nó de cabeçalho.

2. Para determinar o nó e a porta em que um serviço está disponível, consulte o documento [portas usadas por serviços do Hadoop no HDInsight](./hdinsight-hadoop-port-settings-for-services.md) .

## <a id="networktraffic"></a>Controlando o tráfego de rede

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Técnicas para controlar o tráfego de entrada e de saída para clusters HDInsight

O tráfego de rede em redes virtuais do Azure pode ser controlado usando os seguintes métodos:

* **Grupos de segurança de rede** (NSG) permite filtrar o tráfego de entrada e de saída para a rede. Para obter mais informações, consulte o documento [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md) .

* **Dispositivos de rede virtual** (NVA) pode ser usado somente com tráfego de saída. NVAs replicar a funcionalidade de dispositivos como firewalls e roteadores. Para obter mais informações, consulte o documento [dispositivos de rede](https://azure.microsoft.com/solutions/network-appliances) .

Como um serviço gerenciado, o HDInsight requer acesso irrestrito aos serviços de integridade e gerenciamento do HDInsight para o tráfego de entrada e saída da VNET. Ao usar o NSGs, você deve garantir que esses serviços ainda possam se comunicar com o cluster HDInsight.

![Diagrama de entidades do HDInsight criadas na VNET personalizada do Azure](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight com grupos de segurança de rede

Se você planeja usar **grupos de segurança de rede** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifique a região do Azure que você planeja usar para o HDInsight.

2. Identifique os endereços IP exigidos pelo HDInsight. Para obter mais informações, veja [Endereços IP de gestão do HDInsight](hdinsight-management-ip-addresses.md).

3. Crie ou modifique os grupos de segurança de rede para a sub-rede na qual você planeja instalar o HDInsight.

    * __Grupos de segurança de rede__: permitir o tráfego de __entrada__ na porta __443__ a partir dos endereços IP. Isso garantirá que os serviços de gerenciamento do HDInsight possam acessar o cluster de fora da rede virtual.

Para obter mais informações sobre grupos de segurança de rede, consulte a [visão geral dos grupos de segurança de rede](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlando o tráfego de saída de clusters HDInsight

Para obter mais informações sobre como controlar o tráfego de saída de clusters HDInsight, consulte [Configurar a restrição de tráfego de rede de saída para clusters do Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premise"></a>Túnel forçado para o local

O túnel forçado é uma configuração de roteamento definida pelo usuário, em que todo o tráfego de uma sub-rede é forçado a uma rede ou local específico, como sua rede local. __O HDInsight não dá__ suporte ao túnel forçado de tráfego para redes locais. 

## <a id="hdinsight-ip"></a>Endereços IP necessários

Se você usar grupos de segurança de rede ou rotas definidas pelo usuário para controlar o tráfego, consulte [endereços IP de gerenciamento do HDInsight](hdinsight-management-ip-addresses.md).
    
## <a id="hdinsight-ports"></a>Portas necessárias

Se você planeja usar um **Firewall** e acessar o cluster de fora em determinadas portas, talvez seja necessário permitir o tráfego nessas portas necessárias para seu cenário. Por padrão, nenhuma lista de permissões especial de portas é necessária, contanto que o tráfego de gerenciamento do Azure explicado na seção anterior tenha permissão para alcançar o cluster na porta 443.

Para obter uma lista de portas para serviços específicos, consulte o documento [portas usadas pelo Apache Hadoop Services no HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

Para obter mais informações sobre regras de firewall para dispositivos virtuais, consulte o documento [cenário de dispositivo virtual](../virtual-network/virtual-network-scenario-udr-gw-nva.md) .

## <a name="next-steps"></a>Passos Seguintes

* Para obter exemplos de código e exemplos de criação de redes virtuais do Azure, consulte [criar redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md).
* Para obter um exemplo de ponta a ponta de como configurar o HDInsight para se conectar a uma rede local, consulte [conectar o hdinsight a uma rede local](./connect-on-premises-network.md).
* Para configurar clusters do Apache HBase em redes virtuais do Azure, confira [Criar clusters do Apache HBase no HDInsight na rede virtual do Azure](hbase/apache-hbase-provision-vnet.md).
* Para configurar a replicação geográfica do Apache HBase, confira [Configurar a replicação de cluster do Apache HBase em redes virtuais do Azure](hbase/apache-hbase-replication.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte [visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).
* Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](../virtual-network/security-overview.md).
* Para obter mais informações sobre rotas definidas pelo usuário, consulte [rotas definidas pelo usuário e encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md).

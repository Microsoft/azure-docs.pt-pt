---
title: Planeie uma rede virtual para o Azure HDInsight
description: Saiba como planear uma implementação da Rede Virtual Azure para ligar o HDInsight a outros recursos na nuvem, ou recursos no seu datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 30664d533215cb49fa6f436ec4cf88fa319c3300
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387228"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planeie uma rede virtual para o Azure HDInsight

Este artigo fornece informações de fundo sobre a utilização de [Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md) (VNets) com o Azure HDInsight. Também discute decisões de design e implementação que devem ser tomadas antes de poder implementar uma rede virtual para o seu cluster HDInsight. Uma vez terminada a fase de planeamento, pode proceder à Criação de [redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md). Para obter mais informações sobre os endereços IP de gestão HDInsight que são necessários para configurar corretamente os grupos de segurança da rede (NSGs) e as rotas definidas pelo utilizador, consulte os endereços IP de [gestão HDInsight](hdinsight-management-ip-addresses.md).

A utilização de uma Rede Virtual Azure permite os seguintes cenários:

* Conectando-se ao HDInsight diretamente de uma rede no local.
* Ligar o HDInsight a lojas de dados numa rede Azure Virtual.
* Acedendo diretamente aos serviços Apache Hadoop que não estão disponíveis publicamente através da internet. Por exemplo, Apache Kafka APIs ou a Apache HBase Java API.

> [!IMPORTANT]
> A criação de um cluster HDInsight num VNET criará vários recursos de networking, tais como NICs e equilibradores de carga. Não elimine estes recursos de rede, pois são necessários para que o seu cluster funcione corretamente com o VNET.
>
> Depois de 28 de fevereiro de 2019, os recursos de networking (tais como NICs, LBs, etc) para novos clusters HDInsight criados num VNET serão aprovisionados no mesmo grupo de recursos de cluster HDInsight. Anteriormente, estes recursos eram provisionados no grupo de recursos VNET. Não há alteração nos atuais clusters de execução e nos clusters criados sem um VNET.

## <a name="planning"></a>Planeamento

Seguem-se as perguntas que deve responder ao planear instalar o HDInsight numa rede virtual:

* Precisa de instalar o HDInsight numa rede virtual existente? Ou estás a criar uma nova rede?

    Se estiver a utilizar uma rede virtual existente, poderá ter de modificar a configuração da rede antes de poder instalar o HDInsight. Para mais informações, consulte a [adição de HDInsight a uma](#existingvnet) secção de rede virtual existente.

* Deseja ligar a rede virtual que contém o HDInsight a outra rede virtual ou à sua rede no local?

    Para trabalhar facilmente com recursos através das redes, poderá ser necessário criar um DNS personalizado e configurar o reencaminhamento de DNS. Para mais informações, consulte a secção de [ligação de várias redes.](#multinet)

* Deseja restringir/redirecionar o tráfego de entrada ou saída para o HDInsight?

    O HDInsight deve ter uma comunicação sem restrições com endereços IP específicos no centro de dados Azure. Existem também várias portas que devem ser permitidas através de firewalls para comunicação do cliente. Para mais informações, consulte a secção de tráfego da [rede de controlo.](#networktraffic)

## <a id="existingvnet"></a>Adicione hDInsight a uma rede virtual existente

Utilize os passos nesta secção para descobrir como adicionar um novo HDInsight a uma rede virtual Azure existente.

> [!NOTE]  
> Não é possível adicionar um cluster HDInsight existente numa rede virtual.

1. Está a usar um modelo clássico ou de implantação do Gestor de Recursos para a rede virtual?

    HDInsight 3.4 e maior requer uma rede virtual do Gestor de Recursos. Versões anteriores do HDInsight requeriam uma rede virtual clássica.

    Se a sua rede existente é uma rede virtual clássica, então deve criar uma rede virtual do Gestor de Recursos e, em seguida, ligar as duas. [Ligando VNets clássicos a novos VNets.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)

    Uma vez aderido, o HDInsight instalado na rede Derecursos Pode interagir com recursos na rede clássica.

2. Utiliza grupos de segurança de rede, rotas definidas pelo utilizador ou aparelhos de rede virtuais para restringir o tráfego dentro ou fora da rede virtual?

    Como um serviço gerido, o HDInsight requer acesso ilimitado a vários endereços IP no centro de dados Azure. Para permitir a comunicação com estes endereços IP, atualize quaisquer grupos de segurança de rede existentes ou rotas definidas pelo utilizador.

    O HDInsight acolhe vários serviços, que utilizam uma variedade de portas. Não bloqueie o tráfego para estes portos. Para obter uma lista de portas para permitir através de firewalls de aparelhos virtuais, consulte a secção de Segurança.

    Para encontrar a configuração de segurança existente, utilize os seguintes comandos Azure PowerShell ou Azure CLI:

    * Grupos de segurança de rede

        Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Para mais informações, consulte o documento dos grupos de [segurança da rede Troubleshoot.](../virtual-network/diagnose-network-traffic-filter-problem.md)

        > [!IMPORTANT]  
        > As regras do grupo de segurança da rede são aplicadas por ordem com base na prioridade da regra. A primeira regra que corresponde ao padrão de tráfego é aplicada, e nenhum outro é aplicado para esse tráfego. Regras de ordem da maioria permissiva ao menos permissiva. Para obter mais informações, consulte o tráfego da rede Filter com documento [de grupos](../virtual-network/security-overview.md) de segurança da rede.

    * Rotas definidas pelo utilizador

        Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Para mais informações, consulte o documento das [rotas Troubleshoot.](../virtual-network/diagnose-network-routing-problem.md)

3. Crie um cluster HDInsight e selecione a Rede Virtual Azure durante a configuração. Utilize os passos nos seguintes documentos para compreender o processo de criação do cluster:

    * [Create HDInsight using the Azure portal (Criar o HDInsight com o portal do Azure)](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Create HDInsight using Azure PowerShell (Criar o HDInsight com o Azure PowerShell)](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Criar hDInsight usando o Azure Classic CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Crie hDInsight usando um modelo de Gestor de Recursos Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Adicionar o HDInsight a uma rede virtual é um passo de configuração opcional. Certifique-se de selecionar a rede virtual ao configurar o cluster.

## <a id="multinet"></a>Ligação de várias redes

O maior desafio com uma configuração multi-rede é a resolução de nomes entre as redes.

O Azure fornece uma resolução de nome seletiva para os serviços Azure que estão instalados numa rede virtual. Esta resolução de nome incorporado permite ao HDInsight ligar-se aos seguintes recursos utilizando um nome de domínio totalmente qualificado (FQDN):

* Qualquer recurso que esteja disponível na internet. Por exemplo, microsoft.com, windowsupdate.com.

* Qualquer recurso que esteja na mesma Rede Virtual Azure, utilizando o __nome Interno DNS__ do recurso. Por exemplo, ao utilizar a resolução de nome predefinido, os seguintes são exemplos de nomes internos de DNS atribuídos aos nós dos trabalhadores da HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Ambos os nós podem comunicar diretamente uns com os outros, e outros nós no HDInsight, utilizando nomes internos de DNS.

A resolução de nome padrão __não__ permite que o HDInsight resolva os nomes dos recursos em redes que estão unidas à rede virtual. Por exemplo, é comum juntar a sua rede no local à rede virtual. Com apenas a resolução de nome padrão, o HDInsight não pode aceder a recursos na rede no local pelo nome. O contrário também é verdade, os recursos na sua rede no local não podem aceder a recursos na rede virtual pelo nome.

> [!WARNING]  
> Tem de criar o servidor DNS personalizado e configurar a rede virtual para a utilizar antes de criar o cluster HDInsight.

Para permitir a resolução de nomes entre a rede virtual e os recursos em redes unidas, deve realizar as seguintes ações:

1. Crie um servidor DNS personalizado na Rede Virtual Azure onde planeia instalar o HDInsight.

2. Configure a rede virtual para utilizar o servidor DNS personalizado.

3. Encontre o sufixo DNS atribuído ao Azure para a sua rede virtual. Este valor é semelhante ao `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Para obter informações sobre a descoberta do sufixo DNS, consulte a secção [Exemplo: DNS personalizada.](hdinsight-create-virtual-network.md#example-dns)

4. Configure o encaminhamento entre os servidores DNS. A configuração depende do tipo de rede remota.

   * Se a rede remota for uma rede no local, configure o DNS da seguinte forma:

     * __DNS personalizados__ (na rede virtual):

         * Pedidos a prazo para o sufixo DNS da rede virtual para o Azure resolver (168.63.129.16). Azure lida com pedidos de recursos na rede virtual

         * Encaminhar todos os outros pedidos para o servidor DNS no local. O DNS no local trata de todos os outros pedidos de resolução de nomes, mesmo pedidos de recursos da Internet, como Microsoft.com.

     * __No local DNS__: Pedidos avançados para o sufixo DNS da rede virtual para o servidor DNS personalizado. O servidor DNS personalizado encaminha-se então para o Resolver recursivo do Azure.

       Esta configuração solicita nomes de domínio totalmente qualificados que contenham o sufixo DNS da rede virtual para o servidor DNS personalizado. Todos os outros pedidos (mesmo para endereços de internet públicos) são tratados pelo servidor DNS no local.

   * Se a rede remota for outra Rede Virtual Azure, configure o DNS da seguinte forma:

     * __DNS personalizados__ (em cada rede virtual):

         * Os pedidos para o sufixo DNS das redes virtuais são encaminhados para os servidores DNS personalizados. O DNS em cada rede virtual é responsável pela resolução de recursos dentro da sua rede.

         * Encaminhar todos os outros pedidos para o Azure resolver. O resolver recursivo é responsável pela resolução dos recursos locais e da Internet.

       O servidor DNS para cada rede reencaminha pedidos para a outra, com base no sufixo DNS. Outros pedidos são resolvidos utilizando o resolver recursivo Azure.

     Para um exemplo de cada configuração, consulte a secção [Exemplo: DNS personalizada.](hdinsight-create-virtual-network.md#example-dns)

Para mais informações, consulte o documento de Resolução de [Nomes para VMs e Role Instances.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="directly-connect-to-apache-hadoop-services"></a>Ligue-se diretamente aos serviços Apache Hadoop

Pode ligar-se ao aglomerado em `https://CLUSTERNAME.azurehdinsight.net`. Este endereço utiliza um IP público, que pode não ser acessível se tiver usado NSGs para restringir o tráfego de entrada a partir da internet. Além disso, quando implanta o cluster num VNet, pode aceder-lhe utilizando o ponto final privado `https://CLUSTERNAME-int.azurehdinsight.net`. Este ponto final resolve um IP privado dentro do VNet para acesso ao cluster.

Para ligar ao Apache Ambari e outras páginas web através da rede virtual, utilize os seguintes passos:

1. Para descobrir os nomes internos de domínio totalmente qualificados (FQDN) dos nós de cluster HDInsight, utilize um dos seguintes métodos:

    Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

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

    Na lista de nós devolvidos, encontre o FQDN para os nós da cabeça e use os FQDNs para ligar a Ambari e outros serviços web. Por exemplo, use `http://<headnode-fqdn>:8080` para aceder a Ambari.

    > [!IMPORTANT]  
    > Alguns serviços hospedados nos nóóis de cabeça só estão ativos num nó de cada vez. Se tentar aceder a um serviço num nó de cabeça e devolver um erro de 404, mude para o outro nó da cabeça.

2. Para determinar o nó e a porta em que um serviço está disponível, consulte as [Portas utilizadas pelos serviços Hadoop no documento HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

## <a id="networktraffic"></a>Controlar o tráfego da rede

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Técnicas para controlar o tráfego de entrada e saída para os clusters HDInsight

O tráfego de rede numa Rede Virtual Azure pode ser controlado utilizando os seguintes métodos:

* **Os grupos** de segurança da rede (NSG) permitem filtrar o tráfego de entrada e saída para a rede. Para obter mais informações, consulte o tráfego da rede Filter com documento [de grupos](../virtual-network/security-overview.md) de segurança da rede.

* **Os aparelhos virtuais** da rede (NVA) só podem ser utilizados com tráfego de saída. Os NVAs replicam a funcionalidade de dispositivos como firewalls e routers. Para mais informações, consulte o documento [de Aparelhos de Rede.](https://azure.microsoft.com/solutions/network-appliances)

Como um serviço gerido, o HDInsight requer acesso ilimitado aos serviços de saúde e gestão HDInsight, tanto para o tráfego de entrada como para o tráfego de saída do VNET. Ao utilizar NSGs, deve garantir que estes serviços ainda podem comunicar com o cluster HDInsight.

![Diagrama de entidades HDInsight criadas em VNET personalizado Azure](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight com grupos de segurança de rede

Se planeia utilizar **grupos** de segurança de rede para controlar o tráfego da rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifique a região azure que pretende utilizar para o HDInsight.

2. Identifique as etiquetas de serviço exigidas pelo HDInsight para a sua região. Para mais informações, consulte as etiquetas de serviço do Grupo de [Segurança da Rede (NSG) para o Azure HDInsight](hdinsight-service-tags.md).

3. Crie ou modifique os grupos de segurança da rede para a subnet a que planeia instalar o HDInsight.

    * __Grupos__de segurança da rede : permitir o tráfego __de entrada__ no porto __443__ a partir dos endereços IP. Isto garantirá que os serviços de gestão HDInsight possam chegar ao cluster de fora da rede virtual.

Para obter mais informações sobre os grupos de segurança da rede, consulte a [visão geral dos grupos de segurança da rede](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlar o tráfego de saída dos clusters HDInsight

Para obter mais informações sobre o controlo do tráfego de saída dos clusters HDInsight, consulte a restrição de tráfego de rede de [saída da Configure para os clusters Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Túneis forçados para o local

A escavação forçada é uma configuração de encaminhamento definida pelo utilizador onde todo o tráfego de uma subnet é forçado a uma rede ou localização específica, como a sua rede no local. O HDInsight __não__ suporta a escavação forçada do tráfego para as redes no local.

## <a id="hdinsight-ip"></a>Endereços IP necessários

Se utilizar grupos de segurança de rede ou rotas definidas pelo utilizador para controlar o tráfego, consulte os endereços IP de [gestão HDInsight](hdinsight-management-ip-addresses.md).

## <a id="hdinsight-ports"></a>Portas necessárias

Se planeia utilizar uma **firewall** e aceder ao cluster de fora em certas portas, poderá ter de permitir o tráfego nas portas necessárias para o seu cenário. Por predefinição, não é necessária uma lista especial de portas, desde que o tráfego de gestão azul explicado na secção anterior seja autorizado a atingir o cluster na porta 443.

Para obter uma lista de portas para serviços específicos, consulte as [Portas utilizadas pelos serviços Apache Hadoop no documento HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

Para obter mais informações sobre as regras de firewall para aparelhos virtuais, consulte o documento do [cenário do aparelho virtual.](../virtual-network/virtual-network-scenario-udr-gw-nva.md)

## <a name="load-balancing"></a>Balanceamento de carga

Quando se cria um cluster HDInsight, é criado um equilibrador de carga também. O tipo deste equilibrista de carga está no nível básico de [SKU,](../load-balancer/concepts-limitations.md#skus)que tem certos constrangimentos. Um desses constrangimentos é que se tiver duas redes virtuais em diferentes regiões, não pode ligar-se a equilibradores básicos de carga. Consulte [as redes virtuais FAQ: restrições no peering global vnet,](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)para mais informações.

## <a name="transport-layer-security"></a>Segurança da camada de transporte

As ligações ao cluster através do ponto final do cluster público `https://<clustername>.azurehdinsight.net` são proxid através de nós de gateway cluster. Estas ligações são protegidas usando um protocolo chamado TLS. A aplicação de versões mais elevadas de TLS nos gateways melhora a segurança destas ligações. Para obter mais informações sobre o porquê de utilizar versões mais recentes de TLS, consulte [A resolução do Problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Por padrão, os clusters Azure HDInsight aceitam ligações TLS 1.2 em pontos finais HTTPS públicos, bem como versões mais antigas para retrocompatibilidade. Pode controlar a versão TLS mínima suportada nos nós de gateway durante a criação do cluster utilizando o portal Azure, ou um modelo de gestor de recursos. Para o portal, selecione a versão TLS do separador **de rede Security +** durante a criação do cluster. Para um modelo de gestor de recursos no momento de implantação, utilize a propriedade **minSupportedTlsVersion.** Para um modelo de amostra, consulte o [modelo mínimo TLS 1.2 Quickstart HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Este imóvel suporta três valores: "1.0", "1.1" e "1.2", que correspondem a TLS 1.0+, TLS 1.1+ e TLS 1.2+, respectivamente.

> [!IMPORTANT]
> A partir de 30 de junho de 2020, o Azure HDInsight aplicará versões TLS 1.2 ou posteriores para todas as ligações HTTPS. Recomendamos que garanta que todos os seus clientes estão prontos para lidar com versões TLS 1.2 ou posteriores. Para mais informações, consulte [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Passos seguintes

* Para amostras de código e exemplos de criação de Redes Virtuais Azure, consulte [Criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* Para um exemplo de configuração de HDInsight para ligar a uma rede no local, consulte [O Connect HDInsight para uma rede no local](./connect-on-premises-network.md).
* Para configurar clusters Apache HBase em redes virtuais Azure, consulte [Create Apache HBase clusters no HDInsight em Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Para configurar a geo-replicação apache HBase, consulte [Configurar a replicação do cluster Apache HBase nas redes virtuais Azure](hbase/apache-hbase-replication.md).
* Para obter mais informações sobre as redes virtuais do Azure, consulte a [visão geral da Rede Virtual Azure.](../virtual-network/virtual-networks-overview.md)
* Para obter mais informações sobre grupos de segurança da rede, consulte [grupos](../virtual-network/security-overview.md)de segurança da rede .
* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte as rotas definidas pelo utilizador e o [reencaminhamento ip](../virtual-network/virtual-networks-udr-overview.md).

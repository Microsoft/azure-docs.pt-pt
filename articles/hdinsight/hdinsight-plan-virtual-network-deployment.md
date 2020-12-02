---
title: Planeie uma rede virtual para a Azure HDInsight
description: Saiba como planear uma implementação da Rede Virtual Azure para ligar o HDInsight a outros recursos na nuvem ou recursos no seu datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 05/04/2020
ms.openlocfilehash: 750e8cc59f196fda6bf3a816885c3c0b29a69a56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451909"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planeie uma rede virtual para a Azure HDInsight

Este artigo fornece informações de fundo sobre a utilização de [Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md) (VNets) com Azure HDInsight. Também discute decisões de design e implementação que devem ser tomadas antes de implementar uma rede virtual para o seu cluster HDInsight. Uma vez terminada a fase de planeamento, pode proceder à [criação de redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md). Para obter mais informações sobre endereços IP de gestão hdInsight necessários para configurar adequadamente os grupos de segurança da rede (NSGs) e as rotas definidas pelo utilizador, consulte [endereços IP de gestão hdInsight](hdinsight-management-ip-addresses.md).

A utilização de uma Rede Virtual Azure permite os seguintes cenários:

* Ligação ao HDInsight diretamente a partir de uma rede no local.
* Ligação hdInsight às lojas de dados numa rede Virtual Azure.
* Acede diretamente aos serviços da Apache Hadoop que não estão disponíveis publicamente através da internet. Por exemplo, Apache Kafka APIs ou a Apache HBase Java API.

> [!IMPORTANT]
> A criação de um cluster HDInsight num VNET criará vários recursos de networking, tais como NICs e equilibradores de carga. **Não** elimine estes recursos de rede, pois são necessários para que o seu cluster funcione corretamente com o VNET.
>
> Após 28 de fevereiro de 2019, os recursos de networking (tais como NICs, LBs, etc) para novos clusters HDInsight criados num VNET serão aprovisionados no mesmo grupo de recursos de cluster HDInsight. Anteriormente, estes recursos foram aprovisionados no grupo de recursos VNET. Não há alteração nos aglomerados de funcionamento atuais e nos aglomerados criados sem um VNET.

## <a name="planning"></a>Planeamento

Seguem-se as perguntas que deve responder ao planear instalar o HDInsight numa rede virtual:

* Precisa de instalar o HDInsight numa rede virtual existente? Ou estás a criar uma nova rede?

    Se estiver a utilizar uma rede virtual existente, poderá ter de modificar a configuração da rede antes de poder instalar o HDInsight. Para obter mais informações, consulte o [add HDInsight a uma](#existingvnet) secção de rede virtual existente.

* Deseja ligar a rede virtual que contém HDInsight a outra rede virtual ou à sua rede no local?

    Para trabalhar facilmente com recursos através de redes, poderá ser necessário criar um DNS personalizado e configurar o reencaminhamento de DNS. Para obter mais informações, consulte a secção [de várias redes de ligação.](#multinet)

* Deseja restringir/redirecionar o tráfego de entrada ou saída para HDInsight?

    O HDInsight deve ter uma comunicação sem restrições com endereços IP específicos no centro de dados Azure. Há também várias portas que devem ser permitidas através de firewalls para comunicação do cliente. Para obter mais informações, consulte [o tráfego da rede de controlo.](./control-network-traffic.md)

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Adicione HDInsight a uma rede virtual existente

Utilize os passos nesta secção para descobrir como adicionar um novo HDInsight a uma Rede Virtual Azure existente.

> [!NOTE]  
> Não é possível adicionar um cluster HDInsight existente numa rede virtual.

1. Está a utilizar um modelo de implementação clássico ou gestor de recursos para a rede virtual?

    HDInsight 3.4 e maior requer uma rede virtual Do Gestor de Recursos. Versões anteriores do HDInsight necessitavam de uma rede virtual clássica.

    Se a sua rede existente é uma rede virtual clássica, então deve criar uma rede virtual Resource Manager e, em seguida, ligar os dois. [Ligação de VNets clássicos a novos VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Uma vez ligado, o HDInsight instalado na rede Resource Manager pode interagir com recursos na rede clássica.

2. Utiliza grupos de segurança de rede, rotas definidas pelo utilizador ou Aparelhos de Rede Virtual para restringir o tráfego dentro ou fora da rede virtual?

    Como um serviço gerido, o HDInsight requer acesso ilimitado a vários endereços IP no centro de dados Azure. Para permitir a comunicação com estes endereços IP, atualize quaisquer grupos de segurança de rede existentes ou rotas definidas pelo utilizador.

    O HDInsight acolhe vários serviços, que utilizam uma variedade de portas. Não bloqueie o tráfego para estes portos. Para obter uma lista de portas para permitir através de firewalls de aparelhos virtuais, consulte a secção de Segurança.

    Para encontrar a configuração de segurança existente, utilize os seguintes comandos Azure PowerShell ou Azure CLI:

    * Grupos de segurança de rede

        `RESOURCEGROUP`Substitua-o pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Para obter mais informações, consulte o documento dos [grupos de segurança da rede Troubleshoot.](../virtual-network/diagnose-network-traffic-filter-problem.md)

        > [!IMPORTANT]  
        > As regras do grupo de segurança da rede são aplicadas com base na prioridade da regra. A primeira regra que corresponde ao padrão de tráfego é aplicada, e nenhuma outra é aplicada para esse tráfego. Ordem regras da maioria permissiva a menos permissiva. Para obter mais informações, consulte o tráfego da rede Filter com o documento [dos grupos de segurança da rede.](../virtual-network/network-security-groups-overview.md)

    * Rotas definidas pelo utilizador

        `RESOURCEGROUP`Substitua-o pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Para mais informações, consulte o documento [de rotas de resolução de problemas.](../virtual-network/diagnose-network-routing-problem.md)

3. Crie um cluster HDInsight e selecione a Rede Virtual Azure durante a configuração. Utilize os passos nos seguintes documentos para compreender o processo de criação de clusters:

    * [Create HDInsight using the Azure portal (Criar o HDInsight com o portal do Azure)](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Create HDInsight using Azure PowerShell (Criar o HDInsight com o Azure PowerShell)](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Criar HDInsight usando O Azure Classic CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Crie HDInsight usando um modelo de Gestor de Recursos Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Adicionar HDInsight a uma rede virtual é um passo de configuração opcional. Certifique-se de que seleciona a rede virtual ao configurar o cluster.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Ligação de várias redes

O maior desafio com uma configuração multi-rede é a resolução de nomes entre as redes.

O Azure fornece resolução de nomes para os serviços Azure que estão instalados numa rede virtual. Esta resolução de nome incorporado permite que o HDInsight se conecte aos seguintes recursos utilizando um nome de domínio totalmente qualificado (FQDN):

* Qualquer recurso que esteja disponível na internet. Por exemplo, microsoft.com, windowsupdate.com.

* Qualquer recurso que esteja na mesma Rede Virtual Azure, utilizando o __nome DNS interno__ do recurso. Por exemplo, ao utilizar a resolução de nomes predefinidos, são exemplos de nomes internos de DNS atribuídos aos nóns de trabalhadores HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Ambos os nós podem comunicar diretamente uns com os outros, e outros nós em HDInsight, usando nomes dns internos.

A resolução de nomes predefinidos __não__ permite que a HDInsight resolva os nomes dos recursos nas redes que se juntam à rede virtual. Por exemplo, é comum juntar a sua rede no local à rede virtual. Com apenas a resolução de nome padrão, o HDInsight não pode aceder a recursos na rede no local pelo nome. O oposto também é verdade, os recursos na sua rede no local não conseguem aceder a recursos na rede virtual pelo nome.

> [!WARNING]  
> Tem de criar o servidor DNS personalizado e configurar a rede virtual para a utilizar antes de criar o cluster HDInsight.

Para permitir a resolução de nomes entre a rede virtual e os recursos em redes unidas, deve executar as seguintes ações:

1. Crie um servidor DNS personalizado na Rede Virtual Azure onde planeia instalar o HDInsight.

2. Configure a rede virtual para utilizar o servidor DNS personalizado.

3. Encontre o sufixo DNS atribuído ao Azure para a sua rede virtual. Este valor é semelhante a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` . Para obter informações sobre a descoberta do sufixo DNS, consulte a secção [Exemplo: DNS personalizado.](hdinsight-create-virtual-network.md#example-dns)

4. Configurar o encaminhamento entre os servidores DNS. A configuração depende do tipo de rede remota.

   * Se a rede remota for uma rede no local, configuure o DNS da seguinte forma:

     * __DNS personalizados__ (na rede virtual):

         * Reencamúndio dos pedidos de sufância de DNS da rede virtual para o Azure recursive resolver (168.63.129.16). Azure lida com pedidos de recursos na rede virtual

         * Encaminhe todos os outros pedidos para o servidor DNS no local. O DNS no local trata de todos os outros pedidos de resolução de nomes, mesmo pedidos de recursos da Internet, como Microsoft.com.

     * __No local DNS__: Encaminhar os pedidos para o sufixo DNS da rede virtual para o servidor DNS personalizado. O servidor DNS personalizado, em seguida, encaminha-se para o Azure recursive resolver.

       Esta configuração encaminha os pedidos de nomes de domínio totalmente qualificados que contenham o sufixo DNS da rede virtual para o servidor DNS personalizado. Todos os outros pedidos (mesmo para endereços de internet público) são tratados pelo servidor DNS no local.

   * Se a rede remota for outra Rede Virtual Azure, configure DNS da seguinte forma:

     * __DNS personalizados__ (em cada rede virtual):

         * Os pedidos para o sufixo DNS das redes virtuais são reencaminhados para os servidores DNS personalizados. O DNS em cada rede virtual é responsável pela resolução de recursos dentro da sua rede.

         * Encaminhe todos os outros pedidos para o Azure recursive resolver. A resolução recursiva é responsável pela resolução dos recursos locais e da internet.

       O servidor DNS de cada rede remete pedidos para o outro, com base no sufixo DNS. Outros pedidos são resolvidos usando o Azure recursive resolver.

     Para um exemplo de cada configuração, consulte a secção [Exemplo: DNS personalizado.](hdinsight-create-virtual-network.md#example-dns)

Para mais informações, consulte o documento [de Resolução de Nomes para VMs e Instâncias de Papel.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="directly-connect-to-apache-hadoop-services"></a>Ligue-se diretamente aos serviços da Apache Hadoop

Pode ligar-se ao cluster em `https://CLUSTERNAME.azurehdinsight.net` . Este endereço utiliza um IP público, que pode não ser acessível se tiver usado NSGs para restringir o tráfego de entrada da internet. Além disso, quando implantar o cluster num VNet, pode aceder-lhe utilizando o ponto final `https://CLUSTERNAME-int.azurehdinsight.net` privado. Este ponto final resolve-se a um IP privado dentro do VNet para acesso ao cluster.

Para ligar ao Apache Ambari e a outras páginas web através da rede virtual, utilize os seguintes passos:

1. Para descobrir os nomes de domínio internos totalmente qualificados (FQDN) dos nós do cluster HDInsight, utilize um dos seguintes métodos:

    `RESOURCEGROUP`Substitua-o pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

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

    Na lista de nós devolvidos, encontre o FQDN para os nós de cabeça e use os FQDNs para ligar a Ambari e outros serviços web. Por exemplo, use `http://<headnode-fqdn>:8080` para aceder a Ambari.

    > [!IMPORTANT]  
    > Alguns serviços alojados nos nós da cabeça só estão ativos num nó de cada vez. Se tentar aceder a um serviço num nó de cabeça e retornar um erro de 404, mude para o outro nó de cabeça.

2. Para determinar o nó e a porta em que um serviço está disponível, consulte as [portas utilizadas pelos serviços Hadoop no documento HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

## <a name="load-balancing"></a>Balanceamento de carga

Quando cria um cluster HDInsight, também é criado um equilibrador de carga. O tipo deste equilibrador de carga está no [nível básico de SKU,](../load-balancer/skus.md)que tem certos constrangimentos. Um desses constrangimentos é que se tiver duas redes virtuais em diferentes regiões, não pode ligar-se aos equilibradores básicos de carga. Consulte [as redes virtuais FAQ: restrições no perspípeso global de vnet,](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

* Para obter amostras de código e exemplos de criação de Redes Virtuais Azure, consulte [criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* Para obter um exemplo de configuração hdinsight para ligar a uma rede no local, consulte [Connect HDInsight a uma rede no local](./connect-on-premises-network.md).
* Para obter mais informações sobre as redes virtuais Azure, consulte a [visão geral da Rede Virtual Azure](../virtual-network/virtual-networks-overview.md).
* Para obter mais informações sobre grupos de segurança de rede, consulte [os grupos de segurança da rede](../virtual-network/network-security-groups-overview.md).
* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [as rotas definidas pelo Utilizador e o encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).
* Para obter mais informações sobre o controlo do tráfego, incluindo a integração do Firewall, consulte [o tráfego da rede de controlo](./control-network-traffic.md).

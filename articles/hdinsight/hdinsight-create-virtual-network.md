---
title: Criar redes virtuais para clusters Azure HDInsight
description: Aprenda a criar uma Rede Virtual Azure para ligar o HDInsight a outros recursos na nuvem, ou recursos no seu datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 0c7791d43ffbbc13ab151362c5c3026ebbdb0d34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81531021"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Criar redes virtuais para clusters Azure HDInsight

Este artigo fornece exemplos e amostras de código para a criação e configuração de [Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md). Para utilizar com clusters Azure HDInsight. São apresentados exemplos detalhados de criação de grupos de segurança de rede (NSGs) e de configuração de DNS.

Para obter informações sobre a utilização de redes virtuais com o Azure HDInsight, consulte [Plan a rede virtual para o Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Pré-requisitos para amostras de código e exemplos

Antes de executar qualquer uma das amostras de código neste artigo, tenha uma compreensão da rede TCP/IP. Se não estiver familiarizado com a rede TCP/IP, consulte alguém antes de efazer modificações nas redes de produção.

Outros pré-requisitos para as amostras deste artigo incluem os seguintes itens:

* Se estiver a utilizar o PowerShell, terá de instalar o [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).
* Se quiser utilizar o Azure CLI e ainda não o tiver instalado, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Se estiver à procura de orientação passo a passo na ligação do HDInsight à sua rede no local utilizando uma Rede Virtual Azure, consulte o [Connect HDInsight para o seu](connect-on-premises-network.md) documento de rede no local.

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Exemplo: grupos de segurança de rede com HDInsight

Os exemplos desta secção demonstram como criar regras de grupos de segurança de rede. As regras permitem ao HDInsight comunicar com os serviços de gestão azure. Antes de utilizar os exemplos, ajuste os endereços IP para combinar com os da região azure que está a utilizar. Pode encontrar estas informações nos endereços IP de [gestão hDInsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Modelo de Gestão de Recursos Azure

O seguinte modelo de Gestão de Recursos cria uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego a partir dos endereços IP exigidos pelo HDInsight. Este modelo também cria um cluster HDInsight na rede virtual.

* [Implementar uma rede virtual Azure segura e um cluster Hadoop HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o seguinte script PowerShell para criar uma rede virtual que restringe o tráfego de entrada e permite o tráfego a partir dos endereços IP para a região do Norte da Europa.

> [!IMPORTANT]  
> Altere os endereços IP para `hdirule1` e `hdirule2` neste exemplo para corresponder à região azure que está a utilizar. Pode encontrar esta informação endereços IP de [gestão HDInsight](hdinsight-management-ip-addresses.md).

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Este exemplo demonstra como adicionar regras para permitir o tráfego de entrada nos endereços IP necessários. Não contém uma regra para restringir o acesso de outras fontes. O seguinte código demonstra como permitir o acesso sSH a partir da Internet:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>CLI do Azure

Utilize os seguintes passos para criar uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego a partir dos endereços IP exigidos pelo HDInsight.

1. Utilize o seguinte comando para criar `hdisecure`um novo grupo de segurança de rede chamado . Substitua-o `RESOURCEGROUP` pelo grupo de recursos que contém a Rede Virtual Azure. Substitua-a `LOCATION` pela localização (região) em que o grupo foi criado.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Uma vez criado o grupo, recebe informações sobre o novo grupo.

2. Utilize o seguinte para adicionar regras ao novo grupo de segurança da rede que permita a comunicação de entrada na porta 443 do serviço de saúde e gestão Azure HDInsight. Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a Rede Virtual Azure.

    > [!IMPORTANT]  
    > Altere os endereços IP para `hdirule1` e `hdirule2` neste exemplo para corresponder à região azure que está a utilizar. Pode encontrar estas informações nos endereços IP de [gestão hDInsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Para recuperar o identificador único para este grupo de segurança da rede, utilize o seguinte comando:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Utilize o seguinte comando para aplicar o grupo de segurança da rede numa sub-rede. Substitua `GUID` `RESOURCEGROUP` os valores e valores com os devolvidos do passo anterior. `VNETNAME` Substitua `SUBNETNAME` e com o nome de rede virtual e o nome da sub-rede que pretende criar.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Assim que este comando estiver concluído, pode instalar o HDInsight na Rede Virtual.

Estes passos apenas abrem o acesso ao serviço de saúde e gestão HDInsight na nuvem Azure. Qualquer outro acesso ao cluster HDInsight de fora da Rede Virtual está bloqueado. Para permitir o acesso de fora da rede virtual, deve adicionar regras adicionais do Grupo de Segurança da Rede.

O seguinte código demonstra como permitir o acesso sSH a partir da Internet:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a>Exemplo: Configuração DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Resolução de nomes entre uma rede virtual e uma rede de instalações conectada

Este exemplo faz os seguintes pressupostos:

* Tem uma Rede Virtual Azure que está ligada a uma rede no local utilizando um gateway VPN.

* O servidor DNS personalizado na rede virtual está a executar o Linux ou o Unix como o sistema operativo.

* [O Bind](https://www.isc.org/downloads/bind/) está instalado no servidor DNS personalizado.

No servidor DNS personalizado na rede virtual:

1. Utilize o Azure PowerShell ou o Azure CLI para encontrar o sufixo DNS da rede virtual:

    Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. No servidor DNS personalizado para a rede virtual, utilize `/etc/bind/named.conf.local` o seguinte texto como conteúdo do ficheiro:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Substitua `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` o valor pelo sufixo DNS da sua rede virtual.

    Esta configuração encaminha todos os pedidos dNS para o sufixo DNS da rede virtual para o resolver recursivo Azure.

1. No servidor DNS personalizado para a rede virtual, utilize `/etc/bind/named.conf.options` o seguinte texto como conteúdo do ficheiro:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Substitua `10.0.0.0/16` o valor pela gama de endereços IP da sua rede virtual. Esta entrada permite endereços de pedidos de resolução de nomes dentro desta gama.

    * Adicione a gama de endereços IP da `acl goodclients { ... }` rede no local à secção.  a entrada permite pedidos de resolução de nomes de recursos na rede no local.
    
    * Substitua `192.168.0.1` o valor pelo endereço IP do seu servidor DNS no local. Esta entrada encaminha todos os outros pedidos de DNS para o servidor DNS no local.

1. Para utilizar a configuração, reinicie o Bind. Por exemplo, `sudo service bind9 restart`.

1. Adicione um forwardr condicional ao servidor DNS no local. Configure o reencaminhador condicional para enviar pedidos para o sufixo DNS do passo 1 para o servidor DNS personalizado.

    > [!NOTE]  
    > Consulte a documentação do seu software DNS para obter especificações sobre como adicionar um avançado condicional.

Depois de completar estes passos, pode ligar-se aos recursos em qualquer uma das redes utilizando nomes de domínio totalmente qualificados (FQDN). Agora pode instalar o HDInsight na rede virtual.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Resolução de nomes entre duas redes virtuais conectadas

Este exemplo faz os seguintes pressupostos:

* Tem duas Redes Virtuais Azure que estão ligadas usando um gateway VPN ou o lpeering.

* O servidor DNS personalizado em ambas as redes está a executar o Linux ou o Unix como o sistema operativo.

* [O Bind](https://www.isc.org/downloads/bind/) está instalado nos servidores DNS personalizados.

1. Utilize o Azure PowerShell ou o Azure CLI para encontrar o sufixo DNS de ambas as redes virtuais:

    Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Utilize o seguinte texto como `/etc/bind/named.config.local` o conteúdo do ficheiro no servidor DNS personalizado. Faça esta alteração no servidor DNS personalizado em ambas as redes virtuais.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Substitua `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` o valor pelo sufixo DNS da __outra__ rede virtual. Esta entrada solicita o sufixo DNS da rede remota ao DNS personalizado nessa rede.

3. Nos servidores DNS personalizados em ambas as redes virtuais, `/etc/bind/named.conf.options` utilize o seguinte texto como conteúdo do ficheiro:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Substitua `10.0.0.0/16` `10.1.0.0/16` os valores e valores pelas gamas de endereços IP das suas redes virtuais. Esta entrada permite que os recursos em cada rede façam pedidos dos servidores DNS.

    Quaisquer pedidos que não sejam para os sufixos DNS das redes virtuais (por exemplo, microsoft.com) são tratados pelo resolver recursivo azure.

4. Para utilizar a configuração, reinicie o Bind. Por exemplo, `sudo service bind9 restart` em ambos os servidores DNS.

Depois de completar estes passos, pode ligar-se aos recursos da rede virtual utilizando nomes de domínio totalmente qualificados (FQDN). Agora pode instalar o HDInsight na rede virtual.

## <a name="next-steps"></a>Passos seguintes

* Para um exemplo completo de configurar o HDInsight para se ligar a uma rede no local, consulte [o Connect HDInsight para uma rede no local](./connect-on-premises-network.md).
* Para configurar clusters Apache HBase em redes virtuais Azure, consulte [Create Apache HBase clusters no HDInsight em Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Para configurar a geo-replicação apache HBase, consulte [Configurar a replicação do cluster Apache HBase nas redes virtuais Azure](hbase/apache-hbase-replication.md).
* Para obter mais informações sobre as redes virtuais do Azure, consulte a [visão geral da Rede Virtual Azure.](../virtual-network/virtual-networks-overview.md)

* Para obter mais informações sobre grupos de segurança da rede, consulte [grupos](../virtual-network/security-overview.md)de segurança da rede .

* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte as rotas definidas pelo utilizador e o [reencaminhamento ip](../virtual-network/virtual-networks-udr-overview.md).

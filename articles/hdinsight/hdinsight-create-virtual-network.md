---
title: Criar redes virtuais para clusters Azure HDInsight
description: Saiba como criar uma Rede Virtual Azure para ligar o HDInsight a outros recursos em nuvem, ou recursos no seu datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: d4fcf7830250e5e6d2e4255ddb88867023606d0e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547559"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Criar redes virtuais para clusters Azure HDInsight

Este artigo fornece exemplos e amostras de código para criar e configurar [redes virtuais Azure.](../virtual-network/virtual-networks-overview.md) Para utilizar com clusters Azure HDInsight. São apresentados exemplos detalhados da criação de grupos de segurança de rede (NSGs) e de configuração de DNS.

Para obter informações sobre a utilização de redes virtuais com Azure HDInsight, consulte [Plan a virtual network for Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Pré-requisitos para amostras de código e exemplos

Antes de executar qualquer uma das amostras de código deste artigo, tenha uma compreensão da rede TCP/IP. Se não estiver familiarizado com a rede TCP/IP, consulte alguém antes de escamar as redes de produção.

Outros pré-requisitos para as amostras do presente artigo incluem os seguintes itens:

* Se estiver a utilizar o PowerShell, terá de instalar o [Módulo AZ](/powershell/azure/).
* Se pretender utilizar o Azure CLI e ainda não o tiver instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Se estiver à procura de orientação passo a passo sobre a ligação do HDInsight à sua rede no local utilizando uma Rede Virtual Azure, consulte o [Connect HDInsight ao documento de rede no local.](connect-on-premises-network.md)

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Exemplo: grupos de segurança de rede com HDInsight

Os exemplos nesta secção demonstram como criar regras de grupos de segurança de rede. As regras permitem que a HDInsight comunique com os serviços de gestão da Azure. Antes de utilizar os exemplos, ajuste os endereços IP para combinar com os da região Azure que está a usar. Pode encontrar esta informação nos [endereços IP de gestão hdInsight.](hdinsight-management-ip-addresses.md)

### <a name="azure-resource-management-template"></a>Modelo de gestão de recursos Azure

O modelo de Gestão de Recursos a seguir cria uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego a partir dos endereços IP exigidos por HDInsight. Este modelo também cria um cluster HDInsight na rede virtual.

* [Implementar uma Rede Virtual Azure segura e um cluster HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Use o seguinte script PowerShell para criar uma rede virtual que restringe o tráfego de entrada e permite o tráfego a partir dos endereços IP para a região do Norte da Europa.

> [!IMPORTANT]  
> Altere os endereços IP para `hdirule1` e neste exemplo para corresponder à `hdirule2` região Azure que está a usar. Pode encontrar esta informação [endereços IP de gestão hdInsight](hdinsight-management-ip-addresses.md).

```azurepowershell
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

Este exemplo demonstra como adicionar regras para permitir o tráfego de entrada nos endereços IP necessários. Não contém uma regra para restringir o acesso de outras fontes. O seguinte código demonstra como permitir o acesso ao SSH a partir da Internet:

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>CLI do Azure

Utilize os seguintes passos para criar uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego a partir dos endereços IP exigidos por HDInsight.

1. Utilize o seguinte comando para criar um novo grupo de segurança de rede chamado `hdisecure` . `RESOURCEGROUP`Substitua-o pelo grupo de recursos que contém a Rede Virtual Azure. `LOCATION`Substitua-se pela localização (região) em que o grupo foi criado.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Uma vez criado o grupo, recebe informações sobre o novo grupo.

2. Utilize o seguinte para adicionar regras ao novo grupo de segurança da rede que permitem a comunicação de entrada no porto 443 do serviço de saúde e gestão Azure HDInsight. `RESOURCEGROUP`Substitua-o pelo nome do grupo de recursos que contém a Rede Virtual Azure.

    > [!IMPORTANT]  
    > Altere os endereços IP para `hdirule1` e neste exemplo para corresponder à `hdirule2` região Azure que está a usar. Pode encontrar esta informação nos [endereços IP de gestão hdInsight.](hdinsight-management-ip-addresses.md)

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Para recuperar o identificador único para este grupo de segurança de rede, utilize o seguinte comando:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. Utilize o seguinte comando para aplicar o grupo de segurança da rede numa sub-rede. Substitua os `GUID` `RESOURCEGROUP` valores e valores pelos devolvidos do escalão anterior. Substitua `VNETNAME` e pelo nome de rede virtual e nome da `SUBNETNAME` sub-rede que pretende criar.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Uma vez concluído este comando, pode instalar o HDInsight na Rede Virtual.

Estes passos só abrem acesso ao serviço de saúde e gestão HDInsight na nuvem Azure. Qualquer outro acesso ao cluster HDInsight de fora da Rede Virtual está bloqueado. Para permitir o acesso a partir de fora da rede virtual, deve adicionar regras adicionais do Grupo de Segurança de Rede.

O seguinte código demonstra como permitir o acesso ao SSH a partir da Internet:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Exemplo: Configuração DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Resolução de nomes entre uma rede virtual e uma rede de instalações conectadas

Este exemplo faz os seguintes pressupostos:

* Tem uma Rede Virtual Azure que está ligada a uma rede no local utilizando um gateway VPN.

* O servidor DNS personalizado da rede virtual está a executar o Linux ou o Unix como o sistema operativo.

* [O Bind](https://www.isc.org/downloads/bind/) está instalado no servidor DNS personalizado.

No servidor DNS personalizado da rede virtual:

1. Utilize o Azure PowerShell ou o Azure CLI para encontrar o sufixo DNS da rede virtual:

    `RESOURCEGROUP`Substitua-o pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. No servidor DNS personalizado para a rede virtual, utilize o seguinte texto como o conteúdo do `/etc/bind/named.conf.local` ficheiro:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Substitua o `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor pelo sufixo DNS da sua rede virtual.

    Esta configuração encaminha todos os pedidos DNS para o sufixo DNS da rede virtual para o Azure recursive resolver.

1. No servidor DNS personalizado para a rede virtual, utilize o seguinte texto como o conteúdo do `/etc/bind/named.conf.options` ficheiro:

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
    
    * Substitua o `10.0.0.0/16` valor pelo intervalo de endereços IP da sua rede virtual. Esta entrada permite pedidos de resolução de nomes dentro deste intervalo.

    * Adicione o intervalo de endereços IP da rede no local à `acl goodclients { ... }` secção.  a entrada permite pedidos de resolução de nomes a partir de recursos na rede de instalações.
    
    * Substitua o valor `192.168.0.1` pelo endereço IP do servidor DNS no local. Esta entrada encaminha todos os outros pedidos DNS para o servidor DNS no local.

1. Para utilizar a configuração, reinicie o Encaixe. Por exemplo, `sudo service bind9 restart`.

1. Adicione um reencaminhador condicional ao servidor DNS no local. Configure o remetente condicional para enviar pedidos para o sufixo DNS do passo 1 para o servidor DNS personalizado.

    > [!NOTE]  
    > Consulte a documentação do seu software DNS para obter detalhes sobre como adicionar um reencaminhador condicional.

Após completar estes passos, pode ligar-se a recursos em qualquer rede utilizando nomes de domínio totalmente qualificados (FQDN). Agora pode instalar o HDInsight na rede virtual.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Resolução de nomes entre duas redes virtuais conectadas

Este exemplo faz os seguintes pressupostos:

* Tem duas Redes Virtuais Azure que estão ligadas usando uma porta de entrada VPN ou espreitando.

* O servidor DNS personalizado em ambas as redes está a executar o Linux ou o Unix como o sistema operativo.

* [O Bind](https://www.isc.org/downloads/bind/) está instalado nos servidores DNS personalizados.

1. Utilize o Azure PowerShell ou o Azure CLI para encontrar o sufixo DNS de ambas as redes virtuais:

    `RESOURCEGROUP`Substitua-o pelo nome do grupo de recursos que contém a rede virtual e, em seguida, introduza o comando:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Utilize o texto que se segue como o conteúdo do `/etc/bind/named.config.local` ficheiro no servidor DNS personalizado. Faça esta alteração no servidor DNS personalizado em ambas as redes virtuais.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Substitua o `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor pelo sufixo DNS da __outra__ rede virtual. Esta entrada de percursos solicita o sufixo DNS da rede remota para o DNS personalizado naquela rede.

3. Nos servidores DNS personalizados em ambas as redes virtuais, utilize o seguinte texto como o conteúdo do `/etc/bind/named.conf.options` ficheiro:

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

   Substitua os `10.0.0.0/16` valores e `10.1.0.0/16` valores pelos intervalos de endereços IP das suas redes virtuais. Esta entrada permite que os recursos em cada rede façam pedidos dos servidores DNS.

    Quaisquer pedidos que não sejam para os sufixos DNS das redes virtuais (por exemplo, microsoft.com) são tratados pela azure recursive resolver.

4. Para utilizar a configuração, reinicie o Encaixe. Por exemplo, `sudo service bind9 restart` em ambos os servidores DNS.

Após completar estes passos, pode ligar-se a recursos na rede virtual utilizando nomes de domínio totalmente qualificados (FQDN). Agora pode instalar o HDInsight na rede virtual.

## <a name="next-steps"></a>Passos seguintes

* Para obter um exemplo completo de configurar o HDInsight para ligar a uma rede no local, consulte [Connect HDInsight a uma rede no local](./connect-on-premises-network.md).
* Para configurar os clusters Apache HBase em redes virtuais Azure, consulte [criar clusters Apache HBase em HDInsight na Rede Virtual Azure](hbase/apache-hbase-provision-vnet.md).
* Para configurar a geo-replicação apache HBase, consulte [configurar a replicação do cluster Apache HBase em redes virtuais Azure](hbase/apache-hbase-replication.md).
* Para obter mais informações sobre as redes virtuais Azure, consulte a [visão geral da Rede Virtual Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre grupos de segurança de rede, consulte [os grupos de segurança da rede](../virtual-network/network-security-groups-overview.md).

* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [as rotas definidas pelo Utilizador e o encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).
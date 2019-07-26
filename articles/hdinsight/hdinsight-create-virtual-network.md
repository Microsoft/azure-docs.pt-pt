---
title: Criar redes virtuais para clusters do Azure HDInsight
description: Saiba como criar uma rede virtual do Azure para conectar o HDInsight a outros recursos de nuvem ou recursos em seu datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 2fd4c20e5c1cd0a8e1ee6f7c36d4b4a8b99d37ea
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442008"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Criar redes virtuais para clusters do Azure HDInsight

Este artigo fornece exemplos e exemplos de código para criar e configurar [redes virtuais do Azure](../virtual-network/virtual-networks-overview.md) para uso com clusters do Azure HDInsight. São apresentados exemplos detalhados de como criar NSGs (grupos de segurança de rede) e configurar o DNS. 

Para obter informações básicas sobre como usar redes virtuais com o Azure HDInsight, consulte [planejar uma rede virtual para o Azure hdinsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Pré-requisitos para amostras de código e exemplos

Antes de executar qualquer um dos exemplos de código neste artigo, a UO deve ter uma compreensão da rede TCP/IP. Se você não estiver familiarizado com a rede TCP/IP, consulte alguém que está antes de fazer modificações nas redes de produção.

Outros pré-requisitos para os exemplos neste artigo incluem o seguinte:

* Se você estiver usando o PowerShell, será necessário instalar o [módulo AZ](https://docs.microsoft.com/powershell/azure/overview).
* Se você quiser usar CLI do Azure e ainda não o tiver instalado, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Se você estiver procurando orientação passo a passo sobre como conectar o HDInsight à sua rede local usando uma rede virtual do Azure, consulte o documento [conectar o hdinsight ao seu local de rede](connect-on-premises-network.md) .

## <a id="hdinsight-nsg"></a>Exemplo: grupos de segurança de rede com o HDInsight

Os exemplos nesta seção demonstram como criar regras de grupo de segurança de rede que permitem que o HDInsight se comunique com os serviços de gerenciamento do Azure. Antes de usar os exemplos, ajuste os endereços IP para que correspondam àqueles da região do Azure que você está usando. Você pode encontrar essas informações em [endereços IP de gerenciamento do HDInsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Modelo de gerenciamento de recursos do Azure

O modelo de gerenciamento de recursos a seguir cria uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego dos endereços IP exigidos pelo HDInsight. Esse modelo também cria um cluster HDInsight na rede virtual.

* [Implantar uma rede virtual do Azure protegida e um cluster Hadoop do HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Use o seguinte script do PowerShell para criar uma rede virtual que restringe o tráfego de entrada e permite o tráfego dos endereços IP para a região de Europa Setentrional.

> [!IMPORTANT]  
> Altere os endereços IP para `hdirule1` e `hdirule2` , neste exemplo, para corresponder à região do Azure que você está usando. Você pode encontrar essas informações [endereços IP de gerenciamento do HDInsight](hdinsight-management-ip-addresses.md).

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

Este exemplo demonstra como adicionar regras para permitir o tráfego de entrada nos endereços IP necessários. Ele não contém uma regra para restringir o acesso de entrada de outras fontes. O código a seguir demonstra como habilitar o acesso SSH da Internet:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>CLI do Azure

Use as etapas a seguir para criar uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego dos endereços IP exigidos pelo HDInsight.

1. Use o comando a seguir para criar um novo grupo de segurança `hdisecure`de rede chamado. Substitua `RESOURCEGROUP` pelo grupo de recursos que contém a rede virtual do Azure. Substituir `LOCATION` pelo local (região) em que o grupo foi criado.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Depois que o grupo tiver sido criado, você receberá informações sobre o novo grupo.

2. Use o seguinte para adicionar regras ao novo grupo de segurança de rede que permitem a comunicação de entrada na porta 443 do serviço de integridade e gerenciamento do Azure HDInsight. Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual do Azure.

    > [!IMPORTANT]  
    > Altere os endereços IP para `hdirule1` e `hdirule2` , neste exemplo, para corresponder à região do Azure que você está usando. Você pode encontrar essas informações em [endereços IP de gerenciamento do HDInsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Para recuperar o identificador exclusivo para esse grupo de segurança de rede, use o seguinte comando:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Esse comando retorna um valor semelhante ao seguinte texto:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Use o comando a seguir para aplicar o grupo de segurança de rede a uma sub-rede. Substitua os `GUID` valores `RESOURCEGROUP` e por aqueles retornados da etapa anterior. Substitua `VNETNAME` e`SUBNETNAME` pelo nome da rede virtual e nome da sub-rede que você deseja criar.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Depois que esse comando for concluído, você poderá instalar o HDInsight na rede virtual.


Essas etapas abrem apenas o acesso ao serviço de integridade e gerenciamento do HDInsight na nuvem do Azure. Qualquer outro acesso ao cluster HDInsight de fora da rede virtual é bloqueado. Para habilitar o acesso de fora da rede virtual, você deve adicionar mais regras de grupo de segurança de rede.

O código a seguir demonstra como habilitar o acesso SSH da Internet:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a id="example-dns"></a>Exemplo Configuração do DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Resolução de nomes entre uma rede virtual e uma rede local conectada

Este exemplo faz as seguintes suposições:

* Você tem uma rede virtual do Azure que está conectada a uma rede local usando um gateway de VPN.

* O servidor DNS personalizado na rede virtual está executando Linux ou UNIX como o sistema operacional.

* O [BIND](https://www.isc.org/downloads/bind/) é instalado no servidor DNS personalizado.

No servidor DNS personalizado na rede virtual:

1. Use Azure PowerShell ou CLI do Azure para localizar o sufixo DNS da rede virtual:

    Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. No servidor DNS personalizado para a rede virtual, use o seguinte texto como o conteúdo do `/etc/bind/named.conf.local` arquivo:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Substitua o `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor pelo sufixo DNS da sua rede virtual.

    Essa configuração roteia todas as solicitações DNS para o sufixo DNS da rede virtual para o resolvedor recursivo do Azure.

2. No servidor DNS personalizado para a rede virtual, use o seguinte texto como o conteúdo do `/etc/bind/named.conf.options` arquivo:

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
    
    * Substitua o `10.0.0.0/16` valor pelo intervalo de endereços IP da sua rede virtual. Essa entrada permite endereços de solicitações de resolução de nomes dentro desse intervalo.

    * Adicione o intervalo de endereços IP da rede local à `acl goodclients { ... }` seção.  a entrada permite solicitações de resolução de nomes de recursos na rede local.
    
    * Substitua o valor `192.168.0.1` pelo endereço IP do seu servidor DNS local. Essa entrada roteia todas as outras solicitações DNS para o servidor DNS local.

3. Para usar a configuração, reinicie a associação. Por exemplo, `sudo service bind9 restart`.

4. Adicione um encaminhador condicional ao servidor DNS local. Configure o encaminhador condicional para enviar solicitações para o sufixo DNS da etapa 1 para o servidor DNS personalizado.

    > [!NOTE]  
    > Consulte a documentação do seu software DNS para obter informações específicas sobre como adicionar um encaminhador condicional.

Depois de concluir essas etapas, você pode se conectar a recursos em qualquer rede usando nomes de domínio totalmente qualificados (FQDN). Agora você pode instalar o HDInsight na rede virtual.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Resolução de nomes entre duas redes virtuais conectadas

Este exemplo faz as seguintes suposições:

* Você tem duas redes virtuais do Azure conectadas usando um gateway de VPN ou um emparelhamento.

* O servidor DNS personalizado em ambas as redes está executando Linux ou UNIX como o sistema operacional.

* O [BIND](https://www.isc.org/downloads/bind/) é instalado nos servidores DNS personalizados.

1. Use Azure PowerShell ou CLI do Azure para localizar o sufixo DNS de ambas as redes virtuais:

    Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Use o texto a seguir como o conteúdo do `/etc/bind/named.config.local` arquivo no servidor DNS personalizado. Faça essa alteração no servidor DNS personalizado em ambas as redes virtuais.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Substitua o `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor pelo sufixo DNS da __outra__ rede virtual. Essa entrada roteia solicitações para o sufixo DNS da rede remota para o DNS personalizado nessa rede.

3. Nos servidores DNS personalizados em ambas as redes virtuais, use o seguinte texto como o conteúdo do `/etc/bind/named.conf.options` arquivo:

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
    
   Substitua os `10.0.0.0/16` valores `10.1.0.0/16` e pelos intervalos de endereços IP de suas redes virtuais. Essa entrada permite que os recursos em cada rede façam solicitações dos servidores DNS.

    Qualquer solicitação que não seja para os sufixos DNS das redes virtuais (por exemplo, microsoft.com) é tratada pelo resolvedor recursivo do Azure.

4. Para usar a configuração, reinicie a associação. Por exemplo, `sudo service bind9 restart` em ambos os servidores DNS.

Depois de concluir essas etapas, você pode se conectar aos recursos na rede virtual usando nomes de domínio totalmente qualificados (FQDN). Agora você pode instalar o HDInsight na rede virtual.

## <a name="next-steps"></a>Passos Seguintes

* Para obter um exemplo de ponta a ponta de como configurar o HDInsight para se conectar a uma rede local, consulte [conectar o hdinsight a uma rede local](./connect-on-premises-network.md).
* Para configurar clusters do Apache HBase em redes virtuais do Azure, confira [Criar clusters do Apache HBase no HDInsight na rede virtual do Azure](hbase/apache-hbase-provision-vnet.md).
* Para configurar a replicação geográfica do Apache HBase, confira [Configurar a replicação de cluster do Apache HBase em redes virtuais do Azure](hbase/apache-hbase-replication.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte [visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](../virtual-network/security-overview.md).

* Para obter mais informações sobre rotas definidas pelo usuário, consulte [rotas definidas pelo usuário e encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md).

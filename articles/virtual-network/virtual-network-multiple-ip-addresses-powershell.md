---
title: Vários endereços IP para máquinas virtuais do Azure-PowerShell | Microsoft Docs
description: Saiba como atribuir vários endereços IP a uma máquina virtual usando o PowerShell. | Gerenciador de recursos
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: e9bad6ad614855c543ee6d75d4e6f4dc8e2255aa
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876230"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Atribuir vários endereços IP a máquinas virtuais usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma VM (máquina virtual) por meio do modelo de implantação Azure Resource Manager usando o PowerShell. Vários endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implantação do Azure, leia o artigo [entender os modelos de implantação](../resource-manager-deployment-model.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma VM de exemplo com vários endereços IP, conforme descrito no cenário. Altere os valores de variáveis conforme necessário para sua implementação.

1. Abra um prompt de comando do PowerShell e conclua as etapas restantes nesta seção em uma única sessão do PowerShell. Se você ainda não tiver o PowerShell instalado e configurado, conclua as etapas no artigo [como instalar e configurar Azure PowerShell](/powershell/azure/overview) .
2. Faça logon em sua conta com `Connect-AzAccount` o comando.
3. Substitua *MyResource* e *westus* por um nome e um local de sua escolha. Crie um grupo de recursos. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Crie uma rede virtual (VNet) e uma sub-rede no mesmo local que o grupo de recursos:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Crie um NSG (grupo de segurança de rede) e uma regra. O NSG protege a VM usando regras de entrada e saída. Neste caso, é criada uma regra de entrada para a porta 3389, que permite ligações de ambiente de trabalho remotas recebidas.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Defina a configuração de IP primário para a NIC. Altere 10.0.0.4 para um endereço válido na sub-rede que você criou, se você não usou o valor definido anteriormente. Antes de atribuir um endereço IP estático, é recomendável primeiro confirmar que ele ainda não está em uso. Insira o comando `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Se o endereço estiver disponível, a saída retornará *true*. Se não estiver disponível, a saída retornará *false* e uma lista de endereços que estão disponíveis. 

    Nos comandos a seguir, **substitua \<o nome exclusivo do Replace-com-seu > pelo nome DNS exclusivo a ser usado.** O nome deve ser exclusivo em todos os endereços IP públicos em uma região do Azure. Esse é um parâmetro opcional. Ele poderá ser removido se você quiser se conectar apenas à VM usando o endereço IP público.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Quando você atribui várias configurações de IP a uma NIC, uma configuração deve ser atribuída como a *-primária*.

    > [!NOTE]
    > Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre preços de endereço IP, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

7. Defina as configurações de IP secundário para a NIC. Você pode adicionar ou remover configurações conforme necessário. Cada configuração de IP deve ter um endereço IP privado atribuído. Cada configuração pode, opcionalmente, ter um endereço IP público atribuído.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Crie a NIC e associe as três configurações de IP a ela:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Embora todas as configurações sejam atribuídas a uma NIC neste artigo, você pode atribuir várias configurações de IP a cada NIC anexada à VM. Para saber como criar uma VM com várias NICs, leia o artigo [criar uma VM com várias NICs](../virtual-machines/windows/multiple-nics.md) .

9. Crie a VM inserindo os seguintes comandos:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Adicione os endereços IP privados ao sistema operacional da VM concluindo as etapas para seu sistema operacional na seção [adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados ao adaptador de rede do Azure concluindo as etapas a seguir. Os exemplos nas seções a seguir pressupõem que você já tem uma VM com as três configurações de IP descritas no [cenário](#scenario) neste artigo, mas isso não é necessário.

1. Abra um prompt de comando do PowerShell e conclua as etapas restantes nesta seção em uma única sessão do PowerShell. Se você ainda não tiver o PowerShell instalado e configurado, conclua as etapas no artigo [como instalar e configurar Azure PowerShell](/powershell/azure/overview) .
2. Altere os "valores" do $Variables a seguir para o nome da NIC à qual você deseja adicionar o endereço IP e o grupo de recursos e o local em que a NIC existe:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Se você não souber o nome da NIC que deseja alterar, insira os seguintes comandos e, em seguida, altere os valores das variáveis anteriores:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Crie uma variável e defina-a como a NIC existente digitando o seguinte comando:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Nos comandos a seguir, altere *MyVNet* e *mysubnet* para os nomes da VNet e da sub-rede à qual a NIC está conectada. Insira os comandos para recuperar os objetos VNet e de sub-rede aos quais a NIC está conectada:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Se você não souber o nome da VNet ou da sub-rede à qual a NIC está conectada, digite o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Na saída, procure um texto semelhante à seguinte saída de exemplo:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Nessa saída, *MyVnet* é a VNet e mysubnet é a sub-rede à qual a NIC está conectada.

5. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

   **Adicionar um endereço IP privado**

   Para adicionar um endereço IP privado a uma NIC, você deve criar uma configuração de IP. O comando a seguir cria uma configuração com um endereço IP estático de 10.0.0.7. Ao especificar um endereço IP estático, ele deve ser um endereço não utilizado para a sub-rede. É recomendável que você primeiro teste o endereço para garantir que ele esteja disponível digitando `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` o comando. Se o endereço IP estiver disponível, a saída retornará *true*. Se não estiver disponível, a saída retornará *false*e uma lista de endereços que estão disponíveis.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

   Adicione o endereço IP privado ao sistema operacional da VM concluindo as etapas para seu sistema operacional na seção [adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo.

   **Adicionar um endereço IP público**

   Um endereço IP público é adicionado pela Associação de um recurso de endereço IP público a uma nova configuração de IP ou uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, conforme necessário.

   > [!NOTE]
   > Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre preços de endereço IP, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
   >

   **Associar o recurso de endereço IP público a uma nova configuração de IP**

   Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Para criar uma nova configuração de IP com um endereço IP privado estático e o recurso de endereço IP público *myPublicIp3* associado, digite o seguinte comando:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associar o recurso de endereço IP público a uma configuração de IP existente**

   Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado digitando o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Você verá uma saída semelhante à seguinte:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Como a coluna **PublicIpAddress** para *ipconfig-3* está em branco, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente ao IpConfig-3 ou inserir o seguinte comando para criar um:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Digite o seguinte comando para associar o recurso de endereço IP público à configuração de IP existente chamada *ipconfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Defina a NIC com a nova configuração de IP digitando o seguinte comando:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Exiba os endereços IP privados e os recursos de endereço IP público atribuídos à NIC digitando o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Adicione o endereço IP privado ao sistema operacional da VM concluindo as etapas para seu sistema operacional na seção [adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo. Não adicione o endereço IP público ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
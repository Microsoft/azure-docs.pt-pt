---
title: Vários endereços IP para máquinas virtuais do Azure - PowerShell | Documentos da Microsoft
description: Saiba como atribuir vários endereços IP a uma máquina virtual com o PowerShell. | Resource Manager
services: virtual-network
documentationcenter: na
author: jimdial
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
ms.author: jdial;annahar
ms.openlocfilehash: f9ce9bc9272eb76c4162ab0abedd93e959385124
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649055"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Atribuir vários endereços IP para máquinas virtuais com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) por meio do modelo de implementação Azure Resource Manager com o PowerShell. Não não possível atribuir vários endereços IP para os recursos criados por meio do modelo de implementação clássica. Para saber mais sobre os modelos de implementação do Azure, leia os [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Os passos que se seguem explicam como criar um VM de exemplo com vários endereços IP, conforme descrito no cenário. Alterar valores das variáveis conforme necessário para a sua implementação.

1. Abra uma linha de comando do PowerShell e conclua os restantes passos nesta secção numa única sessão do PowerShell. Se ainda não tiver PowerShell instalado e configurado, conclua os passos na [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) artigo.
2. Inicie sessão na sua conta com o `Connect-AzAccount` comando.
3. Substitua *myResourceGroup* e *westus* com um nome e o local de sua preferência. Crie um grupo de recursos. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Crie uma rede virtual (VNet) e uma sub-rede na mesma localização que o grupo de recursos:

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

5. Crie um grupo de segurança de rede (NSG) e uma regra. O NSG protege a VM com regras de entrada e saídas. Neste caso, é criada uma regra de entrada para a porta 3389, que permite ligações de ambiente de trabalho remotas recebidas.

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

6. Definir a configuração de IP primária para a NIC. Alterar 10.0.0.4 para um endereço válido na sub-rede que criou, se não utilizou o valor definido anteriormente. Antes de atribuir um endereço IP estático, recomenda-se que confirme primeiro que ainda não estiver em utilização. Introduza o comando `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Se o endereço está disponível, a saída devolve *True*. Se não estiver disponível, a saída devolve *False* e uma lista de endereços que estão disponíveis. 

    Nos seguintes comandos, **substituir < replace-com o-seu--nome exclusivo > com o nome DNS exclusivo a utilizar.** O nome tem de ser exclusivo em todos os endereços IP públicos dentro de uma região do Azure. Este é um parâmetro opcional. Este pode ser removido se pretender ligar à VM com o endereço IP público.

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

    Ao atribuir várias configurações de IP a uma NIC, uma configuração tem de ser atribuída como o *-primário*.

    > [!NOTE]
    > Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereços IP, leia os [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Existe um limite ao número de endereços IP públicos, que pode ser utilizado numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

7. Definir as configurações de IP secundárias para o NIC. Pode adicionar ou remover as configurações conforme necessário. Cada configuração de IP tem de ter um endereço IP privado atribuído. Cada configuração, opcionalmente, pode ter um endereço IP público atribuído.

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

8. Crie o NIC e associar as três configurações de IP a ele:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Embora todas as configurações são atribuídas a uma NIC neste artigo, pode atribuir várias configurações de IP a cada NIC anexado à VM. Para saber como criar uma VM com várias NICs, leia os [criar uma VM com várias NICs](../virtual-machines/windows/multiple-nics.md) artigo.

9. Crie a VM ao introduzir os seguintes comandos:

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

10. Adicionar os endereços IP privados para o sistema operacional VM, concluindo os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) seção deste artigo. Não adicione os endereços IP públicos para o sistema operativo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Pode adicionar endereços IP públicos e privados para a interface de rede do Azure, concluindo os passos que se seguem. Os exemplos nas secções seguintes partem do princípio de que já tem uma VM com as três configurações de IP descritas a [cenário](#Scenario) neste artigo, mas ele não seja necessário que o faça.

1. Abra uma linha de comando do PowerShell e conclua os restantes passos nesta secção numa única sessão do PowerShell. Se ainda não tiver PowerShell instalado e configurado, conclua os passos na [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) artigo.
2. Altere os "valores" a $Variables seguintes para o nome do NIC que pretende adicionar o endereço IP para e o grupo de recursos e a localização que a NIC existe no:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Se não souber o nome do NIC que pretende alterar, insira os seguintes comandos, em seguida, altere os valores das variáveis anteriores:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Crie uma variável e defini-lo para o NIC existente, escrevendo o seguinte comando:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Nos seguintes comandos, altere *MyVNet* e *MySubnet* para os nomes da VNet e sub-rede a NIC está ligada. Introduza os comandos para recuperar os objetos de VNet e sub-rede que a NIC está ligada a:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Se não souber o nome de VNet ou sub-rede que a NIC está ligada a, introduza o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Na saída, procure por texto semelhante à saída de exemplo seguinte:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Neste dados de saída *MyVnet* é a VNet e *MySubnet* é a NIC está ligada à sub-rede.

5. Conclua os passos de uma das seções a seguir, com base nos seus requisitos:

   **Adicionar um endereço IP privado**

   Para adicionar um endereço IP privado a uma NIC, tem de criar uma configuração de IP. O comando seguinte cria uma configuração com um endereço IP estático de 10.0.0.7. Ao especificar um endereço IP estático, tem de ser um endereço não utilizado para a sub-rede. Recomenda-se que teste primeiro o endereço para garantir que está disponível ao introduzir o `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` comando. Se o endereço IP estiver disponível, a saída devolve *True*. Se não estiver disponível, a saída devolve *False*e uma lista de endereços que estão disponíveis.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Crie configurações de tantos que forem necessárias, com nomes de configuração exclusiva e endereços IP privados (para configurações com endereços IP estáticos).

   Adicionar o endereço IP privado para o sistema operacional VM, concluindo os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) seção deste artigo.

   **Adicionar um endereço IP público**

   Um endereço IP público é adicionado ao associar um recurso de endereço IP público para uma nova configuração de IP ou uma configuração de IP existente. Conclua os passos de uma das seções a seguir, que forem necessárias.

   > [!NOTE]
   > Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereços IP, leia os [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Existe um limite ao número de endereços IP públicos, que pode ser utilizado numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
   >

   **Associar o recurso de endereço IP público para uma nova configuração de IP**

   Sempre que adicionar um endereço IP público numa nova configuração de IP, também tem de adicionar um endereço IP privado, uma vez que todas as configurações de IP tem de ter um endereço IP privado. Pode adicionar um recurso de endereço IP público existente ou crie um novo. Para criar uma nova, introduza o seguinte comando:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Para criar uma nova configuração de IP com um endereço IP privado estático e associada *myPublicIp3* IP público de recurso de endereços, introduza o seguinte comando:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associar o recurso de endereço IP público a uma configuração de IP existente**

   Só pode ser associado a uma configuração de IP que ainda não tiver uma associados um recurso de endereço IP público. Pode determinar se uma configuração de IP tem um endereço IP público associado ao introduzir o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Pode ver o resultado semelhante ao seguinte:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Uma vez que o **PublicIpAddress** coluna para *IpConfig 3* está em branco, nenhum recurso de endereço IP público do está atualmente associado a ele. Pode adicionar um recurso de endereço IP público existente para IpConfig 3, ou introduza o seguinte comando para criar um:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Introduza o seguinte comando para associar o recurso de endereço IP público à configuração de IP existente com o nome *IpConfig 3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Defina a NIC com a nova configuração de IP, introduzindo o seguinte comando:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Ver os endereços IP privados e os recursos de endereço IP públicos atribuídos ao NIC, introduzindo o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Adicionar o endereço IP privado para o sistema operacional VM, concluindo os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) seção deste artigo. Não adicione o endereço IP público para o sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
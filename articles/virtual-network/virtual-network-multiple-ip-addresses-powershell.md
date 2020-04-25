---
title: Múltiplos endereços IP para máquinas virtuais Azure - PowerShell / Microsoft Docs
description: Saiba como atribuir vários endereços IP a uma máquina virtual utilizando o PowerShell. | Gestor de Recursos
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: e4197923fa71c719611bea7603113cab331d4ba8
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147801"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Atribuir vários endereços IP a máquinas virtuais usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação do Gestor de Recursos Azure utilizando o PowerShell. Vários endereços IP não podem ser atribuídos aos recursos criados através do modelo de implementação clássico. Para saber mais sobre os modelos de implementação do Azure, leia o artigo dos [modelos de implementação Da Understand.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

Os passos que se seguem explicam como criar um vM de exemplo com vários endereços IP, como descrito no cenário. Altere os valores variáveis conforme necessário para a sua implementação.

1. Abra um pedido de comando PowerShell e complete os passos restantes nesta secção numa única sessão powerShell. Se ainda não tiver o PowerShell instalado e configurado, complete os passos no artigo Como instalar e configurar o artigo [Azure PowerShell.](/powershell/azure/overview)
2. Faça login na `Connect-AzAccount` sua conta com o comando.
3. Substitua *o myResourceGroup* e *o westus* por um nome e localização à sua escolha. Crie um grupo de recursos. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Criar uma rede virtual (VNet) e uma subnet no mesmo local que o grupo de recursos:

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

5. Criar um grupo de segurança de rede (NSG) e uma regra. O NSG assegura o VM utilizando regras de entrada e saída. Neste caso, é criada uma regra de entrada para a porta 3389, que permite ligações de ambiente de trabalho remotas recebidas.

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

6. Defina a configuração ip primária para o NIC. Altere 10.0.0.4 para um endereço válido na subnet que criou, se não utilizar o valor previamente definido. Antes de atribuir um endereço IP estático, recomenda-se que primeiro confirme que ainda não está a ser utilizado. Introduza `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`o comando . Se o endereço estiver disponível, a saída devolve *True*. Se não estiver disponível, a saída devolve *Falso* e uma lista de endereços disponíveis. 

    Nos seguintes comandos, **substitua \<a substituição por> de nome único com o nome DNS único a utilizar.** O nome deve ser único em todos os endereços IP públicos dentro de uma região de Azure. Este é um parâmetro opcional. Pode ser removido se pretender ligar-se apenas ao VM utilizando o endereço IP público.

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

    Quando atribui várias configurações ip a um NIC, uma configuração deve ser atribuída como *-Primária*.

    > [!NOTE]
    > Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços do endereço IP, leia a página de preços do [endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Existe um limite para o número de endereços IP públicos que podem ser usados numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

7. Defina as configurações ip secundárias para o NIC. Pode adicionar ou remover as configurações conforme necessário. Cada configuração IP deve ter um endereço IP privado atribuído. Cada configuração pode opcionalmente ter um endereço IP público atribuído.

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

8. Crie o NIC e associe-lhe as três configurações IP:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Apesar de todas as configurações serem atribuídas a um NIC neste artigo, pode atribuir várias configurações ip a cada NIC anexado ao VM. Para aprender a criar um VM com vários NICs, leia o [Create a VM com vários artigos nics.](../virtual-machines/windows/multiple-nics.md)

9. Criar o VM inserindo os seguintes comandos:

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

10. Adicione os endereços IP privados ao sistema operativo VM, preenchendo as etapas do seu sistema operativo nos [endereços ADD IP a uma](#os-config) secção do sistema operativo VM deste artigo. Não adicione os endereços IP públicos ao sistema operativo.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Adicione endereços IP a um VM

Pode adicionar endereços IP privados e públicos à interface de rede Azure, completando os passos que se seguem. Os exemplos nas seguintes secções assumem que já tem um VM com as três configurações IP descritas no [cenário](#scenario) neste artigo, mas não é necessário que o faça.

1. Abra um pedido de comando PowerShell e complete os passos restantes nesta secção numa única sessão powerShell. Se ainda não tiver o PowerShell instalado e configurado, complete os passos no artigo Como instalar e configurar o artigo [Azure PowerShell.](/powershell/azure/overview)
2. Altere os "valores" dos seguintes $Variables para o nome do NIC a que pretende adicionar endereço IP e ao grupo de recursos e localização em que o NIC existe:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Se não sabe o nome do NIC que pretende alterar, insira os seguintes comandos e, em seguida, altere os valores das variáveis anteriores:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Crie uma variável e desloque-a para o NIC existente digitando o seguinte comando:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Nos seguintes comandos, altere *myVNet* e *MySubnet* para os nomes da VNet e sub-rede a que o NIC está ligado. Introduza os comandos para recuperar os objetos VNet e sub-rede a que o NIC está ligado:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Se não souber o nome VNet ou subnet a que o NIC está ligado, introduza o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Na saída, procure texto semelhante à seguinte saída de exemplo:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Nesta saída, *myVnet* é o VNet e *MySubnet* é a sub-rede a que o NIC está ligado.

5. Complete os passos numa das seguintes secções, com base nos seus requisitos:

   **Adicione um endereço IP privado**

   Para adicionar um endereço IP privado a um NIC, tem de criar uma configuração IP. O seguinte comando cria uma configuração com um endereço IP estático de 10.0.0.7. Ao especificar um endereço IP estático, deve ser um endereço não utilizado para a sub-rede. Recomenda-se que teste primeiro o endereço para garantir que `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` está disponível entrando no comando. Se o endereço IP estiver disponível, a saída devolve *True*. Se não estiver disponível, a saída devolve *Falso,* e uma lista de endereços disponíveis.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Crie as configurações necessárias, utilizando nomes de configuração únicos e endereços IP privados (para configurações com endereços IP estáticos).

   Adicione o endereço IP privado ao sistema operativo VM preenchendo as etapas do seu sistema operativo nos [endereços IP Add a uma](#os-config) secção do sistema operativo VM deste artigo.

   **Adicione um endereço IP público**

   Um endereço IP público é adicionado associando um recurso de endereço IP público a uma nova configuração IP ou uma configuração IP existente. Complete os passos numa das secções que se seguem, conforme necessário.

   > [!NOTE]
   > Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços do endereço IP, leia a página de preços do [endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Existe um limite para o número de endereços IP públicos que podem ser usados numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
   >

   **Associar o recurso de endereço IP público a uma nova configuração IP**

   Sempre que adicionar um endereço IP público numa nova configuração IP, também deve adicionar um endereço IP privado, porque todas as configurações IP devem ter um endereço IP privado. Pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Para criar uma nova configuração IP com um endereço IP privado estático e o recurso de endereço IP público do *myPublicIp3* associado, insira o seguinte comando:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associar o recurso de endereço IP público a uma configuração IP existente**

   Um recurso de endereço IP público só pode ser associado a uma configuração IP que ainda não tem uma associada. Pode determinar se uma configuração IP tem um endereço IP público associado, introduzindo o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Verá um resultado semelhante ao seguinte:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Uma vez que a coluna **PublicIpAddress** para *IpConfig-3* está em branco, nenhum recurso de endereço IP público está atualmente associado ao mesmo. Pode adicionar um recurso de endereço IP público existente ao IpConfig-3, ou introduzir o seguinte comando para criar um:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Introduza o seguinte comando para associar o recurso de endereço IP público à configuração IP existente chamada *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Detete o NIC com a nova configuração IP, entrando no seguinte comando:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Consulte os endereços IP privados e os recursos de endereço IP públicos atribuídos ao NIC, inserindo o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Adicione o endereço IP privado ao sistema operativo VM preenchendo as etapas do seu sistema operativo nos [endereços IP Add a uma](#os-config) secção do sistema operativo VM deste artigo. Não adicione o endereço IP público ao sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
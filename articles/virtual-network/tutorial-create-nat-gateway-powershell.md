---
title: 'Tutorial: Criar uma porta DE NAT - PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Começar a criar um gateway NAT usando a Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620239"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Tutorial: Criar um gateway NAT usando Azure PowerShell

Este tutorial mostra-lhe como utilizar o serviço Azure Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual
> * Cria uma máquina virtual.
> * Crie um gateway NAT e associe-se à rede virtual.
> * Ligue-se à máquina virtual e verifique o endereço IP DO NAT.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>Criar o portal NAT

Nesta secção criamos a porta de entrada DA NAT e recursos de apoio.

* Para aceder à Internet, precisa de um ou mais endereços IP públicos para o gateway NAT. Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um recurso de endereço IP público chamado **myPublicIP** no **myResourceGroupNAT**. 

* Crie uma porta de entrada Azure NAT global com [a New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). O resultado deste comando criará um recurso de gateway chamado **myNATgateway** que utiliza o endereço IP público **myPublicIP**. O tempo limite de marcha lenta está definido para 10 minutos.  

* Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** usando [New-AzVirtualNetSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) no **myResourceGroup** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O espaço de endereço IP para a rede virtual é **10.1.0.0/16**. A sub-rede dentro da rede virtual é **de 10.1.0.0/24**.

* Crie um anfitrião Azure Bastion chamado **myBastionHost** para aceder à máquina virtual. Use [a New-AzBastion](/powershell/module/az.network/new-azbastion) para criar o hospedeiro de bastião. Crie um endereço IP público para o anfitrião do bastião com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Máquina virtual

Nesta secção, irá criar uma máquina virtual para testar o gateway NAT e verificar o endereço IP público da ligação de saída.

* Criar uma interface de rede com [o New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Desconfime de utilizador e palavra-passe para o VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Crie a máquina virtual com:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Aguarde que a criação da máquina virtual esteja concluída antes de passar para a secção seguinte.

## <a name="test-nat-gateway"></a>Portal DE TESTE NAT

Nesta secção, vamos testar o portal NAT. Primeiro descobriremos o IP público do portal NAT. Ligaremos-nos então à máquina virtual de teste e verificaremos a ligação de saída através do gateway NAT.
    
1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. Encontre o endereço IP público para o gateway NAT no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myPublicIP**.

2. Tome nota do endereço IP público:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Descubra o endereço IP público do gateway NAT" border="true":::

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, a partir da lista de recursos, selecione **myVM** que está localizado no grupo de recursos **myResourceGroupNAT.**

4. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

7. Abrir **internet Explorer** no **myTestVM**.

8. **https://whatsmyip.com** Insira na barra de endereços.

9. Verifique se o endereço IP apresentado corresponde ao endereço de gateway NAT indicado no passo anterior:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer mostrando IP externo de saída" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a rede virtual, a máquina virtual e o gateway NAT com os seguintes passos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure Virtual Network NAT, consulte:
> [!div class="nextstepaction"]
> [Visão geral do NAT da rede virtual](nat-overview.md)

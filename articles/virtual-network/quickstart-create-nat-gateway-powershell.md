---
title: 'Quickstart: Criar um portal NAT - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Este quickstart mostra como criar um gateway NAT usando O Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 7e8cbadf2c68b97451b40afb876ceb7d88d3758e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661061"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Quickstart: Criar um portal NAT usando o Azure PowerShell

Este quickstart mostra-lhe como utilizar o serviço AZURE Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 

>[!NOTE] 
>A Rede Virtual Azure NAT está disponível como pré-visualização pública neste momento e disponível num conjunto limitado de [regiões.](https://azure.microsoft.com/global-infrastructure/regions/) Esta pré-visualização é fornecida sem um acordo de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter mais informações.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode completar este tutorial utilizando a Azure Cloud Shell ou executar os comandos localmente.  Se ainda não usou a Azure Cloud Shell, [inscreva-se agora.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos com [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Criar o portal NAT

As opções públicas de IP para gateway NAT são:

* **Endereços IP públicos**
* **Prefixos públicos de IP**

Ambos podem ser usados com porta de entrada NAT.

Vamos adicionar um endereço IP público e um prefixo IP público a este cenário para demonstrar.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à Internet, precisa de um ou mais endereços IP públicos para o portal NAT. Utilize [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso público de endereço IP chamado **myPublicIP** no **myResourceGroupNAT**. O resultado deste comando será armazenado numa **variável $publicIP** para posterior utilização.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Utilize o [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) para criar um recurso público de prefixo IP chamado **myPublicIPprefix** no **myResourceGroupNAT**.  O resultado deste comando será armazenado numa variável chamada **$publicIPPrefix** para posterior utilização.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool ip público e um prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Mude o tempo de inatividade do padrão de 4 minutos para 10 minutos.

Crie um portal azure global NAT com [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). O resultado deste comando criará um recurso de gateway denominado **myNATgateway** que utiliza o endereço IP público **myPublicIP** e o ip público prefixo **myPublicIPprefix**. O tempo limite está definido para 10 minutos.  O resultado deste comando será armazenado numa variável chamada **$natGateway** para posterior utilização.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Neste ponto, o portal NAT está funcional e tudo o que falta é configurar quais as subredes de uma rede virtual que devem usá-lo.

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Crie a rede virtual e associe a subnet à porta de entrada.

Crie uma rede virtual chamada **myVnet** com uma subnet chamada **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) no **myResourceGroup** utilizando [new-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). O espaço de endereço IP para a rede virtual é **192.168.0.0.0/16**. A sub-rede dentro da rede virtual é **de 192.168.0.0/24**.  O resultado dos comandos será armazenado em variáveis denominadas **$subnet** e **$vnet** para posterior utilização.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Todo o tráfego de saída para destinos da Internet está agora a usar o serviço NAT.  Não é necessário configurar um UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Criar um VM para usar o serviço NAT

Vamos agora criar um VM para usar o serviço NAT.  Este VM tem um IP público para usar como um IP público de nível de instância para permitir o acesso ao VM.  O serviço NAT está consciente e substituirá o destino de Internet padrão na sua subnet. O endereço IP público da VM não será usado para ligações de saída.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para fonte VM

Criamos um IP público para ser usado para aceder ao VM.  Utilize [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso público de endereço IP chamado **myPublicIPVM** no **myResourceGroupNAT**.  O resultado deste comando será armazenado numa variável chamada **$publicIpVM** para posterior utilização.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Crie um NSG e exponha o ponto final do SSH para VM

Os endereços IP públicos padrão são "seguros por defeito", precisamos criar um NSG para permitir o acesso de entrada para ssh. Utilize o [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) para criar um recurso NSG chamado **myNSG**. Utilize [new-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) para criar uma regra NSG para acesso SSH nomeado **sSH** no **myResourceGroupNAT**.  O resultado deste comando será armazenado numa variável denominada **$nsg** para posterior utilização.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Criar NIC para VM

Criar uma interface de rede com [new-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) chamada **myNic**. Este comando associa o endereço IP público e o grupo de segurança da rede. O resultado deste comando será armazenado numa variável chamada **$nic** para posterior utilização.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Criar VM

#### <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Precisa de um par de chaves SSH para concluir este início rápido. Se já tiver um par de chaves SSH, pode ignorar este passo.

Utilize ssh-keygen para criar um par de chaves SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Para obter informações mais detalhadas sobre como criar pares de chaves SSH, incluindo a utilização do PuTTy, veja [Como utilizar chaves SSH com o Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Se criar o par de chaves SSH utilizando a Cloud Shell, o par de chaves é armazenado numa imagem de recipiente. Esta [conta de armazenamento é criada automaticamente.](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage) Não apague a conta de armazenamento, nem a parte do ficheiro no seu interior, até depois de ter recuperado as chaves.

#### <a name="create-vm-configuration"></a>Criar configuração VM

Para criar um VM no PowerShell, cria-se uma configuração que tem configurações para a imagem usar, tamanho e opções de autenticação. A configuração é usada para construir o VM.

Defina as credenciais SSH, as informações do SO e o tamanho da VM. Neste exemplo, a chave SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar um VM chamado **myVM** com [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) no **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Aguarde que o VM se prepare para se deslocar e continue com o resto dos passos.

## <a name="discover-the-ip-address-of-the-vm"></a>Descubra o endereço IP do VM

Primeiro, temos de descobrir o endereço IP do VM que criaste. Para obter o endereço IP público do VM, utilize [O Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo para aceder ao VM.

### <a name="sign-in-to-vm"></a>Inscreva-se na VM

As credenciais SSH devem ser armazenadas na sua Cloud Shell da operação anterior.  Abra uma [Nuvem Azure](https://shell.azure.com) Shell no seu navegador. Utilize o endereço IP recuperado no passo anterior para sSH para a máquina virtual.

```bash
ssh azureuser@<ip-address-destination>
```

Está pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) para remover o grupo de recursos e todos os recursos contidos no interior.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um portal NAT e um VM para usá-lo. 

Reveja as métricas no Monitor Azure para ver o seu serviço NAT a funcionar. Diagnosticar problemas como a exaustão de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é abordado adicionando recursos de endereços IP públicos adicionais ou recursos públicos de prefixo IP ou ambos.


- Conheça a [Rede Virtual Azure NAT](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway NAT.](./nat-gateway-resource.md)
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Arranque rápido para a implementação do [recurso de gateway NAT utilizando](./quickstart-create-nat-gateway-portal.md)o portal Azure .
- [Forneça feedback sobre a Pré-Visualização Pública](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]



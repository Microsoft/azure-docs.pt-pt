---
title: Criar um VM Azure com Networking Acelerado - Azure PowerShell
description: Aprenda a criar uma máquina virtual Linux com Networking Acelerado.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245060"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Criar uma máquina virtual Windows com Networking Acelerado usando o Azure PowerShell

Neste tutorial, aprende-se a criar uma máquina virtual do Windows (VM) com Networking Acelerado. Para criar um VM Linux com Networking Acelerado, consulte [Create a Linux VM com Networking Acelerado](create-vm-accelerated-networking-cli.md). A ligação acelerada em rede permite a virtualização de I/O de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este caminho de alto desempenho contorna o hospedeiro a partir da trajetória de dados, reduzindo a latência, o nervosismo e a utilização do CPU, para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A imagem seguinte mostra a comunicação entre dois VMs com e sem ligação acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem uma rede acelerada, todo o tráfego em rede dentro e fora do VM deve atravessar o hospedeiro e o interruptor virtual. O interruptor virtual fornece todas as aplicações políticas, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços virtualizados da rede para o tráfego de rede. Para saber mais sobre os interruptores virtuais, consulte a [virtualização da rede Hyper-V e o interruptor virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com uma rede acelerada, o tráfego de rede chega à interface de rede do VM (NIC), e é depois encaminhado para o VM. Todas as políticas de rede que o interruptor virtual aplica são agora descarregadas e aplicadas em hardware. A aplicação da política em hardware permite ao NIC encaminhar o tráfego de rede diretamente para o VM, contornando o anfitrião e o interruptor virtual, mantendo ao mesmo tempo toda a política aplicada no hospedeiro.

Os benefícios da ligação acelerada em rede aplicam-se apenas ao VM em que está ativado. Para obter os melhores resultados, é ideal permitir esta funcionalidade em pelo menos dois VMs ligados à mesma Rede Virtual Azure (VNet). Ao comunicar através de VNets ou ligar-se no local, esta funcionalidade tem um impacto mínimo na latência geral.

## <a name="benefits"></a>Vantagens
* **Latência inferior / Pacotes mais altos por segundo (pps):** Remover o interruptor virtual da pata de dados remove o tempo que os pacotes gastam no hospedeiro para processamento de políticas e aumenta o número de pacotes que podem ser processados dentro do VM.
* **Nervosismo reduzido:** O processamento de interruptores virtuais depende da quantidade de política que precisa de ser aplicada e da carga de trabalho da CPU que está a fazer o processamento. Descarregar a aplicação da política para o hardware remove essa variabilidade entregando pacotes diretamente ao VM, removendo o anfitrião para a comunicação VM e todas as interrupções de software e interruptores de contexto.
* **Diminuição da utilização do CPU:** Contornar o interruptor virtual no hospedeiro leva a uma menor utilização de CPU para o tráfego da rede de processamento.

## <a name="limitations-and-constraints"></a>Limitações e Constrangimentos

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As seguintes distribuições são suportadas fora da caixa da Galeria Azure:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Casos de VM suportados
O Networking Acelerado é suportado na maioria dos tamanhos de instância otimizados para a computação com 2 ou mais vCPUs.  Estas séries apoiadas são: D/DSv2 e F/Fs

Em casos que suportam hiperthreading, o Networking Acelerado é suportado em casos vM com 4 ou mais vCPUs. As séries suportadas são: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre os casos de VM, consulte os [tamanhos do Windows VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiões
Disponível em todas as regiões públicas de Azure e Nuvem governamental azure.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitar a rede acelerada num VM em execução
Um tamanho VM suportado sem ligação acelerada de rede só pode ter a funcionalidade ativada quando é parada e negociada.

### <a name="deployment-through-azure-resource-manager"></a>Implantação através do Gestor de Recursos Azure
As máquinas virtuais (clássicas) não podem ser implantadas com networking acelerado.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Criar um VM Windows com Rede Acelerada Azure
## <a name="portal-creation"></a>Criação do portal
Embora este artigo forneça passos para criar uma máquina virtual com networking acelerado utilizando o Azure Powershell, também pode [criar uma máquina virtual com networking acelerado utilizando o portal Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ao criar uma máquina virtual no portal, na **Create a virtual machine** blade, escolha o separador **Networking.**  Neste separador, existe uma opção para **a rede Acelerada**.  Se escolheu um [sistema operativo suportado](#supported-operating-systems) e [o tamanho vm,](#supported-vm-instances)esta opção irá automaticamente povoar para "On".  Caso contrário, irá povoar a opção "Off" para a Rede Acelerada e dar ao utilizador uma razão para não estar ativado.   
* *Nota:* Apenas sistemas operativos suportados podem ser ativados através do portal.  Se estiver a utilizar uma imagem personalizada e a sua imagem suportar o Networking Acelerado, por favor crie o seu VM utilizando CLI ou Powershell. 

Após a criação da máquina virtual, pode confirmar que a Rede Acelerada está ativada seguindo as instruções no Confirme que a rede acelerada está ativada.

## <a name="powershell-creation"></a>Criação powershell
## <a name="create-a-virtual-network"></a>Criar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale a versão 1.0.0 ou mais tarde do [Azure PowerShell.](/powershell/azure/install-az-ps) Para encontrar a sua versão atualmente instalada, execute `Get-Module -ListAvailable Az`. Se precisar de instalar ou atualizar, instale a versão mais recente do módulo Az a partir da [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). Numa sessão powerShell, inicie sessão numa conta Azure utilizando [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myNic*, e *myVM*.

Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *central:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Primeiro, crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). O exemplo seguinte cria uma subnet chamada *mySubnet:*

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Crie uma rede virtual com [new-AzVirtualNetwork,](/powershell/module/az.Network/New-azVirtualNetwork)com a subnet *mySubnet.*

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Em primeiro lugar, crie uma regra de grupo de segurança de rede com [new-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Crie um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e atribua-lhe a regra de segurança *Allow-RDP-All.* Além da regra *Allow-RDP-All,* o grupo de segurança da rede contém várias regras predefinidas. Uma regra predefinida desativa todo o acesso à entrada a partir da Internet, razão pela qual a regra *Allow-RDP-All* é atribuída ao grupo de segurança da rede para que possa ligar-se remotamente à máquina virtual, uma vez criada.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associe o grupo de segurança da rede à subnet *mySubnet* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A regra no grupo de segurança da rede é eficaz para todos os recursos implantados na sub-rede.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com networking acelerado
Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Não é necessário um endereço IP público se não pretender aceder à máquina virtual a partir da Internet, mas para completar os passos deste artigo, é necessário.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Criar uma interface de rede com [o New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) com uma rede acelerada ativada e atribuir o endereço IP público à interface da rede. O exemplo seguinte cria uma interface de rede chamada *myNic* na subnet *mySubnet* da rede virtual *myVnet* e atribui-lhe o endereço IP público *myPublicIp:*

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Detete as suas credenciais VM na variável `$cred` utilizando [get-credential:](/powershell/module/microsoft.powershell.security/get-credential)

```powershell
$cred = Get-Credential
```

Primeiro, defina o seu VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O exemplo que se segue define um VM denominado *myVM* com um tamanho VM que suporta o Networking Acelerado *(Standard_DS4_v2):*

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Para obter uma lista de todos os tamanhos e características vm, consulte os [tamanhos vm do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Crie o resto da configuração VM com [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O exemplo seguinte cria um VM Do Windows Server 2016:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Fixe a interface de rede que criou anteriormente com [add-AzVMNetworkInterface:](/powershell/module/az.compute/add-azvmnetworkinterface)

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Por fim, crie o seu VM com [a New-AzVM:](/powershell/module/az.compute/new-azvm)

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Confirme que o controlador está instalado no sistema operativo

Assim que criar o VM em Azure, ligue-se ao VM e confirme que o controlador está instalado no Windows.

1. A partir de um navegador de Internet, abra o [portal](https://portal.azure.com) Azure e inscreva-se na sua conta Azure.
2. Na caixa que contém os recursos de *pesquisa* de texto no topo do portal Azure, digite *myVm*. Quando o **myVm** aparecer nos resultados da pesquisa, clique nele. Se criar é visível sob o botão **Connect,** o Azure ainda não terminou de criar o VM. Clique em **Ligar** no canto superior esquerdo da visão geral apenas depois de deixar de ver **criar** sob o botão **Ligar.**
3. Introduza o nome de utilizador e a palavra-passe que inseriu Criar [a máquina virtual](#create-the-virtual-machine). Se nunca esteve ligado a um VM Windows em Azure, consulte [Connect to virtual machine](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Clique no botão Windows Iniciar e clique no **Gestor do Dispositivo**. Expanda o nó dos adaptadores da **Rede.** Confirme que aparece o **Adaptador ethernet de Função Virtual Mellanox ConnectX-3,** como mostra a seguinte imagem:

    ![Gestor de Dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

O Networking Acelerado está agora ativado para o seu VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Ativar a rede acelerada nos VMs existentes
Se criou um VM sem Networking Acelerado, é possível ativar esta funcionalidade num VM existente.  O VM deve apoiar o Networking Acelerado, cumprindo os seguintes pré-requisitos que também estão descritos acima:

* O VM deve ser um tamanho suportado para a rede acelerada
* O VM deve ser uma imagem suportada da Galeria Azure (e versão kernel para Linux)
* Todos os VMs num conjunto de disponibilidade ou VMSS devem ser parados/deallocalizados antes de permitir a Rede Acelerada em qualquer NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>VMs e VMs individuais num conjunto de disponibilidade
Primeira paragem/deslocar o VM ou, se um Conjunto de Disponibilidade, todos os VMs do conjunto:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Importante, por favor note que se o seu VM foi criado individualmente, sem um conjunto de disponibilidade, só precisa de parar/desalojar o VM individual para permitir a Ligação acelerada.  Se o seu VM foi criado com um conjunto de disponibilidade, todos os VMs contidos no conjunto de disponibilidade terão de ser parados/deallocalizados antes de permitir a Rede Acelerada em qualquer um dos NICs. 

Uma vez parado, ative a Rede Acelerada no NIC do seu VM:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Reinicie o seu VM ou, se num conjunto de disponibilidade, todos os VMs do conjunto, e confirme que o Networking Acelerado está ativado:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
O VMSS é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.  Primeiro, parem os VMs:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Assim que os VMs estiverem parados, atualize a propriedade De Rede Acelerada sob a interface de rede:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Tenha em anote que um VMSS tem atualizações VM que aplicam atualizações utilizando três configurações diferentes, automáticas, rolantes e manuais.  Nestas instruções, a política é definida como automática para que o VMSS reacione as alterações imediatamente após o reinício.  Para o colocar em automático de modo a que as alterações sejam imediatamente captadas:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Finalmente, reinicie o VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Assim que reiniciar, aguarde que as atualizações terminem, mas uma vez concluída, o VF aparecerá dentro do VM.  (Certifique-se de que está a utilizar um osso e tamanho VM suportados)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionar os VMexistentes existentes com Networking Acelerado

Os VMs com Networking Acelerado ativado só podem ser redimensionados para VMs que suportam o Networking Acelerado.  

Um VM com rede acelerada ativado não pode ser redimensionado para uma instância VM que não suporta a rede acelerada utilizando a operação de redimensionamento.  Em vez disso, para redimensionar um destes VMs:

* Stop/Deallocate o VM ou se num conjunto de disponibilidade/VMSS, pare/deslocar todos os VMs no conjunto/VMSS.
* A ligação acelerada deve ser desativada no NIC do VM ou se num conjunto de disponibilidade/VMSS, todos os VMs no conjunto/VMSS.
* Uma vez desativada a rede acelerada, o conjunto VM/disponibilidade/VMSS pode ser movido para um novo tamanho que não suporta o Networking Acelerado e reiniciado.

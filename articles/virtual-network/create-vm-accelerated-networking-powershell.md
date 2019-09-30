---
title: Criar uma máquina virtual do Azure com rede acelerada | Microsoft Docs
description: Saiba como criar uma máquina virtual Linux com rede acelerada.
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
ms.openlocfilehash: f8f4f55f2c2aa4a0f9cce08e10c9f12f81a54dba
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678001"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual do Windows com rede acelerada

Neste tutorial, você aprenderá a criar uma VM (máquina virtual) do Windows com rede acelerada. Para criar uma VM do Linux com rede acelerada, consulte [criar uma VM do Linux com rede acelerada](create-vm-accelerated-networking-cli.md). A rede acelerada habilita o SR-IOV (virtualização de e/s de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de DataPath, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes em tipos de VM com suporte. A figura a seguir mostra a comunicação entre duas VMs com e sem rede acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem a rede acelerada, todo o tráfego de rede de entrada e saída da VM deve atravessar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais sobre os comutadores virtuais, consulte [virtualização de rede Hyper-V e comutador virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com a rede acelerada, o tráfego de rede chega à NIC (interface de rede) da VM e, em seguida, é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica agora são descarregadas e aplicadas no hardware. A aplicação da política em hardware permite que a NIC encaminhe o tráfego de rede diretamente para a VM, ignorando o host e o comutador virtual, mantendo toda a política aplicada no host.

Os benefícios da rede acelerada só se aplicam à VM em que ela está habilitada. Para obter os melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma VNet (rede virtual) do Azure. Ao se comunicar entre VNets ou conectar-se localmente, esse recurso tem um impacto mínimo sobre a latência geral.

## <a name="benefits"></a>Benefícios
* **Latência mais baixa/pacotes maiores por segundo (PPS):** Remover o comutador virtual do caminho de data remove o tempo que os pacotes gastam no host para processamento de política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Variação reduzida:** O processamento do comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade fornecendo pacotes diretamente para a VM, removendo o host para comunicação de VM e todas as interrupções de software e alternâncias de contexto.
* **Utilização de CPU reduzida:** Ignorar o comutador virtual no host leva a menos utilização de CPU para processar o tráfego de rede.

## <a name="limitations-and-constraints"></a>Limitações e restrições

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições a seguir têm suporte pronto para uso na galeria do Azure:
* **Windows Server 2016 datacenter** 
* **Datacenter do Windows Server 2012 R2**
* **Windows Server 2019 datacenter**

### <a name="supported-vm-instances"></a>Instâncias de VM com suporte
A rede acelerada tem suporte na maioria dos tamanhos de instância de uso geral e otimizado para computação com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/FS

Em instâncias que dão suporte a hyperthreading, a rede acelerada tem suporte em instâncias de VM com 4 ou mais vCPUs. As séries com suporte são: D/Dsv3, E/Esv3, Fsv2, Lsv2, MS/MMS e MS/Mmsv2.

Para obter mais informações sobre instâncias de VM, consulte [tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiões
Disponível em todas as regiões públicas do Azure e na nuvem do Azure governamental.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitando a rede acelerada em uma VM em execução
Um tamanho de VM com suporte sem rede acelerada habilitada só pode ter o recurso habilitado quando é interrompido e desalocado.

### <a name="deployment-through-azure-resource-manager"></a>Implantação por meio do Azure Resource Manager
As máquinas virtuais (clássicas) não podem ser implantadas com rede acelerada.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Criar uma VM do Windows com a rede acelerada do Azure
## <a name="portal-creation"></a>Criação do portal
Embora este artigo forneça etapas para criar uma máquina virtual com rede acelerada usando o Azure PowerShell, você também pode [criar uma máquina virtual com rede acelerada usando o portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ao criar uma máquina virtual no portal, na folha **criar uma máquina virtual** , escolha a guia **rede** .  Nessa guia, há uma opção para **rede acelerada**.  Se você tiver escolhido um [sistema operacional com suporte](#supported-operating-systems) e um [tamanho de VM](#supported-vm-instances), essa opção será preenchida automaticamente como "ativada".  Caso contrário, ele preencherá a opção "desativado" para rede acelerada e dará ao usuário um motivo pelo qual ele não está habilitado.   
* *Nota:* Somente sistemas operacionais com suporte podem ser habilitados por meio do Portal.  Se você estiver usando uma imagem personalizada e sua imagem der suporte à rede acelerada, crie sua VM usando a CLI ou o PowerShell. 

Depois que a máquina virtual for criada, você poderá confirmar se a rede acelerada está habilitada seguindo as instruções em confirmar se a rede acelerada está habilitada.

## <a name="powershell-creation"></a>Criação do PowerShell
## <a name="create-a-virtual-network"></a>Criar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale [Azure PowerShell](/powershell/azure/install-az-ps) versão 1.0.0 ou posterior. Para localizar a versão atualmente instalada, execute `Get-Module -ListAvailable Az`. Se você precisar instalar ou atualizar o, instale a versão mais recente do módulo AZ do [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az). Em uma sessão do PowerShell, faça logon em uma conta do Azure usando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Exemplos de nomes de parâmetro incluem *MyResource*, *myNic*e *myVM*.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group no local *centralus* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Primeiro, crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). O exemplo a seguir cria uma sub-rede chamada *mysubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), com a sub-rede *mysubnet* .

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Primeiro, crie uma regra de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e atribua a regra de segurança *Allow-RDP-All* a ele. Além da regra *Allow-RDP-All* , o grupo de segurança de rede contém várias regras padrão. Uma regra padrão desabilita todo o acesso de entrada da Internet, motivo pelo qual a regra *Allow-RDP-All* é atribuída ao grupo de segurança de rede para que você possa se conectar remotamente à máquina virtual, depois de ela ser criada.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associe o grupo de segurança de rede à sub-rede *mysubnet* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A regra no grupo de segurança de rede é eficaz para todos os recursos implantados na sub-rede.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com rede acelerada
Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Um endereço IP público não é necessário se você não planeja acessar a máquina virtual pela Internet, mas para concluir as etapas neste artigo, ele é necessário.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Crie um adaptador de rede com [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) com rede acelerada habilitada e atribua o endereço IP público à interface de rede. O exemplo a seguir cria uma interface de rede chamada *myNic* na sub-rede *mysubnet* da rede virtual *MYVNET* e atribui o endereço IP público *myPublicIp* a ela:

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

Defina suas credenciais de VM para a variável `$cred` usando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Primeiro, defina sua VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O exemplo a seguir define uma VM chamada *myVM* com um tamanho de VM que dá suporte à rede acelerada (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Para obter uma lista de todos os tamanhos e características de VM, consulte [tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Crie o restante da sua configuração de VM com [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O exemplo a seguir cria uma VM do Windows Server 2016:

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

Anexe a interface de rede que você criou anteriormente com [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Por fim, crie sua VM com [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Confirme se o driver está instalado no sistema operacional

Depois de criar a VM no Azure, conecte-se à VM e confirme se o driver está instalado no Windows.

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com sua conta do Azure.
2. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *myVm*. Quando **myVm** aparecer nos resultados da pesquisa, clique nele. Se a **criação** estiver visível no botão **conectar** , o Azure ainda não terminou de criar a VM. Clique em **conectar** no canto superior esquerdo da visão geral somente depois de não ver mais a **criação** no botão **conectar** .
3. Insira o nome de usuário e a senha que você inseriu em [criar a máquina virtual](#create-the-virtual-machine). Se você nunca se conectou a uma VM do Windows no Azure, consulte [conectar-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Clique com o botão direito do mouse no botão Iniciar do Windows e clique em **Device Manager**. Expanda o nó **adaptadores de rede** . Confirme se o **adaptador Ethernet de função virtual Mellanox ConnectX-3** aparece, conforme mostrado na figura a seguir:

    ![Gestor de Dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

A rede acelerada agora está habilitada para sua VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Habilitar rede acelerada em VMs existentes
Se você tiver criado uma VM sem rede acelerada, será possível habilitar esse recurso em uma VM existente.  A VM deve dar suporte à rede acelerada ao atender aos seguintes pré-requisitos que também são descritos acima:

* A VM deve ter um tamanho com suporte para rede acelerada
* A VM deve ser uma imagem da galeria do Azure com suporte (e a versão do kernel para Linux)
* Todas as VMs em um conjunto de disponibilidade ou VMSS devem ser interrompidas/desalocadas antes de habilitar a rede acelerada em qualquer NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>VMs individuais & VMs em um conjunto de disponibilidade
Primeiro pare/Desaloque a VM ou, se houver um conjunto de disponibilidade, todas as VMs no conjunto:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Importante. Observe que, se a VM tiver sido criada individualmente, sem um conjunto de disponibilidade, você só precisará parar/desalocar a VM individual para habilitar a rede acelerada.  Se sua VM foi criada com um conjunto de disponibilidade, todas as VMs contidas no conjunto de disponibilidade precisarão ser interrompidas/desalocadas antes de habilitar a rede acelerada em qualquer uma das NICs. 

Depois de parar, habilite a rede acelerada na NIC da VM:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Reinicie a VM ou, se estiver em um conjunto de disponibilidade, todas as VMs no conjunto e confirme se a rede acelerada está habilitada:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS é um pouco diferente, mas segue o mesmo fluxo de trabalho.  Primeiro, pare as VMs:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Depois que as VMs forem interrompidas, atualize a propriedade de rede acelerada na interface de rede:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Observe que um VMSS tem atualizações de VM que aplicam atualizações usando três configurações diferentes, automática, sem interrupção e manual.  Nessas instruções, a política é definida como automática para que o VMSS selecione as alterações imediatamente após a reinicialização.  Para defini-lo como automático para que as alterações sejam imediatamente selecionadas:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Por fim, reinicie o VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Depois de reiniciar, aguarde a conclusão das atualizações, mas depois de concluída, o FV aparecerá dentro da VM.  (Verifique se você está usando um sistema operacional com suporte e o tamanho da VM)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionando VMs existentes com rede acelerada

As VMs com rede acelerada habilitada só podem ser redimensionadas para VMs que dão suporte à rede acelerada.  

Uma VM com rede acelerada habilitada não pode ser redimensionada para uma instância de VM que não dá suporte à rede acelerada usando a operação de redimensionamento.  Em vez disso, para redimensionar uma dessas VMs:

* Pare/Desaloque a VM ou se estiver em um conjunto de disponibilidade/VMSS, pare/desaloque todas as VMs no conjunto/VMSS.
* A rede acelerada deve ser desabilitada na NIC da VM ou se estiver em um conjunto de disponibilidade/VMSS, todas as VMs no conjunto/VMSS.
* Quando a rede acelerada está desabilitada, a VM/conjunto de disponibilidade/VMSS pode ser movida para um novo tamanho que não dá suporte à rede acelerada e reiniciada.

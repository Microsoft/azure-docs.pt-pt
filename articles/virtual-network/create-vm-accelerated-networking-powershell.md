---
title: Criar uma máquina virtual do Azure com o Accelerated Networking | Documentos da Microsoft
description: Saiba como criar uma máquina virtual Linux com redes aceleradas.
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
ms.openlocfilehash: c4567919490c8bc9094dea3dddbe22550d9eebb2
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192910"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual do Windows com redes aceleradas

Neste tutorial, saiba como criar uma máquina virtual de Windows (VM) com redes aceleradas. Para criar uma VM do Linux com redes aceleradas, consulte [criar uma VM com Accelerated Networking](create-vm-accelerated-networking-cli.md). Funcionamento em rede acelerado permite que a virtualização de e/s de raiz única (SR-IOV) para uma VM, melhorando em grande medida o desempenho de rede. Este caminho de alto desempenho ignora o anfitrião a partir de datapath, reduzindo a latência, distorção e a utilização da CPU, para utilização com as mais exigentes cargas de trabalho de rede sobre os tipos VM suportados. A imagem seguinte mostra a comunicação entre duas VMs com e sem redes aceleradas:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem redes aceleradas, todo o tráfego de rede e para a VM deve percorrer o anfitrião e o comutador virtual. O comutador virtual fornece todos os imposição de políticas, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços de rede virtualizado para o tráfego de rede. Para obter mais informações sobre comutadores virtuais, veja [Virtualização de rede do Hyper-V e o comutador virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com redes aceleradas, o tráfego de rede chega à interface de rede da VM (NIC) e, em seguida, é reencaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica-se agora são descarregadas e aplicadas no hardware. Aplicando a diretiva de hardware, permite que o NIC para reencaminhar o tráfego de rede diretamente à VM, ignorando o anfitrião e o comutador virtual, enquanto mantém a diretiva de todos os que ele aplicado no anfitrião.

Os benefícios do funcionamento em rede acelerado só se aplicam à VM que está ativada no. Para obter melhores resultados, é ideal para ativar esta funcionalidade em, pelo menos, duas VMs ligadas à mesma rede Virtual do Azure (VNet). Durante a comunicação entre VNets ou ligação no local, esta funcionalidade tem um impacto mínimo para latência geral.

## <a name="benefits"></a>Benefícios
* **Menor latência / superior pacotes por segundo (pps):** Remover o comutador virtual do datapath remove o tempo gasto de pacotes no anfitrião para processamento de diretiva e aumenta o número de pacotes que podem ser processadas dentro da VM.
* **Menor interferência:** Processamento de comutador virtual depende da quantidade de política que tem de ser aplicadas e a carga de trabalho da CPU que está a fazer o processamento. Descarregar a imposição de política para o hardware remove essa variabilidade fornecendo pacotes diretamente à VM, remover o anfitrião a comunicação de VM e todas as interrupções de software e Alternâncias de contexto.
* **Diminuição da utilização da CPU:** Ignorar o comutador virtual no anfitrião leva para menos de utilização da CPU para processar o tráfego de rede.

## <a name="limitations-and-constraints"></a>Limitações e restrições

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições seguintes são suportadas prontos a utilizar da galeria do Azure:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Instâncias VM suportadas
Funcionamento em rede acelerado é suportado em fins mais gerais e tamanhos de instâncias com otimização de computação com vCPUs 2 ou mais.  Esta série suportado é: D/DSv2 e F/Fs

Redes aceleradas é suportada em instâncias que suportam o hyperthreading, nas instâncias VM com vCPUs 4 ou mais. Série suportado é: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre as instâncias de VM, consulte [tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiões
Disponível em todas as regiões públicas do Azure e na Cloud do Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Ativar o Accelerated Networking numa VM em execução
Um tamanho de VM suportado sem aceleração de rede ativada só pode ter a funcionalidade ativada quando está parado e desalocado.

### <a name="deployment-through-azure-resource-manager"></a>Implementação através do Azure Resource Manager
Não é possível implementar máquinas virtuais (clássico) com redes aceleradas.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Criar um VM do Windows com redes aceleradas do Azure
## <a name="portal-creation"></a>Criação do portal
Embora este artigo fornece passos para criar uma máquina virtual com o funcionamento em rede acelerado com o Azure Powershell, também pode [criar uma máquina virtual com o funcionamento em rede acelerado com o portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ao criar uma máquina virtual no portal, no **criar uma máquina virtual** painel, escolha a **Networking** separador.  Neste separador, é uma opção para **Accelerated networking**.  Se tiver optado por um [sistema operativo suportado](#supported-operating-systems) e [tamanho da VM](#supported-vm-instances), esta opção será preenchida automaticamente como "On".  Caso contrário, irá preencher a opção "Desativado" para redes aceleradas e dar ao utilizador um motivo por que ele não é possível ativar.   
* *Nota:* Apenas os sistemas operativos suportados, pode ser ativados através do portal.  Se estiver a utilizar uma imagem personalizada e sua imagem oferece suporte a redes aceleradas, crie a VM com a CLI ou Powershell. 

Depois da máquina virtual é criada, pode confirmar que Accelerated Networking está ativada ao seguir as instruções de confirmação que accelerated networking está ativada.

## <a name="powershell-creation"></a>Criação de PowerShell
## <a name="create-a-virtual-network"></a>Criar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale [do Azure PowerShell](/powershell/azure/install-az-ps) versão 1.0.0 ou posterior. Para localizar a versão atualmente instalada, execute `Get-Module -ListAvailable Az`. Se precisar de instalar ou atualizar, instale a versão mais recente do módulo de Az a [galeria do PowerShell](https://www.powershellgallery.com/packages/Az). Numa sessão do PowerShell, inicie sessão para uma conta do Azure com [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluídos *myResourceGroup*, *myNic*, e *myVM*.

Criar um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* no *centralus* localização:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Em primeiro lugar, crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). O exemplo seguinte cria uma sub-rede denominada *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Criar uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), com o *mySubnet* sub-rede.

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

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e atribua a *Allow-RDP-All* regra de segurança para ele. Para além da *Allow-RDP-All* regra, o grupo de segurança de rede contém várias regras de predefinidas. Uma regra predefinida desativa todos os acessos de entrada da Internet, razão pela qual o *Allow-RDP-All* regra é atribuída ao grupo de segurança de rede para que pode ligar remotamente à máquina virtual, depois de criado.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associar o grupo de segurança de rede para o *mySubnet* sub-rede com [conjunto AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A regra no grupo de segurança de rede está em vigor para todos os recursos implementados na sub-rede.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com redes aceleradas
Criar um endereço IP público [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Um endereço IP público não é necessário se não planeja para aceder à máquina virtual a partir da Internet, mas para concluir os passos neste artigo, é necessário.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Criar uma interface de rede com [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) com accelerated networking ativada e atribuir o endereço IP público à interface de rede. O exemplo seguinte cria uma interface de rede com o nome *myNic* no *mySubnet* sub-rede do *myVnet* rede virtual e atribui o *myPublicIp*  endereço IP público para o mesmo:

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

Definir as credenciais da sua VM o `$cred` variável usando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Em primeiro lugar, defina a sua VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O exemplo seguinte define uma VM com o nome *myVM* com um tamanho VM que suporte redes aceleradas (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Para obter uma lista de todos os tamanhos de VM e características, consulte [tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Criar o resto da sua configuração de VM com [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [conjunto AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O exemplo seguinte cria uma VM do Windows Server 2016:

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

Anexar a interface de rede que criou anteriormente com [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Por fim, crie a sua VM com [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Confirmar que o controlador está instalado no sistema operativo

Depois de criar a VM no Azure, ligar à VM e confirme que o driver é instalado no Windows.

1. A partir de um browser da Internet, abra o Azure [portal](https://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *myVm*. Quando **myVm** aparece nos resultados da pesquisa, clique no mesmo. Se **Creating** está visível sob o **Connect** botão, Azure não ainda acabou de criar a VM. Clique em **Connect** no canto superior esquerdo da descrição geral do apenas depois de já não verá **criando** sob o **Connect** botão.
3. Introduza o nome de utilizador e palavra-passe que introduziu no [criar a máquina virtual](#create-the-virtual-machine). Se nunca se ligar a uma VM do Windows no Azure, veja [ligar à máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Faça duplo clique no botão Iniciar do Windows e clique em **Gestor de dispositivos**. Expanda a **adaptadores de rede** nó. Confirme que o **adaptador de Ethernet de função Virtual Mellanox ConnectX-3** for apresentada, conforme mostrado na imagem seguinte:

    ![Gestor de dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

Funcionamento em rede acelerado está agora ativado para a sua VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Ativar redes aceleradas em VMs existentes
Se tiver criado uma VM sem redes aceleradas, é possível ativar esta funcionalidade numa VM existente.  A VM tem de suportar redes aceleradas cumprindo os seguintes pré-requisitos também são descritos acima:

* A VM tem de ser um tamanho suportado para redes aceleradas
* A VM tem de ser uma imagem da galeria do Azure suportados (e a versão de kernel de Linux)
* Todas as VMs num conjunto de disponibilidade ou VMSS tem de ser parada/desalocada antes de ativar redes aceleradas em qualquer NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>Conjunto de VMs individuais e de VMs de disponibilidade
Primeiro parar/desalocar a VM ou, se um conjunto de disponibilidade, todas as VMs no conjunto de:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Importante,. tenha em atenção que se a VM foi criada individualmente, sem um conjunto de disponibilidade, só precisa parar/desalocar a VM individual para ativar a redes aceleradas.  Se a VM foi criada com um conjunto de disponibilidade, todas as VMs contidas no conjunto de disponibilidade terá de ser parada/desalocada antes de ativar redes aceleradas em qualquer uma das NICs. 

Depois de parado, ative a Accelerated Networking na NIC da VM:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Reinicie a VM ou, se num conjunto de disponibilidade, todas as VMs no conjunto e confirme que o Accelerated Networking está ativado:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.  Em primeiro lugar, pare as VMs:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Assim que as VMs são paradas, atualize a propriedade redes aceleradas sob a interface de rede:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

. Tenha em atenção que um VMSS tem as atualizações VM que se aplicam a atualizações com três configurações diferentes, manuais, automática e sem interrupção.  Nestas instruções a política está definida como automático, para que a VMSS captará as alterações imediatamente após o reinício.  Defini-la como automático, para que as alterações são captadas imediatamente:

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

Uma vez que reinicie, espere pelas atualizações concluir e depois de concluir, a VF aparecerá dentro da VM.  (Certifique-se que estiver a utilizar um tamanho VM e de SO suportado)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionar VMs existentes com redes aceleradas

As VMs com redes aceleradas ativada apenas podem ser redimensionadas para VMs que suportam redes aceleradas.  

Uma VM com Accelerated Networking ativado não pode ser redimensionada para uma instância VM que não suporta redes aceleradas usando a operação de redimensionamento.  Em vez disso, para redimensionar um estas VMS:

* Parar/Desalocação da VM ou em caso de um conjunto de disponibilidade/VMSS, parar/desalocar todas as VMs no conjunto/VMSS.
* Funcionamento em rede acelerado tem de ser desativado na NIC da VM ou se estiver numa disponibilidade conjunto/VMSS, todas as VMs no conjunto/VMSS.
* Assim que o Accelerated Networking está desativada, o conjunto de disponibilidade/da VM/VMSS podem ser movidos para um novo tamanho que não suporta redes aceleradas e reiniciado.

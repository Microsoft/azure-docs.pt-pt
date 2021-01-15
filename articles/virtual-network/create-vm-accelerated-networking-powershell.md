---
title: Criar Windows VM com rede acelerada - Azure PowerShell
description: Crie uma máquina virtual Windows (VM) com rede acelerada para melhorar consideravelmente o seu desempenho em rede.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b3728a2b67529bab0900d42b3e39140d9329bc83
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223640"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Criar uma VM do Windows com rede acelerada com o Azure PowerShell

Neste tutorial, aprende-se a criar uma máquina virtual Windows (VM) com rede acelerada.

> [!NOTE]
> Para utilizar o networking acelerado com uma máquina virtual Linux, consulte [Criar um VM Linux com rede acelerada](create-vm-accelerated-networking-cli.md).

A rede acelerada permite a virtualização de E/S de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este percurso de alto desempenho contorna o hospedeiro da trajetória de dados, o que reduz a latência, o nervosismo e a utilização do CPU para as cargas de trabalho de rede mais exigentes em tipos de VM suportados. O diagrama a seguir ilustra como dois VMs comunicam com e sem rede acelerada:

![Comunicação entre máquinas virtuais Azure com e sem rede acelerada](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem uma rede acelerada, todo o tráfego de ligação em rede dentro e fora do VM deve atravessar o hospedeiro e o interruptor virtual. O interruptor virtual fornece toda a aplicação de políticas, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços virtualizados de rede para o tráfego de rede.

> [!NOTE]
> Para saber mais sobre os interruptores virtuais, consulte [a Switch Virtual Hyper-V](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Com a rede acelerada, o tráfego de rede chega à interface de rede (NIC) do VM e é depois encaminhado para o VM. Todas as políticas de rede que o interruptor virtual aplica são agora descarregadas e aplicadas em hardware. Como a política é aplicada em hardware, o NIC pode encaminhar o tráfego de rede diretamente para o VM. O NIC contorna o hospedeiro e o interruptor virtual, mantendo toda a política que aplica no hospedeiro.

Os benefícios da rede acelerada só se aplicam ao VM em que está ativado. Para obter os melhores resultados, ative esta funcionalidade em pelo menos dois VMs ligados à mesma rede virtual Azure. Ao comunicar através de redes virtuais ou conectar no local, esta funcionalidade tem um impacto mínimo na latência geral.

## <a name="benefits"></a>Benefícios

- **Menor latência / Pacotes mais altos por segundo (pps)**: Eliminar o interruptor virtual do caminho dos dados remove o tempo que os pacotes de tempo passam no hospedeiro para processamento de políticas. Também aumenta o número de pacotes que podem ser processados dentro do VM.

- **Nervosismo reduzido**: O processamento do interruptor virtual depende da quantidade de política que precisa de ser aplicada. Também depende da carga de trabalho do CPU que está a fazer o processamento. O descarregamento da aplicação da política para o hardware remove essa variabilidade entregando pacotes diretamente para o VM. O descarregamento também remove a comunicação host-to-VM, todas as interrupções de software e todos os interruptores de contexto.

- **Utilização reduzida do CPU**: Contornar o interruptor virtual no hospedeiro leva a uma menor utilização do CPU para o tráfego da rede de processamento.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

As seguintes distribuições são suportadas diretamente a partir da Galeria Azure:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Limitações e constrangimentos

### <a name="supported-vm-instances"></a>Instâncias VM apoiadas

A rede acelerada é suportada na maioria dos tamanhos de instância otimizados para o cálculo com dois ou mais CPUs virtuais (vCPUs).  Estas séries suportadas são: Dv2/DSv2 e F/Fs.

Em casos que suportam a hiper-leitura, a rede acelerada é suportada em instâncias VM com quatro ou mais vCPUs. As séries apoiadas são: D/Dsv3, D/Dsv4, Da/Dasv4, E/Esv3, Ea/Easv4, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre as instâncias VM, consulte [tamanhos para máquinas virtuais windows em Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Imagens personalizadas

Se estiver a utilizar uma imagem personalizada e a sua imagem suportar rede acelerada, certifique-se de que tem os controladores necessários que funcionam com mellanox ConnectX-3 e ConnectX-4 Lx NICs no Azure.

### <a name="regions"></a>Regiões

A rede acelerada está disponível em todas as regiões globais do Azure e na Cloud do Governo de Azure.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Permitir uma rede acelerada num VM em execução

Um tamanho VM suportado sem rede acelerada ativado só pode ter a funcionalidade ativada quando esta é interrompida elocada.

### <a name="deployment-through-azure-resource-manager"></a>Implementação através do Gestor de Recursos Azure

As máquinas virtuais (clássicas) não podem ser implantadas com rede acelerada.

## <a name="vm-creation-using-the-portal"></a>Criação VM usando o portal

Embora este artigo forneça passos para criar um VM com rede acelerada utilizando o Azure PowerShell, também pode [utilizar o portal Azure para criar uma máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que permita uma rede acelerada. Quando criar um VM no portal, na página **de máquina virtual,** escolha o **separador 'Rede'.** Este separador tem uma opção para **rede acelerada.** Se tiver escolhido um [sistema operativo suportado](#supported-operating-systems) e tamanho [VM,](#supported-vm-instances)esta opção é automaticamente definida para **On**. Caso contrário, a opção está definida para **Desligar**, e a Azure mostra a razão pela qual não pode ser ativada.

> [!NOTE]
> Apenas sistemas operativos suportados podem ser ativados através do portal. Se estiver a utilizar uma imagem personalizada e a sua imagem suportar uma rede acelerada, crie o seu VM utilizando CLI ou PowerShell. 

Depois de criar o VM, pode confirmar se a rede acelerada está ativada. Siga estas instruções:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus VMs. Procure e selecione **máquinas Virtuais.**

2. Na lista de máquinas virtuais, escolha o seu novo VM.

3. Na barra de menus VM, escolha **Networking**.

Na informação da interface de rede, junto à etiqueta **de rede acelerada,** o portal apresenta **desativado** ou **ativado** para o estado acelerado da rede.

## <a name="vm-creation-using-powershell"></a>Criação VM usando PowerShell

Antes de prosseguir, instale a versão 1.0.0 ou mais tarde da [Azure PowerShell.](/powershell/azure/install-az-ps) Para encontrar a sua versão atualmente instalada, corra `Get-Module -ListAvailable Az` . Se necessitar de instalar ou atualizar, instale a versão mais recente do módulo Az a partir da [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). Numa sessão PowerShell, inscreva-se numa conta Azure utilizando [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *myNic* e *myVM*.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O seguinte comando cria um grupo de recursos chamado *myResourceGroup* na localização *central:*

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). O seguinte comando cria uma sub-rede chamada *mySubnet:*

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Crie uma rede virtual com [a New-AzVirtualNetwork,](/powershell/module/az.Network/New-azVirtualNetwork)com a *sub-rede mySubnet.*

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Criar uma regra de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
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

2. Crie um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e atribua-lhe a regra de segurança *Allow-RDP-All.* Além da regra *Allow-RDP-All,* o grupo de segurança da rede contém várias regras padrão. Uma regra predefinida desativa todo o acesso à entrada a partir da internet. Uma vez criada, a regra *Allow-RDP-All* é atribuída ao grupo de segurança da rede para que possa ligar-se remotamente ao VM.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Associe o grupo de segurança da rede à sub-rede *mySubnet* com [a Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A regra no grupo de segurança da rede é eficaz para todos os recursos utilizados na sub-rede.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com rede acelerada

1. Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Um endereço IP público é desnecessário se não pretender aceder ao VM a partir da internet. No entanto, é necessário completar os passos deste artigo.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Crie uma interface de rede com [o New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) com rede acelerada ativada e atribua o endereço IP público à interface de rede. O exemplo a seguir cria uma interface de rede chamada *myNic* na sub-rede *mySubnet* da rede virtual *myVnet,* atribuindo-lhe o endereço IP público *myPublicIp:*

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Crie um VM e anexe a interface de rede

1. Descreva as suas credenciais VM para a `$cred` variável utilizando [o Get-Credential,](/powershell/module/microsoft.powershell.security/get-credential)o que lhe leva a iniciar sessão:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Defina o seu VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O seguinte comando define um VM chamado *myVM* com um tamanho VM que suporta networking acelerado *(Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Para obter uma lista de todos os tamanhos e características VM, consulte os [tamanhos do Windows VM](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Crie o resto da sua configuração VM com [o Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [o Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O seguinte comando cria um VM Windows Server 2016:

    ```azurepowershell
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

4. Anexar a interface de rede que criou anteriormente com [Add-AzVMNetworkInterface:](/powershell/module/az.compute/add-azvmnetworkinterface)

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Crie o seu VM com [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Confirme que o controlador Ethernet está instalado no Windows VM

Assim que criar o VM em Azure, ligue-se ao VM e confirme que o controlador Ethernet está instalado no Windows.

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus VMs. Procure e selecione **máquinas Virtuais.**

2. Na lista de máquinas virtuais, escolha o seu novo VM.

3. Na página geral da VM, se o **Estado** do VM estiver listado como **Criação,** aguarde até que o Azure termine de criar o VM. O **Estado** será alterado para **Running** after VM criação está completo.

4. A partir da barra de ferramentas de visão geral VM, selecione **Connect**  >  **RDP**  >  **Download RDP File**.

5. Abra o ficheiro .rdp e, em seguida, inscreva-se no VM com as credenciais que introduziu no [Create a VM e anexe a](#create-a-vm-and-attach-the-network-interface) secção de interface de rede. Se nunca ligou a um Windows VM em Azure, consulte [Connect to virtual machine](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. Depois da sessão de ambiente de trabalho remota para o seu VM aparecer, clique com o botão Iniciar o Windows e escolha **o Gestor de Dispositivos**.

7. Na janela **Do Gestor de Dispositivos,** expanda o nó **dos adaptadores de rede.**

8. Confirme que o **Adaptador Ethernet de Função Virtual Mellanox ConnectX-3** aparece, como mostra a seguinte imagem:

    ![Adaptador Ethernet de função virtual Mellanox ConnectX-3, novo adaptador de rede para networking acelerado, Gestor de Dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

A rede acelerada está agora ativada para o seu VM.

> [!NOTE]
> Se o adaptador Mellanox não arrancar, abra um aviso de administrador na sessão de ambiente de trabalho remoto e introduza o seguinte comando:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Permitir a ligação acelerada em VMs existentes

Se criou um VM sem rede acelerada, pode ativar esta funcionalidade num VM existente. O VM deve apoiar a ligação acelerada em rede cumprindo os seguintes pré-requisitos, que também estão acima descritos:

* O VM deve ser um tamanho suportado para uma rede acelerada.
* O VM deve ser uma imagem suportada da Galeria Azure (e versão kernel para Linux).
* Todos os VMs num conjunto de disponibilidade ou num conjunto de escala de máquina virtual devem ser interrompidos ou translocados antes de permitir uma rede acelerada em qualquer NIC.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>VMs individuais e VMs em um conjunto de disponibilidade

1. Parar ou negociar o VM ou, se um conjunto de disponibilidade, todos os VMs do conjunto:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Quando cria um VM individualmente, sem um conjunto de disponibilidade, só precisa parar ou negociar o VM individual para permitir uma rede acelerada. Se o seu VM foi criado com um conjunto de disponibilidade, deve parar ou negociar todos os VM contidos no conjunto de disponibilidade antes de permitir uma rede acelerada em qualquer um dos NICs, de modo que os VMs acabem num cluster que suporte a rede acelerada. O requisito de stop ou deallocate é desnecessário se desativar a rede acelerada, porque os clusters que suportam a rede acelerada também funcionam bem com NICs que não usam rede acelerada.

2. Ativar a ligação acelerada no NIC do seu VM:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Reinicie o seu VM ou, se num conjunto de disponibilidade, todos os VMs do conjunto e confirme que a rede acelerada está ativada:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Conjuntos de dimensionamento de máquinas virtuais

Um conjunto de escala de máquina virtual é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.

1. Pare os VMs:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Atualizar a propriedade de rede acelerada sob a interface de rede:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Descreva as atualizações aplicadas para automáticas de modo a que as alterações sejam imediatamente captadas:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Um conjunto de escala tem atualizações VM que aplicam atualizações utilizando três configurações diferentes: automática, rolante e manual. Nestas instruções, a política é definida como automática, de modo que a balança definida recolhe as alterações imediatamente após o seu reinício.

4. Reiniciar o conjunto de escala:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Assim que recomeçar, aguarde que as atualizações terminem. Depois de as atualizações serem feitas, a função virtual (VF) aparece dentro do VM. Certifique-se de que está a utilizar um tamanho de SO e VM suportados.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionamento dos VM existentes com rede acelerada

Se um VM tiver ativado a ligação em rede acelerada, só é possível redimensioná-lo para um VM que suporta uma rede acelerada.  

Um VM com rede acelerada ativado não pode ser redimensionado para uma instância VM que não suporta rede acelerada usando a operação de redimensionamento. Em vez disso, para redimensionar um destes VMs:

1. Pare ou transloja o VM. Para um conjunto de disponibilidade ou escala definido, pare ou translote todos os VMs no conjunto de disponibilidade ou escala definido.

2. Desativar a rede acelerada no NIC do VM. Para um conjunto de disponibilidade ou escala definido, desative a rede acelerada nos NICs de todos os VMs no conjunto de disponibilidade ou escala.

3. Depois de desativar a rede acelerada, mova o VM, o conjunto de disponibilidades ou a escala definido para um novo tamanho que não suporta rede acelerada e, em seguida, reinicie-os.
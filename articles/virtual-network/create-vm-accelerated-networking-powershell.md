---
title: Criar Windows VM com rede acelerada - Azure PowerShell
description: Aprenda a criar uma máquina virtual Linux com networking acelerado.
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
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: 202acff5bae87174781dc6c914bebf0494dfcf05
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871448"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Criar um VM Windows com networking acelerado utilizando o Azure PowerShell

Neste tutorial, aprende-se a criar uma máquina virtual do Windows (VM) com networking acelerado.

> [!NOTE]
> Para utilizar uma rede acelerada com uma máquina virtual Linux, consulte [Create a Linux VM com networking acelerado](create-vm-accelerated-networking-cli.md).

A ligação acelerada em rede permite a virtualização de I/O de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este caminho de alto desempenho contorna o hospedeiro a partir da trajetória de dados, o que reduz a latência, o nervosismo e a utilização do CPU para as cargas de trabalho de rede mais exigentes em tipos de VM suportados. O diagrama que se segue ilustra como dois VMs comunicam com e sem ligação acelerada:

![Comunicação entre máquinas virtuais Azure com e sem rede acelerada](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem uma rede acelerada, todo o tráfego em rede dentro e fora do VM deve atravessar o hospedeiro e o interruptor virtual. O interruptor virtual fornece todas as aplicações políticas, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços virtualizados da rede para o tráfego de rede.

> [!NOTE]
> Para saber mais sobre interruptores virtuais, consulte [hyper-V Virtual Switch](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Com uma rede acelerada, o tráfego de rede chega à interface de rede do VM (NIC) e é depois encaminhado para o VM. Todas as políticas de rede que o interruptor virtual aplica são agora descarregadas e aplicadas em hardware. Como a política é aplicada no hardware, o NIC pode encaminhar o tráfego da rede diretamente para o VM. O NIC contorna o hospedeiro e o interruptor virtual, mantendo toda a política aplicada no hospedeiro.

Os benefícios da rede acelerada aplicam-se apenas ao VM em que está ativado. Para obter os melhores resultados, ative esta funcionalidade em pelo menos dois VMs ligados à mesma rede virtual Azure. Ao comunicar através de redes virtuais ou conectar-se no local, esta funcionalidade tem um impacto mínimo na latência geral.

## <a name="benefits"></a>Benefícios

- **Latência inferior / Pacotes mais altos por segundo (pps)**: Eliminar o interruptor virtual da trajetória de dados remove o tempo que os pacotes de tempo gastam no hospedeiro para o processamento de políticas. Também aumenta o número de pacotes que podem ser processados dentro do VM.

- **Nervosismo reduzido**: O processamento de interruptores virtuais depende da quantidade de política que precisa de ser aplicada. Também depende da carga de trabalho da CPU que está a fazer o processamento. Descarregar a aplicação da política para o hardware remove essa variabilidade entregando pacotes diretamente ao VM. O descarregamento também remove a comunicação host-to-VM, todas as interrupções de software e todos os interruptores de contexto.

- **Diminuição da utilização**do CPU : Contornar o interruptor virtual no hospedeiro leva a uma menor utilização de CPU para o tráfego da rede de processamento.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

As seguintes distribuições são suportadas diretamente a partir da Galeria Azure:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Limitações e constrangimentos

### <a name="supported-vm-instances"></a>Casos de VM suportados

A rede acelerada é suportada na maioria dos tamanhos de instância otimizados para a computação com dois ou mais CPUs virtuais (vCPUs).  Estas séries suportadas são: Dv2/DSv2 e F/Fs.

Em casos que suportam hiperthreading, a rede acelerada é suportada em casos vm com quatro ou mais vCPUs. As séries suportadas são: D/Dsv3, D/Dsv4, E/Esv3, Ea/Easv4, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre os casos de VM, consulte [Sizes para máquinas virtuais Windows em Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiões

O networking acelerado está disponível em todas as regiões globais de Azure e Nuvem governamental Azure.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitar a rede acelerada em um VM em execução

Um tamanho VM suportado sem ligação acelerada de rede só pode ter a funcionalidade ativada quando é parada e negociada.

### <a name="deployment-through-azure-resource-manager"></a>Implantação através do Gestor de Recursos Azure

As máquinas virtuais (clássicas) não podem ser implantadas com networking acelerado.

## <a name="vm-creation-using-the-portal"></a>Criação vM usando o portal

Embora este artigo forneça passos para criar um VM com networking acelerado utilizando o Azure PowerShell, também pode [utilizar o portal Azure para criar uma máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que permita uma rede acelerada. Quando criar um VM no portal, na página **Criar uma máquina virtual,** escolha o separador **Networking.** Este separador tem uma opção para **a rede acelerada**. Se escolheu um [sistema operativo suportado](#supported-operating-systems) e [o tamanho vm,](#supported-vm-instances)esta opção é automaticamente definida para **On**. Caso contrário, a opção está definida para **Off**, e Azure mostra a razão pela qual não pode ser ativado.

> [!NOTE]
> Apenas sistemas operativos suportados podem ser ativados através do portal. Se estiver a utilizar uma imagem personalizada e a sua imagem suportar uma rede acelerada, por favor crie o seu VM utilizando CLI ou PowerShell. 

Depois de criar o VM, pode confirmar se a rede acelerada está ativada. Siga estas instruções:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus VMs. Procure e selecione **máquinas Virtuais**.

2. Na lista de máquinas virtuais, escolha o seu novo VM.

3. Na barra de menuS VM, escolha **Networking**.

Na informação da interface da rede, junto à etiqueta de **rede Acelerada,** o portal apresenta **desativado** ou **ativado** para o estado de rede acelerado.

## <a name="vm-creation-using-powershell"></a>Criação vm usando PowerShell

Antes de prosseguir, instale a versão 1.0.0 do [AzurE PowerShell](/powershell/azure/install-az-ps) ou posterior. Para encontrar a versão atualmente `Get-Module -ListAvailable Az`instalada, execute . Se precisar de instalar ou atualizar, instale a versão mais recente do módulo Az a partir da [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). Numa sessão powerShell, inscreva-se numa conta Azure utilizando [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myNic*, e *myVM*.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O seguinte comando cria um grupo de recursos chamado *myResourceGroup* na localização *central:*

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). O seguinte comando cria uma subnet chamada *mySubnet:*

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Crie uma rede virtual com [new-AzVirtualNetwork,](/powershell/module/az.Network/New-azVirtualNetwork)com a subnet *mySubnet.*

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Crie uma regra de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

2. Crie um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e atribua-lhe a regra de segurança *Allow-RDP-All.* Além da regra *Allow-RDP-All,* o grupo de segurança da rede contém várias regras predefinidas. Uma regra predefinida desativa todo o acesso à entrada a partir da internet. Uma vez criado, a regra *Allow-RDP-All* é atribuída ao grupo de segurança da rede para que possa ligar-se remotamente ao VM.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Associe o grupo de segurança da rede à subnet *mySubnet* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A regra no grupo de segurança da rede é eficaz para todos os recursos implantados na sub-rede.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com networking acelerado

1. Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Um endereço IP público é desnecessário se não planeia aceder ao VM a partir da internet. No entanto, é necessário completar os passos deste artigo.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Crie uma interface de rede com [new-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) com rede acelerada ativada e atribua o endereço IP público à interface da rede. O exemplo seguinte cria uma interface de rede chamada *myNic* na subnet *mySubnet* da rede virtual *myVnet,* atribuindo-lhe o endereço IP público *myPublicIp:*

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Criar um VM e anexar a interface de rede

1. Detete as suas `$cred` credenciais VM na variável utilizando [get-credential,](/powershell/module/microsoft.powershell.security/get-credential)o que o leva a iniciar sessão:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Defina o seu VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O seguinte comando define um VM denominado *myVM* com um tamanho VM que suporta a rede acelerada *(Standard_DS4_v2):*

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Para obter uma lista de todos os tamanhos e características vm, consulte os [tamanhos vm do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Crie o resto da configuração VM com [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O seguinte comando cria um VM Windows Server 2016:

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

4. Fixe a interface de rede que criou anteriormente com [add-AzVMNetworkInterface:](/powershell/module/az.compute/add-azvmnetworkinterface)

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Crie o seu VM com [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Confirme que o controlador Ethernet está instalado no Windows VM

Assim que criar o VM em Azure, ligue-se ao VM e confirme que o controlador Ethernet está instalado no Windows.

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus VMs. Procure e selecione **máquinas Virtuais**.

2. Na lista de máquinas virtuais, escolha o seu novo VM.

3. Na página geral do VM, se o **Status** do VM estiver listado como **Criação,** aguarde até que o Azure termine de criar o VM. O **Status** será alterado para **Running** após a criação de VM estar completa.

4. A partir da barra de ferramentas de visão geral VM, selecione **Connect** > **RDP** > **Download RDP File**.

5. Abra o ficheiro .rdp e, em seguida, inscreva-se no VM com as credenciais que inseriu no [Create a VM e fixe a](#create-a-vm-and-attach-the-network-interface) secção de interface de rede. Se nunca esteve ligado a um VM Windows em Azure, consulte [Connect to virtual machine](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. Depois da sessão remota de ambiente de trabalho para o seu VM aparecer, clique no botão Windows Start e escolha **O Gestor do Dispositivo**.

7. Na janela Do Gestor de **Dispositivos,** expanda o nó dos adaptadores da **Rede.**

8. Confirme que aparece o **Adaptador ethernet de Função Virtual Mellanox ConnectX-3,** como mostra a seguinte imagem:

    ![Mellanox ConnectX-3 Adaptador de Função Virtual Ethernet, novo adaptador de rede para ligação acelerada em rede, Gestor de Dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

A rede acelerada está agora ativada para o seu VM.

> [!NOTE]
> Se o adaptador Mellanox não arrancar, abra um pedido de administrador na sessão remota de ambiente de trabalho e introduza o seguinte comando:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Ativar a ligação acelerada em rede nos VMs existentes

Se criou um VM sem ligação automática de rede, poderá ativar esta funcionalidade num VM existente. O VM deve apoiar a ligação acelerada em rede, cumprindo os seguintes pré-requisitos, que também estão acima descritos:

* O VM deve ser um tamanho suportado para uma rede acelerada.
* O VM deve ser uma imagem suportada da Azure Gallery (e versão kernel para Linux).
* Todos os VMs num conjunto de disponibilidade ou um conjunto de escala de máquina virtual devem ser parados ou ser transferidos antes de ativar a rede acelerada em qualquer NIC.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>VMs individuais e VMs em conjunto de disponibilidade

1. Parar ou desalojar o VM ou, se um conjunto de disponibilidade, todos os VMs do conjunto:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Quando cria um VM individualmente, sem um conjunto de disponibilidade, basta parar ou desalojar o VM individual para permitir uma ligação de rede acelerada. Se o seu VM foi criado com um conjunto de disponibilidade, deve parar ou desalojar todos os VMs contidos no conjunto de disponibilidade supérbio antes de permitir uma ligação acelerada em qualquer um dos NICs, de modo a que os VMs acabem num cluster que suporta a rede acelerada. O requisito de paragem ou dedesatribuição é desnecessário se desativar a rede acelerada, porque os clusters que suportam a rede acelerada também funcionam bem com NICs que não usam networking acelerado.

2. Ativar a ligação acelerada de rede no NIC do seu VM:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Reinicie o seu VM ou, se num conjunto de disponibilidade, todos os VMs do conjunto, e confirme que a rede acelerada está ativada:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Conjuntos de dimensionamento de máquinas virtuais

Um conjunto de escala de máquina virtual é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.

1. Parem os VMs:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Atualize a propriedade de rede acelerada sob a interface de rede:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Desloque as atualizações aplicadas para automática, de modo a que as alterações sejam imediatamente captadas:

    ```azurepowershell
    $vmss.UpgradePolicy.AutomaticOSUpgrade = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Um conjunto de escala tem atualizações vM que aplicam atualizações usando três configurações diferentes: automática, rolante e manual. Nestas instruções, a política é definida como automática, pelo que o conjunto de escala saque as alterações imediatamente após o seu reinício.

4. Reiniciar o conjunto de escala:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Assim que reiniciar, aguarde que as atualizações terminem. Após a atualização, a função virtual (VF) aparece dentro do VM. Certifique-se de que está a utilizar um osso e vm suportados.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionar os VMexistentes existentes com networking acelerado

Se um VM tiver acelerado a rede ativada, só é possível redimensioná-lo para um VM que suporta o networking acelerado.  

Um VM com rede acelerada ativado não pode ser redimensionado para uma instância VM que não suporta a ligação acelerada em rede utilizando a operação de redimensionamento. Em vez disso, para redimensionar um destes VMs:

1. Parar ou desalocar o VM. Para um conjunto de disponibilidade ou conjunto de escala, pare ou desaloque todos os VMs no conjunto de disponibilidade ou escala.

2. Desative a ligação acelerada no NIC do VM. Para um conjunto de disponibilidade ou conjunto de escala, desative a ligação acelerada em rede nos NICs de todos os VMs no conjunto de disponibilidade ou na escala.

3. Depois de desativar o networking acelerado, mova o VM, o conjunto de disponibilidade ou a escala definida para um novo tamanho que não suporta o networking acelerado e, em seguida, reinicie-os.

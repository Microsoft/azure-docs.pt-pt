---
title: Criar e gerir VMs windows em Azure que usam vários NICs
description: Aprenda a criar e gerir um VM do Windows que tenha vários NICs ligados a ele utilizando modelos Azure PowerShell ou Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 66a135cd1629aa2befcd4c56d835473791d62ce8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974010"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Criar e gerir uma máquina virtual Windows que tenha vários NICs
As máquinas virtuais (VMs) em Azure podem ter vários cartões de interface de rede virtuais (NICs) ligados a eles. Um cenário comum é ter sub-redes diferentes para a conectividade frontal e traseira. Pode associar vários NICs num VM a várias sub-redes, mas essas sub-redes devem residir todas na mesma rede virtual (vNet). Este artigo detalha como criar um VM que tenha vários NICs ligados a ele. Também aprende a adicionar ou remover NICs de um VM existente. Diferentes [tamanhos VM](../sizes.md) suportam um número variado de NICs, por isso dimensione o seu VM em conformidade.

## <a name="prerequisites"></a>Pré-requisitos

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *myVnet*e *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Criar uma VM com vários NICs
Primeiro, criar um grupo de recursos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *EastUs:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Criar rede virtual e sub-redes
Um cenário comum é que uma rede virtual tenha duas ou mais sub-redes. Uma sub-rede pode ser para o tráfego frontal, a outra para tráfego de back-end. Para ligar a ambas as sub-redes, utilize vários NICs no seu VM.

1. Defina duas sub-redes de rede virtuais com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir define as sub-redes para *mySubnetFrontEnd* e *mySubnetBackEnd:*

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Crie a sua rede virtual e sub-redes com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVnet:*

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Criar vários NICs
Criar dois NICs com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Fixe um NIC à sub-rede frontal e um NIC à sub-rede traseira. O exemplo a seguir cria NICs chamados *myNic1* e *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Normalmente, também se cria um [grupo de segurança](../../virtual-network/network-security-groups-overview.md) de rede para filtrar o tráfego de rede para o VM e um [equilibrador](../../load-balancer/load-balancer-overview.md) de carga para distribuir tráfego por vários VMs.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora comece a construir a sua configuração VM. Cada tamanho VM tem um limite para o número total de NICs que pode adicionar a um VM. Para obter mais informações, consulte [os tamanhos do Windows VM](../sizes.md).

1. Desajuste as suas credenciais VM à `$cred` variável da seguinte forma:

    ```powershell
    $cred = Get-Credential
    ```

2. Defina o seu VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). O exemplo a seguir define um VM chamado *myVM* e utiliza um tamanho VM que suporta mais de dois NICs *(Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Crie o resto da sua configuração VM com [o Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [o Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). O exemplo a seguir cria um VM Windows Server 2016:

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

4. Anexe os dois NICs que criou anteriormente com [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Crie o seu VM com [New-AzVM](/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Adicione as rotas para os NICs secundários ao SO, completando as etapas em [Configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Adicione um NIC a um VM existente
Para adicionar um NIC virtual a um VM existente, você negoceia o VM, adicione o NIC virtual e, em seguida, inicie o VM. Diferentes [tamanhos VM](../sizes.md) suportam um número variado de NICs, por isso dimensione o seu VM em conformidade. Se necessário, pode [redimensionar um VM](resize-vm.md).

1. Translocar o VM com [Stop-AzVM](/powershell/module/az.compute/stop-azvm). O exemplo seguinte é o que a VM nomeou *myVM* no *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente do VM com [Get-AzVm](/powershell/module/az.compute/get-azvm). O exemplo seguinte obtém informações para o vM nomeado *myVM* no *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. O exemplo a seguir cria um NIC virtual com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) chamado *myNic3* que está ligado ao *mySubnetBackEnd*. O NIC virtual é então anexado ao vM nomeado *myVM* no *myResourceGroup* com [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>NICs virtuais primários
    Um dos NICs num VM multi-NIC tem de ser primário. Se um dos NICs virtuais existentes no VM já estiver definido como primário, pode saltar este passo. O exemplo a seguir pressupõe que dois NICs virtuais estão agora presentes num VM e deseja adicionar o primeiro NIC `[0]` () como o principal:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Inicie o VM com [Start-AzVm:](/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Adicione as rotas para os NICs secundários ao SO, completando as etapas em [Configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Remova um NIC de um VM existente
Para remover um NIC virtual de um VM existente, você transcolote o VM, remova o NIC virtual e, em seguida, inicie o VM.

1. Translocar o VM com [Stop-AzVM](/powershell/module/az.compute/stop-azvm). O exemplo seguinte é o que a VM nomeou *myVM* no *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente do VM com [Get-AzVm](/powershell/module/az.compute/get-azvm). O exemplo seguinte obtém informações para o vM nomeado *myVM* no *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Obtenha informações sobre o nic remove com [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface). O exemplo a seguir obtém informações sobre *o myNic3:*

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Remova o NIC com [Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface) e, em seguida, atualize o VM com [Update-AzVm](/powershell/module/az.compute/update-azvm). O exemplo a seguir remove *o myNic3* tal como obtido `$nicId` no passo anterior:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Inicie o VM com [Start-AzVm:](/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Criar vários NICs com modelos
Os modelos do Azure Resource Manager fornecem uma forma de criar múltiplas instâncias de um recurso durante a implementação, tais como a criação de vários NICs. Os modelos do Gestor de Recursos utilizam ficheiros JSON declarativos para definir o seu ambiente. Para mais informações, consulte [a visão geral do Gestor de Recursos Azure.](../../azure-resource-manager/management/overview.md) Pode utilizar *a cópia* para especificar o número de casos para criar:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Para obter mais informações, consulte [a criação de múltiplas instâncias utilizando *a cópia.*](../../azure-resource-manager/templates/copy-resources.md) 

Também pode usar `copyIndex()` para anexar um número a um nome de recurso. Em seguida, pode criar *myNic1,* *MyNic2* e assim por diante. O seguinte código mostra um exemplo de anexação do valor do índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de criação de [vários NICs utilizando modelos de Gestor de Recursos.](../../virtual-network/template-samples.md)

Adicione as rotas para os NICs secundários ao SO, completando as etapas em [Configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o SO convidado para vários NICs

O Azure atribui um portal predefinido à primeira interface de rede (primária) ligada à máquina virtual. O Azure não atribui um gateway predefinido a interfaces de rede (secundárias) adicionais ligadas a uma máquina virtual. Por conseguinte, não pode comunicar com recursos que estejam fora da sub-rede em que se encontre uma interface de rede secundária, por predefinição. As interfaces de rede secundárias podem, no entanto, comunicar com recursos fora da sua sub-rede, embora os passos para permitir a comunicação sejam diferentes para diferentes sistemas operativos.

1. A partir de um pedido de comando do Windows, execute o `route print` comando, que devolve a saída semelhante à seguinte saída para uma máquina virtual com duas interfaces de rede anexas:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Neste exemplo, **o Adaptador de Rede Hiper-V da Microsoft #4** (interface 7) é a interface de rede secundária que não tem um gateway predefinido atribuído à mesma.

2. A partir de um pedido de comando, executar o `ipconfig` comando para ver qual endereço IP é atribuído à interface de rede secundária. Neste exemplo, 192.168.2.4 é atribuído à interface 7. Nenhum endereço de gateway predefinido é devolvido para a interface de rede secundária.

3. Para encaminhar todo o tráfego destinado a endereços fora da sub-rede da interface de rede secundária para o gateway para a sub-rede, executar o seguinte comando:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    O endereço de porta de entrada para a sub-rede é o primeiro endereço IP (terminando em .1) no intervalo de endereços definido para a sub-rede. Se não quiser encaminhar todo o tráfego para fora da sub-rede, pode adicionar rotas individuais a destinos específicos, em vez disso. Por exemplo, se apenas quiser encaminhar o tráfego da interface de rede secundária para a rede 192.168.3.0, introduza o comando:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Para confirmar a comunicação bem sucedida com um recurso na rede 192.168.3.0, por exemplo, insira o seguinte comando para ping 192.168.3.4 utilizando a interface 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Poderá ter de abrir o ICMP através da firewall do Windows do dispositivo que está a pingar com o seguinte comando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Para confirmar que a rota adicionada está na tabela de rotas, insira o `route print` comando, que devolve a saída semelhante ao seguinte texto:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    A rota listada com *192.168.1.1 no* **gateway,** é a rota que existe por padrão para a interface de rede primária. A rota com *192.168.2.1* em **Gateway,** é a rota que adicionou.

## <a name="next-steps"></a>Passos seguintes
Reveja [os tamanhos do Windows VM](../sizes.md) quando estiver a tentar criar um VM que tenha vários NICs. Preste atenção ao número máximo de NICs que cada tamanho VM suporta.
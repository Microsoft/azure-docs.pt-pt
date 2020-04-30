---
title: Crie e gerencie VMs windows em Azure que usam vários NICs
description: Aprenda a criar e gerir um Windows VM que tenha vários NICs ligados ao mesmo utilizando modelos De PowerShell ou Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 6651ae21694022be86d8db08737c609aed3df569
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870261"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Crie e gerencie uma máquina virtual windows que tenha vários NICs
As máquinas virtuais (VMs) em Azure podem ter vários cartões de interface de rede virtual (NICs) ligados a eles. Um cenário comum é ter subredes diferentes para conectividade frontal e traseira. Pode associar vários NICs num VM a várias subredes, mas essas subredes devem residir na mesma rede virtual (vNet). Este artigo detalha como criar um VM que tem vários NICs ligados a ele. Também aprende a adicionar ou remover NICs de um VM existente. Diferentes tamanhos de [VM](sizes.md) suportam um número variado de NICs, de modo que o tamanho do seu VM em conformidade.

## <a name="prerequisites"></a>Pré-requisitos

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myVnet,* e *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Criar uma VM com vários NICs
Primeiro, criar um grupo de recursos. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *eastus:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Criar rede virtual e subredes
Um cenário comum é que uma rede virtual tenha duas ou mais subredes. Uma subnet a ser para o tráfego frontal, a outra para o tráfego de back-end. Para se ligar a ambas as subredes, utilize vários NICs no seu VM.

1. Defina duas subredes de rede virtual com [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo seguinte define as subredes para *mySubnetFrontEnd* e *mySubnetBackEnd:*

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Crie a sua rede virtual e subnets com [new-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual chamada *myVnet:*

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Criar vários NICs
Crie dois NICs com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Fixe um NIC à sub-rede frontal e um NIC à sub-rede traseira. O exemplo seguinte cria NICs *chamados myNic1* e *myNic2:*

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

Normalmente também cria um grupo de segurança de [rede](../../virtual-network/security-overview.md) para filtrar o tráfego da rede para o VM e um [equilibrador](../../load-balancer/load-balancer-overview.md) de carga para distribuir tráfego por vários VMs.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora comece a construir a sua configuração VM. Cada tamanho VM tem um limite para o número total de NICs que pode adicionar a um VM. Para mais informações, consulte os [tamanhos do Windows VM](sizes.md).

1. Delineie as `$cred` suas credenciais VM para a variável da seguinte forma:

    ```powershell
    $cred = Get-Credential
    ```

2. Defina o seu VM com [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). O exemplo que se segue define um VM chamado *myVM* e utiliza um tamanho VM que suporta mais de dois NICs *(Standard_DS3_v2):*

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Crie o resto da configuração VM com [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) e [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). O exemplo seguinte cria um VM Do Windows Server 2016:

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

4. Fixe os dois NICs que criou anteriormente com [add-AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Crie o seu VM com [new-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Adicione rotas para NICs secundários ao Sistema operativo, completando as etapas em [Configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Adicione um NIC a um VM existente
Para adicionar um NIC virtual a um VM existente, você desaloca o VM, adicione o NIC virtual, em seguida, iniciar o VM. Diferentes tamanhos de [VM](sizes.md) suportam um número variado de NICs, de modo que o tamanho do seu VM em conformidade. Se necessário, pode [redimensionar um VM](resize-vm.md).

1. Deslocar o VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). O seguinte exemplo desafeta o VM denominado *myVM* no *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente do VM com [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). O exemplo seguinte obtém informações para o VM nomeado *myVM* no *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. O exemplo seguinte cria um NIC virtual com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) nomeado *myNic3* que está ligado ao *mySubnetBackEnd*. O NIC virtual é então anexado ao VM nomeado *myVM* no *myResourceGroup* com [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

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
    Um dos NICs de um VM multi-NIC precisa ser primário. Se um dos NICs virtuais existentes no VM já estiver definido como primário, pode saltar este passo. O exemplo que se segue pressupõe que dois NICs virtuais estão`[0]`agora presentes num VM e deseja adicionar o primeiro NIC ( ) como o principal:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Inicie o VM com [Start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Adicione rotas para NICs secundários ao Sistema operativo, completando as etapas em [Configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Remova um NIC de um VM existente
Para remover um NIC virtual de um VM existente, você desaloca o VM, remova o NIC virtual e, em seguida, inicie o VM.

1. Deslocar o VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). O seguinte exemplo desafeta o VM denominado *myVM* no *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente do VM com [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). O exemplo seguinte obtém informações para o VM nomeado *myVM* no *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Obtenha informações sobre o remove NIC com [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). O exemplo seguinte obtém informações sobre *o myNic3:*

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Retire o NIC com [remove-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) e, em seguida, atualize o VM com [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). O exemplo seguinte remove *o myNic3* obtido na `$nicId` etapa anterior:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Inicie o VM com [Start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Criar vários NICs com modelos
Os modelos do Gestor de Recursos Azure fornecem uma forma de criar múltiplas instâncias de um recurso durante a implementação, tais como a criação de vários NICs. Os modelos do Gestor de Recursos utilizam ficheiros JSON declarativos para definir o seu ambiente. Para mais informações, consulte a [visão geral do Gestor de Recursos do Azure.](../../azure-resource-manager/management/overview.md) Pode utilizar *cópia* para especificar o número de instâncias para criar:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Para mais informações, consulte a [criação de múltiplos casos utilizando *cópia*](../../resource-group-create-multiple.md). 

Também pode `copyIndex()` usar para anexar um número a um nome de recurso. Pode então criar *o myNic1,* *MyNic2* e assim por diante. O seguinte código mostra um exemplo de adesão ao valor do índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de criação de [múltiplos NICs utilizando modelos](../../virtual-network/template-samples.md)de Gestor de Recursos .

Adicione rotas para NICs secundários ao Sistema operativo, completando as etapas em [Configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configure o os convidado para vários NICs

O Azure atribui uma porta de entrada predefinida à primeira interface de rede (primária) ligada à máquina virtual. O Azure não atribui um gateway predefinido a interfaces de rede (secundárias) adicionais ligadas a uma máquina virtual. Por conseguinte, não pode comunicar com recursos que estejam fora da sub-rede em que se encontre uma interface de rede secundária, por predefinição. As interfaces de rede secundária podem, no entanto, comunicar com recursos fora da sua subnet, embora os passos para permitir a comunicação sejam diferentes para diferentes sistemas operativos.

1. A partir de um `route print` pedido de comando windows, executar o comando, que retorna a saída semelhante à seguinte saída para uma máquina virtual com duas interfaces de rede anexadas:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Neste exemplo, o Adaptador de **Rede Hiper-V da Microsoft #4** (interface 7) é a interface de rede secundária que não tem um gateway predefinido atribuído a ele.

2. A partir de um `ipconfig` pedido de comando, execute o comando para ver qual o endereço IP atribuído à interface de rede secundária. Neste exemplo, 192.168.2.4 é atribuído à interface 7. Não é devolvido nenhum endereço de gateway predefinido para a interface de rede secundária.

3. Para encaminhar todo o tráfego destinado a endereços fora da sub-rede da interface de rede secundária até à porta de entrada da sub-rede, executar o seguinte comando:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    O endereço de porta de entrada da sub-rede é o primeiro endereço IP (terminando em .1) na gama de endereços definida para a sub-rede. Se não quiser desviar todo o tráfego para fora da subnet, pode adicionar rotas individuais a destinos específicos. Por exemplo, se apenas quiser encaminhar o tráfego da interface de rede secundária para a rede 192.168.3.0, introduza o comando:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Para confirmar uma comunicação bem sucedida com um recurso na rede 192.168.3.0, por exemplo, insira o seguinte comando para ping 192.168.3.4 utilizando a interface 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Pode ser necessário abrir o ICMP através da firewall do Windows do dispositivo que está a pingar com o seguinte comando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Para confirmar que a rota adicional está `route print` na tabela de rotas, insira o comando, que devolve a saída semelhante ao seguinte texto:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    A rota listada com *192.168.1.1* no **Gateway,** é a rota que existe por defeito para a interface de rede primária. O percurso com *192.168.2.1* em **Gateway**é o percurso que acrescentou.

## <a name="next-steps"></a>Passos seguintes
Reveja os tamanhos do [Windows VM](sizes.md) quando estiver a tentar criar um VM que tem vários NICs. Preste atenção ao número máximo de NICs que cada tamanho VM suporta. 



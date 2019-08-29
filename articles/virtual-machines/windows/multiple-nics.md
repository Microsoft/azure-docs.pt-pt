---
title: Criar e gerenciar VMs do Windows no Azure que usam várias NICs | Microsoft Docs
description: Saiba como criar e gerenciar uma VM do Windows que tem várias NICs anexadas usando Azure PowerShell ou modelos do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: d10844a52505331418e3bc4e9b36d00a5a7e7b6f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102618"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Criar e gerenciar uma máquina virtual do Windows que tenha várias NICs
As VMs (máquinas virtuais) no Azure podem ter várias NICs (placas de interface de rede) virtuais conectadas a elas. Um cenário comum é ter diferentes sub-redes para conectividade de front-end e back-end. Você pode associar várias NICs em uma VM a várias sub-redes, mas essas sub-redes devem residir na mesma rede virtual (vNet). Este artigo fornece detalhes sobre como criar uma VM que tenha várias NICs anexadas a ela. Você também aprenderá a adicionar ou remover NICs de uma VM existente. Diferentes [tamanhos de VM](sizes.md) dão suporte a um número variável de NICs, portanto, dimensione sua VM adequadamente.

## <a name="prerequisites"></a>Pré-requisitos

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetrode exemplo incluem MyResource, *myVnet*e *myVM*.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-vm-with-multiple-nics"></a>Criar uma VM com vários NICs
Primeiro, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos chamado MyResource Group no local *eastus* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Criar rede virtual e sub-redes
Um cenário comum é que uma rede virtual tenha duas ou mais sub-redes. Uma sub-rede pode ser para o tráfego de front-end, a outra para o tráfego de back-end. Para se conectar a ambas as sub-redes, você usa várias NICs em sua VM.

1. Defina duas sub-redes de rede virtual com [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir define as sub-redes para *mySubnetFrontEnd* e *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Crie sua rede virtual e sub-redes com [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Criar várias NICs
Crie duas NICs com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Anexe uma NIC à sub-rede de front-end e uma NIC à sub-rede de back-end. O exemplo a seguir cria NICs chamadas *myNic1* e *myNic2*:

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

Normalmente, você também cria um [grupo de segurança de rede](../../virtual-network/security-overview.md) para filtrar o tráfego de rede para a VM e um balanceador de [carga](../../load-balancer/load-balancer-overview.md) para distribuir o tráfego entre várias VMS.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora, comece a criar sua configuração de VM. Cada tamanho de VM tem um limite para o número total de NICs que você pode adicionar a uma VM. Para obter mais informações, consulte [tamanhos de VM do Windows](sizes.md).

1. Defina suas credenciais de VM para `$cred` a variável da seguinte maneira:

    ```powershell
    $cred = Get-Credential
    ```

2. Defina sua VM com [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). O exemplo a seguir define uma VM chamada *myVM* e usa um tamanho de VM que dá suporte a mais de duas NICs (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Crie o restante da sua configuração de VM com [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) e [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). O exemplo a seguir cria uma VM do Windows Server 2016:

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

4. Anexe as duas NICs que você criou anteriormente com [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Crie sua VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Adicione rotas para NICs secundárias ao sistema operacional, concluindo as etapas em [Configurar o sistema operacional para várias NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Adicionar uma NIC a uma VM existente
Para adicionar uma NIC virtual a uma VM existente, você Desaloca a VM, adiciona a NIC virtual e, em seguida, inicia a VM. Diferentes [tamanhos de VM](sizes.md) dão suporte a um número variável de NICs, portanto, dimensione sua VM adequadamente. Se necessário, você pode [redimensionar uma VM](resize-vm.md).

1. Desaloque a VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). O exemplo a seguir Desaloca a VM chamada *myVM* no *MyResource*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente da VM com [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). O exemplo a seguir obtém informações para a VM chamada *myVM* no MyResource:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. O exemplo a seguir cria uma NIC virtual com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) chamada *myNic3* que está anexada a *mySubnetBackEnd*. Em seguida, a NIC virtual é anexada à VM chamada *myVM* em MyResource filecom [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

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

    ### <a name="primary-virtual-nics"></a>NICs virtuais primárias
    Uma das NICs em uma VM com várias NICs precisa ser primária. Se uma das NICs virtuais existentes na VM já estiver definida como primária, você poderá ignorar esta etapa. O exemplo a seguir pressupõe que duas NICs virtuais agora estão presentes em uma VM e você deseja adicionar a primeira NIC (`[0]`) como a primária:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Inicie a VM com [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Adicione rotas para NICs secundárias ao sistema operacional, concluindo as etapas em [Configurar o sistema operacional para várias NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Remover uma NIC de uma VM existente
Para remover uma NIC virtual de uma VM existente, Desaloque a VM, remova a NIC virtual e, em seguida, inicie a VM.

1. Desaloque a VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). O exemplo a seguir Desaloca a VM chamada *myVM* no *MyResource*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente da VM com [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). O exemplo a seguir obtém informações para a VM chamada *myVM* no MyResource:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Obtenha informações sobre a remoção da NIC com [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). O exemplo a seguir obtém informações sobre *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Remova a NIC com [Remove-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) e, em seguida, atualize a VM com [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). O exemplo a seguir remove *myNic3* conforme obtido `$nicId` pela na etapa anterior:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Inicie a VM com [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Criar várias NICs com modelos
Os modelos de Azure Resource Manager fornecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Os modelos do Resource Manager usam arquivos JSON declarativos para definir seu ambiente. Para obter mais informações, consulte [visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Você pode usar *copiar* para especificar o número de instâncias a serem criadas:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Para obter mais informações, consulte [criando várias instâncias usando *copiar*](../../resource-group-create-multiple.md). 

Você também pode usar `copyIndex()` para acrescentar um número a um nome de recurso. Em seguida, você pode criar *myNic1*, *MyNic2* e assim por diante. O código a seguir mostra um exemplo de acréscimo do valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo de como [criar várias NICs usando modelos do Resource Manager](../../virtual-network/template-samples.md).

Adicione rotas para NICs secundárias ao sistema operacional, concluindo as etapas em [Configurar o sistema operacional para várias NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o sistema operacional convidado para várias NICs

O Azure atribui um gateway padrão para a primeira interface de rede (primária) anexada à máquina virtual. O Azure não atribui um gateway predefinido a interfaces de rede (secundárias) adicionais ligadas a uma máquina virtual. Por conseguinte, não pode comunicar com recursos que estejam fora da sub-rede em que se encontre uma interface de rede secundária, por predefinição. As interfaces de rede secundárias podem, no entanto, se comunicar com recursos fora de sua sub-rede, embora as etapas para habilitar a comunicação sejam diferentes para sistemas operacionais diferentes.

1. Em um prompt de comando do Windows, `route print` execute o comando, que retorna uma saída semelhante à seguinte saída para uma máquina virtual com duas interfaces de rede anexadas:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Neste exemplo, **Microsoft Hyper-V adaptador de rede #4** (interface 7) é a interface de rede secundária que não tem um gateway padrão atribuído a ele.

2. Em um prompt de comando, execute `ipconfig` o comando para ver qual endereço IP é atribuído à interface de rede secundária. Neste exemplo, 192.168.2.4 é atribuído à interface 7. Nenhum endereço de gateway padrão é retornado para a interface de rede secundária.

3. Para rotear todo o tráfego destinado a endereços fora da sub-rede da interface de rede secundária para o gateway para a sub-rede, execute o seguinte comando:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    O endereço do gateway para a sub-rede é o primeiro endereço IP (terminando em 0,1) no intervalo de endereços definido para a sub-rede. Se você não quiser rotear todo o tráfego fora da sub-rede, poderá adicionar rotas individuais a destinos específicos, em vez disso. Por exemplo, se você quisesse apenas rotear o tráfego da interface de rede secundária para a rede 192.168.3.0, insira o comando:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Para confirmar a comunicação bem-sucedida com um recurso na rede 192.168.3.0, por exemplo, digite o seguinte comando para executar ping 192.168.3.4 usando a interface 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Talvez seja necessário abrir o ICMP por meio do firewall do Windows do dispositivo que você está executando ping com o seguinte comando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Para confirmar se a rota adicionada está na tabela de rotas, insira `route print` o comando, que retorna uma saída semelhante ao seguinte texto:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    A rota listada com *192.168.1.1* em **Gateway**é a rota que está lá por padrão para a interface de rede primária. A rota com *192.168.2.1* em **Gateway**é a rota que você adicionou.

## <a name="next-steps"></a>Passos seguintes
Examine os [tamanhos de VM do Windows](sizes.md) quando você estiver tentando criar uma VM com várias NICs. Preste atenção ao número máximo de NICs com suporte de cada VM. 



---
title: Criar e gerir VMs do Windows no Azure que utilizar vários NICs | Documentos da Microsoft
description: Saiba como criar e gerir uma VM do Windows com múltiplos NICs ligados ao mesmo com os modelos do Azure PowerShell ou do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: a89d77e47f8a7ffd7072e8f93c19ec6266f261b3
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720160"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Criar e gerir uma máquina de virtual do Windows que tenha vários NICs
Máquinas virtuais (VMs) no Azure pode ter placas de interface de vários rede virtual (NIC) anexadas aos mesmos. Um cenário comum é ter diferentes sub-redes para a conectividade de front-end e back-end. Pode associar várias NICs numa VM em várias sub-redes, mas essas sub-redes devem residir na mesma rede virtual (vNet). Este artigo fornece detalhes sobre como criar uma VM com várias NICs ligados ao mesmo. Também irá aprender a adicionar ou remover NICs a partir de uma VM existente. Diferentes [tamanhos de VM](sizes.md) suporta um número variável de NICs, então, da mesma forma a dimensionar sua VM.

## <a name="prerequisites"></a>Pré-requisitos

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem *myResourceGroup*, *myVnet*, e *myVM*.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-vm-with-multiple-nics"></a>Criar uma VM com vários NICs
Primeiro, crie um grupo de recursos. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* no *EastUs* localização:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Criar rede virtual e sub-redes
Um cenário comum é para uma rede virtual para ter duas ou mais sub-redes. Uma sub-rede pode ser para o tráfego de front-end, a outra para o tráfego de back-end. Para ligar a ambas as sub-redes, utilize, em seguida, várias NICs na sua VM.

1. Definir duas sub-redes da rede virtual com [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo seguinte define as sub-redes para *mySubnetFrontEnd* e *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Criar a sua rede virtual e sub-redes com [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual denominada *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Criar vários NICs
Criar dois NICs com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Anexe uma NIC na sub-rede do front-end e uma NIC na sub-rede de back-end. O exemplo seguinte cria com o nome de NICs *myNic1* e *myNic2*:

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

Normalmente, também cria um [grupo de segurança de rede](../../virtual-network/security-overview.md) para filtrar o tráfego de rede para a VM e um [Balanceador de carga](../../load-balancer/load-balancer-overview.md) para distribuir o tráfego por várias VMs.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora começa a criar a sua configuração de VM. Cada tamanho de VM tem um limite para o número total de NICs que podem ser adicionados a uma VM. Para obter mais informações, consulte [tamanhos de VM do Windows](sizes.md).

1. Definir as credenciais da sua VM o `$cred` variável da seguinte forma:

    ```powershell
    $cred = Get-Credential
    ```

2. Definir a sua VM com [novo AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). O exemplo seguinte define uma VM com o nome *myVM* e utiliza um tamanho VM que suporte a mais de duas NICs (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Criar o resto da sua configuração de VM com [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) e [conjunto AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). O exemplo seguinte cria uma VM do Windows Server 2016:

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

4. Ligar as duas NICs que criou anteriormente com [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Criar a sua VM com [novo-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Adicionar rotas para NICs secundários para o sistema operacional, concluindo os passos em [configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Adicionar uma NIC a uma VM existente
Para adicionar uma NIC virtual a uma VM existente, deve desalocar a VM, adicione a NIC virtual, em seguida, inicie a VM. Diferentes [tamanhos de VM](sizes.md) suporta um número variável de NICs, então, da mesma forma a dimensionar sua VM. Se for necessário, pode [redimensionar uma VM](resize-vm.md).

1. Desaloque a VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). O exemplo seguinte desaloca a VM com o nome *myVM* na *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obter a configuração existente da VM com [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). O exemplo seguinte obtém informações para a VM com o nome *myVM* na *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. O exemplo seguinte cria uma NIC virtual com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) com o nome *myNic3* que está ligado à *mySubnetBackEnd*. A NIC virtual, em seguida, é anexada à VM com o nome *myVM* na *myResourceGroup* com [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

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

    ### <a name="primary-virtual-nics"></a>NICs virtuais principais
    Um dos NICs numa VM multi-NIC tem de ser principal. Se um dos NICs virtuais existentes na VM já está definido como principal, pode ignorar este passo. O exemplo seguinte assume que dois NICs virtuais agora estão presentes numa VM e que pretende adicionar a primeira NIC (`[0]`) como principal:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Iniciar a VM com [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Adicionar rotas para NICs secundários para o sistema operacional, concluindo os passos em [configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Remover um NIC a uma VM existente
Para remover uma NIC virtual a partir de uma VM existente, desalocar a VM, remover a NIC virtual, em seguida, inicia a VM.

1. Desaloque a VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). O exemplo seguinte desaloca a VM com o nome *myVM* na *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obter a configuração existente da VM com [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). O exemplo seguinte obtém informações para a VM com o nome *myVM* na *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Obter informações sobre a remoção NIC com [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). O exemplo seguinte obtém informações *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Remover a NIC com o [Remove-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) e, em seguida, atualize a VM com [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). O exemplo seguinte remove *myNic3* tal como obtidas pelo `$nicId` no passo anterior:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Iniciar a VM com [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Criar vários NICs com modelos
Modelos Azure Resource Manager fornecem uma forma de criar várias instâncias de um recurso durante a implementação, como a criação de várias NICs. Modelos do Resource Manager utilizam ficheiros JSON declarativos para definir o seu ambiente. Para obter mais informações, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Pode usar *cópia* para especificar o número de instâncias para criar:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Para obter mais informações, consulte [criação de várias instâncias usando *cópia*](../../resource-group-create-multiple.md). 

Também pode utilizar `copyIndex()` para anexar um número para um nome de recurso. Em seguida, pode criar *myNic1*, *MyNic2* e assim por diante. O código seguinte mostra um exemplo de acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de [criação de vários NICs com modelos do Resource Manager](../../virtual-network/template-samples.md).

Adicionar rotas para NICs secundários para o sistema operacional, concluindo os passos em [configurar o sistema operativo para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o sistema operacional convidado para vários NICs

O Azure atribui um gateway predefinido para a primeira interface de rede (principal) ligado à máquina virtual. O Azure não atribui um gateway predefinido a interfaces de rede (secundárias) adicionais ligadas a uma máquina virtual. Por conseguinte, não pode comunicar com recursos que estejam fora da sub-rede em que se encontre uma interface de rede secundária, por predefinição. Interfaces de rede secundárias podem, no entanto, comunicar com os recursos fora da sua sub-rede, embora os passos para ativar a comunicação são diferentes para sistemas operativos diferentes.

1. Num prompt de comando do Windows, execute o `route print` comando, que devolve resultados semelhantes ao seguinte de saída para uma máquina virtual com duas interfaces de rede anexada:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Neste exemplo, **4 de n. º do adaptador de rede do Microsoft Hyper-V** (interface 7) é a interface de rede secundárias que não tem um gateway predefinido atribuído a ele.

2. A partir de uma linha de comandos, execute o `ipconfig` comando para ver qual o endereço IP é atribuído à interface de rede secundárias. Neste exemplo, 192.168.2.4 é atribuído à interface 7. Nenhum endereço de gateway predefinido é retornado para a interface de rede secundárias.

3. Para encaminhar todo o tráfego destinado aos endereços fora da sub-rede da interface de rede secundário para o gateway para a sub-rede, execute o seguinte comando:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    O endereço de gateway para a sub-rede é o primeiro endereço IP (que termine em.1) no intervalo de endereços definido para a sub-rede. Se não quiser encaminhar todo o tráfego fora da sub-rede, poderia adicionar rotas individuais para determinados destinos, em vez disso. Por exemplo, se só quiser encaminhar o tráfego a partir da interface de rede secundárias para a 192.168.3.0 rede, introduza o comando:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Para confirmar a comunicação bem-sucedida com um recurso no 192.168.3.0, rede, por exemplo, introduza o seguinte comando para efetuar o ping 192.168.3.4 usando a interface 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Poderá ter de abrir o ICMP através da firewall do Windows do dispositivo que estiver ping com o seguinte comando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Para confirmar a rota adicionada está na tabela de rotas, introduza o `route print` comando, que devolve resultados semelhantes ao seguinte texto:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    A rota listada com *192.168.1.1* sob **Gateway**, é a rota que é um padrão para a interface de rede principal. A rota com *192.168.2.1* sob **Gateway**, é a rota que adicionou.

## <a name="next-steps"></a>Passos Seguintes
Revisão [tamanhos de VM do Windows](sizes.md) quando está a tentar criar uma VM com várias NICs. Preste atenção para o número máximo de NICs que oferece suporte a cada tamanho de VM. 



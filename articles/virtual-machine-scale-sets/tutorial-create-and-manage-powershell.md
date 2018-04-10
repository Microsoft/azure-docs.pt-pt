---
title: Tutorial - Criar e gerir um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como utilizar o Azure PowerShell para criar um conjunto de dimensionamento de máquinas virtuais e fazer algumas tarefas de gestão comuns, como iniciar e parar uma instância ou alterar a capacidade do conjunto de dimensionamento.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 54f63ec4cddf64110eadf25fff60167238f9f9a6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2018
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Criar e gerir um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell
Um conjunto de dimensionamento de máquinas virtuais permite implementar e gerir um conjunto de máquinas virtuais idênticas e de dimensionamento automático. Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e ligar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos de instância de VM específicos
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas de gestão comuns de conjuntos de dimensionamento

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 5.6.0 ou posterior do módulo do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure. 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de um conjunto de dimensionamento de máquinas virtuais. Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Neste exemplo, é criado um grupo de recursos chamado *myResourceGroup* na região *EastUS*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
O nome do grupo de recursos é especificado quando cria ou modifica um conjunto de dimensionamento neste tutorial.


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Primeiro, defina um nome de utilizador e uma palavra-passe para as instâncias da VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Para distribuir o tráfego para instâncias de VM individuais, é também criado um balanceador de carga. O balanceador de carga inclui regras para distribuir tráfego na porta TCP 80, bem como permitir o tráfego de ambiente de trabalho remoto na porta TCP 3389 e a comunicação remota do PowerShell na porta TCP 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

A criação e configuração de todas as instâncias de VM e recursos do conjunto de dimensionamento demora alguns minutos.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Ver as instâncias da VM num conjunto de dimensionamento
Para ver uma lista das instâncias da VM num conjunto de dimensionamento, utilize [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) da seguinte forma:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

O seguinte resultado de exemplo mostra duas instâncias da VM no conjunto de dimensionamento:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Para ver informações adicionais sobre uma instância específica da VM, adicione o parâmetro `-InstanceId` a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). O seguinte exemplo mostra informações sobre a instância da VM *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Listar informações de ligação
Um endereço IP público é atribuído ao balanceador de carga que encaminha tráfego para as instâncias de VM individuais. Por predefinição, as regras de Tradução de Endereços de Rede (NAT) são adicionadas ao balanceador de carga do Azure que encaminha o tráfego de ligação remota para cada VM numa determinada porta. Para ligar às instâncias de VM remotas num conjunto de dimensionamento, crie uma ligação remota a um número de porta e endereço IP público atribuído.

Para listar as portas NAT para ligar a instâncias da VM num conjunto de dimensionamento, obtenha primeiro o objeto do balanceador de carga com [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Em seguida, veja as regras NAT de entrada com [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

O seguinte resultado de exemplo mostra o nome de instância, o endereço IP público do balanceador de carga e o número de porta para o qual as regras NAT encaminham o tráfego:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

O *Nome* da regra está alinhado com o nome da instância da VM, conforme mostrado num comando [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) anterior. Por exemplo, para ligar à instância da VM *0*, utilize *myScaleSet3389.0* e ligue à porta *50001*. Para ligar à instância da VM *1*, utilize o valor de *myScaleSet3389.1* e ligue à porta *50002*. Para utilizar o PowerShell remoto, ligue-se à regra da instância da VM adequada para a porta *TCP* *5985*.

Veja o endereço IP público do balanceador de carga com [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Exemplo de saída:

```powershell
IpAddress
---------
52.168.121.216
```

Crie uma ligação remota à primeira instância de VM. Especifique o seu endereço IP público e número da porta da instância de VM necessária, conforme mostrado nos comandos anteriores. Quando lhe for pedido, introduza as credenciais que utilizou quando criou o conjunto de dimensionamento (por predefinição, nos comandos de exemplo, *azureuser* e *P@ssw0rd!*). Se utiliza o Azure Cloud Shell, efetue este passo a partir de um pedido local do Power Shell ou do Cliente de Ambiente de Trabalho Remoto. O exemplo seguinte liga à instância da VM *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Após iniciar sessão na instância da VM, pode fazer algumas alterações de configuração manuais, conforme necessário. Por agora, feche a ligação remota.


## <a name="understand-vm-instance-images"></a>Compreender as imagens de instâncias de VM
Quando definiu uma configuração de conjunto de dimensionamento com [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) num passo anterior, utilizou uma imagem do Windows Server 2016 Datacenter. O Azure Marketplace inclui muitas imagens que podem ser utilizadas para criar as instâncias de VM. Para ver uma lista dos editores disponíveis utilize o comando [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher).

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Para ver uma lista das imagens de um determinado editor, utilize [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). Também é possível filtrar a lista de imagens por `-PublisherName` ou `–Offer`. No exemplo seguinte, a lista está filtrada para todas as imagens com o nome de editor *MicrosoftWindowsServer* e uma oferta que corresponde ao *WindowsServer*:

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

O resultado de exemplo seguinte mostra todas as imagens do Windows Server disponíveis:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Quando criou um conjunto de dimensionamento no início do tutorial, foi fornecida uma imagem de VM predefinida do *Windows Server 2016 DataCenter* para as instâncias de VMs. Pode especificar uma imagem de VM diferente com base no resultado de [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). O exemplo seguinte criaria um conjunto de dimensionamento com o parâmetro `-ImageName` para especificar uma imagem de VM de *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Uma vez que a criação e configuração de todas as instâncias de VMs e recursos do conjunto de dimensionamento demora alguns minutos, não precisa de implementar o seguinte conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicy "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>Compreender os tamanhos de instâncias de VM
Um tamanho de instância de VM, ou *SKU*, determina a quantidade de recursos de computação, como a CPU, GPU e memória que ficam disponíveis para a instância de VM. As instâncias de VMs num conjunto de dimensionamento precisam de ter o tamanho adequado para a carga de trabalho esperada.

### <a name="vm-instance-sizes"></a>Tamanhos de instância de VM
A tabela seguinte categoriza tamanhos de VM comuns em casos de utilização.

| Tipo                     | Tamanhos comuns           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU-para-memória equilibrada. Ideal para desenvolvimento/teste e aplicações e soluções de dados pequenas a médias.  |
| [Com otimização de computação](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | CPU-para-memória elevada. É adequado para aplicações de tráfego médio, dispositivos de rede e processos em lote.        |
| [Com otimização de memória](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Memória-para-núcleo elevada. É ideal para bases de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](../virtual-machines/windows/sizes-storage.md)      | Ls                | Débito e E/S de disco elevados. Ideal para bases de dados de Macrodados, SQL e NoSQL.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | VMs especializadas destinadas a composição gráfica e edição de vídeo exigentes.       |
| [Elevado desempenho](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | As nossas mais poderosas VMs com CPU, com interfaces de rede de alto débito (RDMA) opcionais. 

### <a name="find-available-vm-instance-sizes"></a>Localizar tamanhos de instâncias de VM disponíveis
Para ver uma lista de tamanhos de instâncias de VM disponíveis numa região específica, utilize o comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). 

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

O resultado será semelhante ao seguinte exemplo condensado, que mostra os recursos atribuídos a cada tamanho de VM:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Quando criou um conjunto de dimensionamento no início do tutorial, foi fornecido o SKU de VM predefinido *Standard_DS1_v2* para as instâncias das VMs. Pode especificar um tamanho de instância de VM diferente com base no resultado de [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). O seguinte exemplo criaria um conjunto de dimensionamento com o parâmetro `-VmSize` para especificar um tamanho de instância de VM de *Standard_F1*. Uma vez que a criação e configuração de todas as instâncias de VMs e recursos do conjunto de dimensionamento demora alguns minutos, não precisa de implementar o seguinte conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicy "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Quando criou um conjunto de dimensionamento, pediu duas instâncias de VM. Para aumentar ou diminuir o número de instâncias de VM no conjunto de dimensionamento, pode alterar a capacidade manualmente. O conjunto de dimensionamento cria ou remove o número necessário de instâncias de VM e, em seguida, configura o balanceador de carga de forma a distribuir tráfego.

Em primeiro lugar, crie um objeto de conjunto de dimensionamento com [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) e especifique um valor novo para `sku.capacity`. Para aplicar a alteração de capacidade, utilize [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). O exemplo seguinte define o número de instâncias de VMs no seu conjunto de dimensionamento como *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Para ver o número de instâncias que tem agora no conjunto de dimensionamento, utilize [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

O seguinte resultado de exemplo mostra que a capacidade do conjunto de dimensionamento é agora *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Tarefas comuns de gestão
Agora, pode criar um conjunto de dimensionamento, listar as informações de ligação e ligar-se a instâncias de VMs. Aprendeu como utilizar uma imagem de SO diferente para as suas instâncias de VM, selecionar um tamanho de VM diferente ou dimensionar manualmente o número de instâncias. Como parte da sua gestão quotidiana, poderá precisar de parar, iniciar ou reiniciar as instâncias de VMs no seu conjunto de dimensionamento.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Parar e desalocar instâncias de VMs num conjunto de dimensionamento
Para parar uma ou mais VMs num conjunto de dimensionamento, utilize [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. O seguinte exemplo para a instância *1*:

```azurepowershell-interactive
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Por predefinição, as VMs paradas são desalocadas e não implicam custos de computação. Se quiser que a VM permaneça no estado aprovisionado quando for parada, adicione o parâmetro `-StayProvisioned` ao comando anterior. As VMs paradas que continuem aprovisionadas incorrem em custos de computação regulares.

### <a name="start-vm-instances-in-a-scale-set"></a>Iniciar instâncias de VMs num conjunto de dimensionamento
Para iniciar uma ou mais VMs num conjunto de dimensionamento, utilize [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. O seguinte exemplo inicia a instância *1*:

```azurepowershell-interactive
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Reiniciar instâncias de VMs num conjunto de dimensionamento
Para reiniciar uma ou mais VMs num conjunto de dimensionamento, utilize [Retart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. O seguinte exemplo reinicia a instância *1*:

```azurepowershell-interactive
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Limpar recursos
Quando eliminar um grupo de recursos, todos os recursos nele contidos, como as instâncias de VMs, a rede virtual e os discos, também são eliminados. O parâmetro `-Force` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo. O parâmetro `-AsJob` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a realizar algumas tarefas básicas de criação e gestão de conjuntos de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Criar e ligar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos específicos de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas de gestão comuns de conjuntos de dimensionamento

Avance para o próximo tutorial para saber mais sobre os discos de conjuntos de dimensionamento.

> [!div class="nextstepaction"]
> [Utilizar discos de dados com conjuntos de dimensionamento](tutorial-use-disks-powershell.md)

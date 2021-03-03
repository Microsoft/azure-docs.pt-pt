---
title: Implementar anfitriões dedicados do Azure usando o Azure PowerShell
description: Implemente VMs para anfitriões dedicados utilizando a Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 4517c939e14f3157db606f087ef042c1ba7eba3e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672266"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Implementar VMs para anfitriões dedicados usando o Azure PowerShell

Este artigo guia-o através da forma de criar um [anfitrião dedicado](../dedicated-hosts.md) a Azure para hospedar as suas máquinas virtuais (VMs). 

Certifique-se de que instalou a versão 2.8.0 ou posterior do Azure PowerShell e que está a fazer o seu sedumento numa conta Azure com `Connect-AzAccount` . 

## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em anfitriões dedicados.
- Os tamanhos e tipos de hardware disponíveis para anfitriões dedicados variam por região. Consulte a [página de preços do](https://aka.ms/ADHPricing) anfitrião para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de anfitriões

Um **grupo anfitrião** é um recurso que representa uma coleção de anfitriões dedicados. Você cria um grupo de anfitriões em uma região e uma zona de disponibilidade, e adiciona anfitriões a ele. Ao planear uma elevada disponibilidade, existem opções adicionais. Pode utilizar uma ou ambas as seguintes opções com os seus anfitriões dedicados: 
- Abrangendo várias zonas de disponibilidade. Neste caso, você é obrigado a ter um grupo de anfitriões em cada uma das zonas que você deseja usar.
- Abrangem vários domínios de avaria que são mapeados para prateleiras físicas. 
 
Em qualquer dos casos, é necessário fornecer a contagem de domínio de avaria para o seu grupo anfitrião. Se não pretender abranger os domínios de avaria no seu grupo, utilize uma contagem de domínio de avaria de 1. 

Também pode decidir utilizar as zonas de disponibilidade e os domínios de avaria. Este exemplo cria um grupo anfitrião na zona 1, com 2 domínios de avaria. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Adicione o `-SupportAutomaticPlacement true` parâmetro para ter os seus VMs e séries de instâncias colocadas automaticamente nos anfitriões, dentro de um grupo anfitrião. Para obter mais informações, consulte [Manual vs. colocação automática ](../dedicated-hosts.md#manual-vs-automatic-placement).


## <a name="create-a-host"></a>Criar um anfitrião

Agora vamos criar um anfitrião dedicado no grupo anfitrião. Além de um nome para o anfitrião, você é obrigado a fornecer o SKU para o anfitrião. Host SKU captura a série VM suportada, bem como a geração de hardware para o seu anfitrião dedicado.

Para obter mais informações sobre os SKUs e preços do anfitrião, consulte [os preços do Anfitrião Dedicado Azure](https://aka.ms/ADHPricing).

Se definir uma contagem de domínio de avaria para o seu grupo anfitrião, será solicitado que especifique o domínio de avaria para o seu anfitrião. Neste exemplo, definimos o domínio de falha para o anfitrião para 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Criar uma VM

Crie uma máquina virtual no anfitrião dedicado. 

Se especificou uma zona de disponibilidade ao criar o seu grupo anfitrião, é-lhe exigido que utilize a mesma zona ao criar a máquina virtual. Para este exemplo, como o nosso grupo anfitrião está na zona 1, precisamos de criar o VM na zona 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Se criar uma máquina virtual num hospedeiro que não tenha recursos suficientes, a máquina virtual será criada num estado FALHADO. 

## <a name="check-the-status-of-the-host"></a>Verifique o estado do anfitrião

Pode verificar o estado de saúde do anfitrião e quantas máquinas virtuais ainda pode implementar no hospedeiro usando o [GetAzHost](/powershell/module/az.compute/get-azhost) com o `-InstanceView` parâmetro.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

A saída será semelhante a esta:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento 

Quando implementar um conjunto de escala, especifique o grupo anfitrião.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Se quiser escolher manualmente qual o anfitrião para implantar a escala definida, adicione `--host` e o nome do anfitrião.



## <a name="add-an-existing-vm"></a>Adicione um VM existente 

Pode adicionar um VM existente a um anfitrião dedicado, mas o VM tem primeiro de ser stop\Deallocated. Antes de mover um VM para um anfitrião dedicado, certifique-se de que a configuração VM é suportada:

- O tamanho VM deve ser do mesmo tamanho da família do hospedeiro dedicado. Por exemplo, se o seu anfitrião dedicado for DSv3, então o tamanho VM pode ser Standard_D4s_v3, mas não pode ser um Standard_A4_v2. 
- O VM precisa de ser localizado na mesma região que o hospedeiro dedicado.
- O VM não pode fazer parte de um grupo de colocação de proximidade. Retire o VM do grupo de colocação de proximidade antes de o mover para um hospedeiro dedicado. Para obter mais informações, consulte [mover um VM de um grupo de colocação de proximidade](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- O VM não pode estar num conjunto de disponibilidade.
- Se o VM estiver numa zona de disponibilidade, deve ser a mesma zona de disponibilidade que o grupo anfitrião. As definições da zona de disponibilidade para o VM e o grupo anfitrião devem corresponder.

Substitua os valores das variáveis pela sua própria informação.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Limpeza

Está a ser cobrado pelos seus anfitriões dedicados, mesmo quando não são implantadas máquinas virtuais. Deve eliminar quaisquer anfitriões que não esteja a utilizar para economizar custos.  

Só é possível eliminar um hospedeiro quando já não existem máquinas virtuais a usá-lo. Eliminar os VMs utilizando [remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Depois de eliminar os VMs, pode eliminar o anfitrião utilizando [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Uma vez eliminado todos os seus anfitriões, poderá eliminar o grupo anfitrião utilizando o [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Também pode eliminar todo o grupo de recursos num único comando utilizando [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Isto eliminará todos os recursos criados no grupo, incluindo todos os VMs, anfitriões e grupos anfitriões.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Passos seguintes

- Existe o modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa ambas as zonas e domínios de avaria para máxima resiliência numa região.

- Também pode implementar anfitriões dedicados utilizando o [portal Azure.](../dedicated-hosts-portal.md)
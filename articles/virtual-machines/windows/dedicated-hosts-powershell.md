---
title: Implementar anfitriões dedicados azure usando o Azure PowerShell
description: Implemente VMs para anfitriões dedicados usando o Azure PowerShell.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 5cd82635f3aec2cca251e122aadf96f70d377c8a
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190525"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Implementar VMs para anfitriões dedicados usando o Azure PowerShell

Este artigo guia-o através de como criar um [anfitrião dedicado](dedicated-hosts.md) azure para hospedar as suas máquinas virtuais (VMs). 

Certifique-se de que instalou a versão 2.8.0 do Azure PowerShell ou mais tarde, e está inscrito numa conta Azure com `Connect-AzAccount`. 

## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são atualmente suportados em anfitriões dedicados.
- As seguintes séries VM são suportadas: DSv3, ESv3 e FSv2. 

## <a name="create-a-host-group"></a>Criar um grupo de anfitriões

Um **grupo de anfitriões** é um recurso que representa uma coleção de anfitriões dedicados. Cria-se um grupo de acolhimento numa região e numa zona de disponibilidade e adiciona-lhe anfitriões. Ao planear uma alta disponibilidade, existem opções adicionais. Pode utilizar uma ou ambas as seguintes opções com os seus anfitriões dedicados: 
- Atravesse várias zonas de disponibilidade. Neste caso, é-lhe exigido que tenha um grupo de acolhimento em cada uma das zonas que deseja utilizar.
- Atravesse vários domínios de falha que são mapeados em prateleiras físicas. 
 
Em qualquer dos casos, é necessário fornecer a contagem de domínio de avaria para o seu grupo anfitrião. Se não quiser abranger domínios de avaria no seu grupo, utilize uma contagem de domínio de falha de 1. 

Também pode decidir utilizar ambas as zonas de disponibilidade e domínios de avaria. Este exemplo cria um grupo de anfitriões na zona 1, com 2 domínios de falha. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Criar um hospedeiro

Agora vamos criar um anfitrião dedicado no grupo anfitrião. Além de um nome para o anfitrião, é necessário fornecer o SKU para o anfitrião. Host SKU captura a série VM suportada, bem como a geração de hardware para o seu anfitrião dedicado.


Para obter mais informações sobre o anfitrião SKUs e preços, consulte [o preço do Anfitrião Dedicado Azure.](https://aka.ms/ADHPricing)

Se definir uma contagem de domínio de avaria para o seu grupo anfitrião, será-lhe pedido que especifique o domínio de avaria para o seu anfitrião. Neste exemplo, definimos o domínio de avaria para o anfitrião para 1.


```powershell
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Criar uma VM

Crie uma máquina virtual no hospedeiro dedicado. 

Se especificou uma zona de disponibilidade ao criar o seu grupo de anfitriões, é-lhe exigido que utilize a mesma zona ao criar a máquina virtual. Para este exemplo, como o nosso grupo anfitrião está na zona 1, precisamos criar o VM na zona 1.  


```powershell
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

Pode verificar o estado de saúde do hospedeiro e quantas máquinas virtuais ainda pode implementar para o hospedeiro utilizando o [GetAzHost](/powershell/module/az.compute/get-azhost) com o parâmetro `-InstanceView`.

```
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

## <a name="clean-up"></a>Limpeza

Está a ser cobrado pelos seus anfitriões dedicados mesmo quando não são implementadas máquinas virtuais. Deve eliminar quaisquer anfitriões que não esteja a utilizar para poupar custos.  

Só é possível eliminar um hospedeiro quando já não existem máquinas virtuais que o utilizem. Elimine os VMs utilizando [remove-AzVM](/powershell/module/az.compute/remove-azvm).

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Depois de eliminar os VMs, pode eliminar o hospedeiro utilizando [o Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Uma vez eliminado todos os seus anfitriões, pode eliminar o grupo anfitrião utilizando [o Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Também pode eliminar todo o grupo de recursos num único comando utilizando [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Isto eliminará todos os recursos criados no grupo, incluindo todos os VMs, anfitriões e grupos de acolhimento.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Passos seguintes

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas como domínios de falha para a máxima resiliência numa região.

- Também pode instalar anfitriões dedicados utilizando o [portal Azure.](dedicated-hosts-portal.md)

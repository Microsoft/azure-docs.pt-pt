---
title: Implantar hosts dedicados do Azure usando o Azure PowerShell
description: Implante VMs em hosts dedicados usando Azure PowerShell.
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
ms.openlocfilehash: 92dca6f4f41ff426aebcb8e580653afaa71afff8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033357"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Versão prévia: implantar VMs em hosts dedicados usando o Azure PowerShell

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMS). 

Verifique se você instalou Azure PowerShell versão 2.4.2 ou posterior e se está conectado a uma conta do Azure no com `Connect-AzAccount`. Para instalar a versão 2.4.2, abra um prompt do PowerShell e digite:

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrerelease
```

Será necessário pelo menos a versão 1.6.0 do módulo PowerShellGet para habilitar a funcionalidade do módulo de visualização no PowerShell. As versões mais recentes do PowerShell Core têm isso criado automaticamente, mas para versões mais antigas do PowerShell, você pode executar o seguinte comando para atualizar para a versão mais recente:

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> Atualmente, os hosts dedicados do Azure estão em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitações de visualização conhecidas**
> - Atualmente, não há suporte para conjuntos de dimensionamento de máquinas virtuais em hosts dedicados.
> - A versão inicial da visualização dá suporte à seguinte série de VMs: DSv3 e ESv3. 



## <a name="create-a-host-group"></a>Criar um grupo de hosts

Um **grupo de hosts** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com seus hosts dedicados: 
- Alcance entre várias zonas de disponibilidade. Nesse caso, é necessário ter um grupo de hosts em cada uma das zonas que você deseja usar.
- Alcance entre vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínios de falha para seu grupo de hosts. Se você não quiser abranger domínios de falha em seu grupo, use uma contagem de domínio de falha de 1. 

Você também pode optar por usar zonas de disponibilidade e domínios de falha. Este exemplo cria um grupo de hosts na zona 1, com 2 domínios de falha. 


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

## <a name="create-a-host"></a>Criar um host

Agora, vamos criar um host dedicado no grupo de hosts. Além de um nome para o host, você deve fornecer a SKU para o host. O SKU do host captura a série de VMs com suporte, bem como a geração de hardware para seu host dedicado.  Durante a visualização, ofereceremos suporte aos seguintes valores de SKU do host: DSv3_Type1 e ESv3_Type1.


Para obter mais informações sobre os preços e as SKUs do host, consulte [preços do host dedicado do Azure](https://aka.ms/ADHPricing).

Se você definir uma contagem de domínios de falha para seu grupo de hosts, será solicitado que você especifique o domínio de falha para o host. Neste exemplo, definimos o domínio de falha para o host como 1.


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

Crie uma máquina virtual no host dedicado. 

Se você especificou uma zona de disponibilidade ao criar o grupo de hosts, será necessário usar a mesma zona ao criar a máquina virtual. Para este exemplo, como nosso grupo de hosts está na zona 1, precisamos criar a VM na zona 1.  


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
> Se você criar uma máquina virtual em um host que não tem recursos suficientes, a máquina virtual será criada em um estado de falha. 

## <a name="check-the-status-of-the-host"></a>Verificar o status do host

Você pode verificar o status de integridade do host e quantas máquinas virtuais você ainda pode implantar no host usando [GetAzHost](/powershell/module/az.compute/get-azhost) com o parâmetro `-InstanceView`.

```
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

A saída terá uma aparência semelhante a esta:

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

Você está sendo cobrado por seus hosts dedicados, mesmo quando não há máquinas virtuais implantadas. Você deve excluir os hosts que não está usando no momento para economizar custos.  

Você só pode excluir um host quando não houver mais máquinas virtuais usando-o. Exclua as VMs usando [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Depois de excluir as VMs, você pode excluir o host usando [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Depois de excluir todos os hosts, você poderá excluir o grupo de hosts usando [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Você também pode excluir o grupo de recursos inteiro em um único comando usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Isso excluirá todos os recursos criados no grupo, incluindo todas as VMs, hosts e grupos de hosts.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Passos seguintes

- Há um modelo de exemplo, encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter máxima resiliência em uma região.

- Você também pode implantar hosts dedicados usando o [portal do Azure](dedicated-hosts-portal.md).

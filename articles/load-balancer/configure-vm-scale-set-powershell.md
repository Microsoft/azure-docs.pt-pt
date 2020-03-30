---
title: Configure conjunto de escala de máquina virtual com um equilíbrio de carga Azure existente - Azure PowerShell
description: Aprenda a configurar um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350000"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Configure um conjunto de escala de máquina virtual com um equilíbrio de carga Azure existente utilizando o Azure PowerShell

Neste artigo, você aprenderá a configurar um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Um equilibrador de carga padrão existente na subscrição onde será implantado o conjunto de escala de máquina virtual.
- Uma Rede Virtual Azure para o conjunto de escala de máquina virtual.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Assine em Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implementar um conjunto de escala de máquina virtual com o equilibrador de carga existente

Substitua os valores em parênteses pelos nomes dos recursos na sua configuração.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

O exemplo abaixo implementa um conjunto de escala de máquina virtual com:

- Conjunto de escala de máquina virtual chamado **myVMSS**
- Balancer de carga Azure nomeado **myLoadBalancer**
- Piscina de backend de balancer de carga chamada **myBackendPool**
- Rede Virtual Azure nomeada **myVnet**
- Subnet nomeado **mySubnet**
- Grupo de recursos nomeado **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Após a criação do conjunto de calcário, a porta de backend não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda de saúde do equilibrador de carga. Para alterar a porta, pode remover a sonda de saúde atualizando o conjunto de escala de máquina virtual Azure, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente.  Para saber mais sobre conjuntos de escala de máquinas virtuais e equilibrador de carga, consulte:

- [O que é o Balanceador de Carga do Azure?](load-balancer-overview.md)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)
                                
---
title: Configurar a balança virtual da máquina definida com um balanceador de carga Azure existente - Azure PowerShell
description: Aprenda a configurar uma balança de máquina virtual definida com um Balançador de Carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88510067"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente usando Azure PowerShell

Neste artigo, você vai aprender a configurar uma balança de máquina virtual definida com um Azure Load Balancer existente.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Um balanceador de carga padrão existente na subscrição onde será implantado o conjunto de escala de máquina virtual.
- Uma Rede Virtual Azure para o conjunto de escala de máquina virtual.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Inscreva-se no Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implementar um conjunto de escala de máquina virtual com o equilibrador de carga existente

Substitua os valores nos parênteses pelos nomes dos recursos na sua configuração.

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

O exemplo abaixo implementa uma escala de máquina virtual definida com:

- Conjunto de escala de máquina virtual nomeado **myVMSS**
- Azure Load Balancer nomeado **myLoadBalancer**
- Piscina de backend de balançador de carga chamada **myBackendPool**
- Rede Virtual Azure chamada **myVnet**
- Sub-rede chamada **mySubnet**
- Grupo de recursos nomeado **myResourceGroup**

```azurepowershell-interactive

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
> Após a criação do conjunto de escala, a porta de backend não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda sanitária do equilibrador de carga. Para alterar a porta, pode remover a sonda de saúde atualizando o conjunto de escala de máquina virtual Azure, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou uma balança de máquina virtual definida com um Balançador de Carga Azure existente.  Para saber mais sobre conjuntos de balanças de máquinas virtuais e balanceador de carga, consulte:

- [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)

---
title: Criar e configurar um plano de proteção Azure DDoS utilizando a Azure PowerShell
description: Saiba como criar um Plano de Proteção DDoS utilizando o Azure PowerShell
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 69f9b5a74566879ecf8f15f23e689ebb731da45a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97814147"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Quickstart: Criar e configurar norma de proteção Azure DDoS usando Azure PowerShell

Começa com o Azure DDoS Protection Standard utilizando o Azure PowerShell. 

Um plano de proteção DDoS define um conjunto de redes virtuais que têm o padrão de proteção DDoS ativado, através de subscrições. Pode configurar um plano de proteção DDoS para a sua organização e ligar redes virtuais de várias subscrições ao mesmo plano. 

Neste arranque rápido, irá criar um plano de proteção DDoS e ligá-lo a uma rede virtual. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção DDoS

Em Azure, aloca recursos relacionados a um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos, utilize [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Neste exemplo, vamos nomear o nosso grupo de recursos _MyResourceGroup_ e usar a localização _dos EUA orientais:_

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Agora crie um plano de proteção DDoS chamado _MyDdosProtectionPlan:_

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Ativar o DDoS para uma rede virtual

### <a name="enable-ddos-for-a-new-virtual-network"></a>Ativar o DDoS para uma nova rede virtual

Pode ativar a proteção DDoS ao criar uma rede virtual. Neste exemplo, vamos nomear a nossa rede virtual _MyVnet:_ 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Ativar o DDoS para uma rede virtual existente

Pode associar uma rede virtual existente ao criar um plano de proteção DDoS:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Validar e testar

Primeiro, verifique os detalhes do seu plano de proteção DDoS:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Verifique se o comando devolve os detalhes corretos do seu plano de proteção DDoS.

## <a name="clean-up-resources"></a>Limpar os recursos

Podes ficar com os teus recursos para o próximo tutorial. Se já não for necessário, elimine o grupo de recursos _MyResourceGroup._ Quando elimina o grupo de recursos, também elimina o plano de proteção DDoS e todos os seus recursos relacionados. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Para desativar a proteção DDoS para uma rede virtual: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Se pretender eliminar um plano de proteção DDoS, tem primeiro de dissociar todas as redes virtuais do mesmo.

## <a name="next-steps"></a>Passos seguintes

Para aprender a visualizar e configurar a telemetria para o seu plano de proteção DDoS, continue para os tutoriais.

> [!div class="nextstepaction"]
> [Ver e configurar telemetria de proteção contra DDoS](telemetry.md)
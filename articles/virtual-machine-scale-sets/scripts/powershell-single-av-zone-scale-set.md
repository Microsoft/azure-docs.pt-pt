---
title: Amostras Azure PowerShell - Conjunto de escala de zona única
description: Este script cria um conjunto de dimensionamento de máquinas virtuais com o Windows Server 2016 numa única Zona de Disponibilidade.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 04/05/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: dc76798ff97aa49d358b639557255b41872bab4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89070926"
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Criar um conjunto de dimensionamento de máquinas virtuais de zona única com o PowerShell
Este script cria um conjunto de dimensionamento de máquinas virtuais com o Windows Server 2016 numa única Zona de Disponibilidade. Depois de executar o script, pode aceder à máquina virtual através de RDP.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Execute o seguinte comando para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Cria o conjunto de dimensionamento de máquinas virtuais e todos os recursos de suporte, incluindo a rede virtual, o balanceador de carga e as regras NAT. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Obtém informações sobre um conjunto de dimensionamento de máquinas virtuais. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Adiciona uma extensão da VM para que o Script Personalizado instale uma aplicação Web básica. |
| [Atualização-AzVmss](/powershell/module/az.compute/update-azvmss) | Atualiza o modelo do conjunto de dimensionamento de máquinas virtuais para aplicar a extensão da VM. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Obtém informações sobre o endereço IP público atribuído, utilizado pelo balanceador de carga. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).


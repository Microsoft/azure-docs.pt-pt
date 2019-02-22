---
title: Exemplo do Script do PowerShell do Azure - Peering de redes virtuais | Documentos da Microsoft
description: Exemplo do Script do PowerShell do Azure - Peering de redes virtuais
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 6faf875893092496ee312e93d542d24c30abd1e1
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648851"
---
# <a name="peer-two-virtual-networks"></a>Configurar o peering entre duas redes virtuais

Este script cria e liga duas redes virtuais na mesma região através da rede do Azure. Depois de executar o script, irá criar um peering entre duas redes virtuais.

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. | 
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)| Cria uma rede e sub-rede virtual do Azure. |
| [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) | Cria um peering entre duas redes virtuais.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Encontrará exemplos adicionais de scripts do PowerShell de redes na [Documentação de Descrição Geral de Redes do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).

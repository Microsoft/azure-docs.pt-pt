---
title: Amostras Azure PowerShell - Use uma imagem VM personalizada
description: Este script cria um conjunto de dimensionamento de máquinas virtuais que utiliza uma imagem de VM personalizada como origem para as instâncias de VMs.
author: axayjo
ms.author: akjosh
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh
ms.openlocfilehash: b99077223581c4960c838b54b6b8885309f7d999
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125133"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-powershell"></a>Criar um conjunto de dimensionamento de máquinas virtuais a partir de uma imagem de VM personalizada com o PowerShell
Este script cria um conjunto de dimensionamento de máquinas virtuais que utiliza uma imagem de VM personalizada como origem para as instâncias de VMs.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.ps1 "Create a virtual machine scale set with a custom VM image")]

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode encontrar exemplos adicionais do script do conjunto de dimensionamento de máquinas virtuais do PowerShell na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../powershell-samples.md).

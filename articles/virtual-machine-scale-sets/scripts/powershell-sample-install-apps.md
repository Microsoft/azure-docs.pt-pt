---
title: Amostras Azure PowerShell - Instale aplicações
description: Este script cria um conjunto de dimensionamento de máquinas virtuais em Windows Server 2016 e utiliza a Extensão de Script Personalizado para instalar uma aplicação Web básica.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: sample
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a885306928fc42be5773c0d1d558d2810e355c54
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76276508"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>Instalar aplicações num conjunto de dimensionamento de máquinas virtuais com o PowerShell
Este script cria um conjunto de dimensionamento de máquinas virtuais em Windows Server 2016 e utiliza a Extensão de Script Personalizado para instalar uma aplicação Web básica. Após executar o script, pode aceder à aplicação Web através de um browser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

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
| [Extensão add-AzVmss](/powershell/module/az.compute/add-azvmssextension) | Adiciona uma extensão da VM para que o Script Personalizado instale uma aplicação Web básica. |
| [Atualização-AzVmss](/powershell/module/az.compute/update-azvmss) | Atualiza o modelo do conjunto de dimensionamento de máquinas virtuais para aplicar a extensão da VM. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Obtém informações sobre o endereço IP público atribuído, utilizado pelo balanceador de carga. |
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode encontrar exemplos adicionais do script do conjunto de dimensionamento de máquinas virtuais do PowerShell na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../powershell-samples.md).

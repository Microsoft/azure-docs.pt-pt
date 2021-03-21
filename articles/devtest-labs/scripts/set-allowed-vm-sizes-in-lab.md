---
title: 'Script PowerShell: Definir tamanhos de VM permitidos em Azure Lab Services | Microsoft Docs'
description: Este artigo inclui uma amostra de script PowerShell que define tamanhos de máquina virtual (VM) nos Serviços de Laboratório Azure.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998142"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Use PowerShell para definir tamanhos de VM permitidos em Serviços de Laboratório Azure

Esta amostra De script PowerShell permitiu tamanhos de máquina virtual (VM) em Serviços de Laboratório Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório.** O guião requer que tenhas um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| Find-AzResource | Pesquisas de recursos com base em parâmetros especificados. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtém recursos. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Criar um recurso. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Amostras adicionais de scripts powerShell do Azure Lab Services podem ser encontradas nas [amostras powerShell dos Serviços do Laboratório Azure](../samples-powershell.md).

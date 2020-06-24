---
title: 'Script PowerShell: Definir tamanhos de VM permitidos nos Serviços de Laboratório Azure / Microsoft Docs'
description: Estes artigos incluem uma amostra de script PowerShell que define tamanhos de máquina virtual (VM) em Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 5b3dbee7d0ac928c4f18f25348e714aba9c1cd13
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898060"
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

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de scripts powerShell do Azure Lab Services podem ser encontradas nas [amostras powerShell dos Serviços do Laboratório Azure](../samples-powershell.md).

---
title: 'Script PowerShell: Conjunto permitido tamanhos vM em Serviços de Laboratório Azure / Microsoft Docs'
description: Estes artigos incluem um script powerShell de amostra que define tamanhos permitidos de máquina virtual (VM) em Serviços de Laboratório Azure.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760470"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Use powerShell para definir tamanhos de VM permitidos em Serviços de Laboratório Azure

Esta amostra de conjuntos de scripts PowerShell permitiu tamanhos de máquina virtual (VM) em Serviços de Laboratório Azure.

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
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Recebe recursos. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso. |
| [Novo AzResource](/powershell/module/az.resources/new-azresource) | Criar um recurso. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script seletiva de serviços do Laboratório Azure PowerShell podem ser encontradas nas [amostras powerShell dos Serviços de Laboratório Azure](../samples-powershell.md).

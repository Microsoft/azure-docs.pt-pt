---
title: PowerShell - Adicione um utilizador externo a um laboratório em Azure DevTest Labs
description: Este artigo fornece um script Azure PowerShell que adiciona um utilizador externo a um laboratório em Azure DevTest Labs.
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
ms.openlocfilehash: e9efe5476da051d905feff3d6803cb60ea0c4beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760492"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Use o PowerShell para adicionar um utilizador externo a um laboratório em Azure DevTest Labs

Este script da amostra PowerShell adiciona um utilizador externo a um laboratório em Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório.** O guião requer que tenhas um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Retry o objeto de utilizador do diretório ativo Azure. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Atribui a função especificada ao comitente especificado, no âmbito especificado. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script seletiva de serviços do Laboratório Azure PowerShell podem ser encontradas nas [amostras powerShell dos Serviços de Laboratório Azure](../samples-powershell.md).

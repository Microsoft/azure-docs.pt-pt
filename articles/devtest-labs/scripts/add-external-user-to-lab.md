---
title: PowerShell - Adicione um utilizador externo a um laboratório em Azure DevTest Labs
description: Este artigo fornece um script Azure PowerShell que adiciona um utilizador externo a um laboratório em Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 76fbb5e4d7f5db39a0aadba9098ebf1064b3cda5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022399"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Use o PowerShell para adicionar um utilizador externo a um laboratório em Azure DevTest Labs

Esta amostra do script PowerShell adiciona um utilizador externo a um laboratório em Azure DevTest Labs. 

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
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Retira o objeto do utilizador do diretório ativo Azure. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Atribui a função especificada ao principal especificado, no âmbito especificado. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Amostras adicionais de scripts powerShell do Azure Lab Services podem ser encontradas nas [amostras powerShell dos Serviços do Laboratório Azure](../samples-powershell.md).

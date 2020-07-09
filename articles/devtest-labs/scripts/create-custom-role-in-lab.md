---
title: PowerShell - Crie um papel personalizado num laboratório em Azure DevTest Labs
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
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: a7822d6ea46b7efc21f43e944d0a96f609df389a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84897412"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Use o PowerShell para criar um papel personalizado num laboratório em Azure DevTest Labs

Esta amostra de script PowerShell cria um papel personalizado para usar em um laboratório em Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório.** O guião requer que tenhas um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzProviderOperação](/powershell/module/az.resources/get-azprovideroperation) | Obtém as operações para um fornecedor de recursos Azure que são securáveis usando a Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Lista todas as funções Azure RBAC que estão disponíveis para atribuição. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Cria um papel personalizado. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de scripts powerShell do Azure Lab Services podem ser encontradas nas [amostras powerShell dos Serviços do Laboratório Azure](../samples-powershell.md).

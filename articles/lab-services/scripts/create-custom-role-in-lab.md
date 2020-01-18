---
title: PowerShell-criar uma função personalizada em um laboratório no Azure DevTest Labs
description: Esse script do PowerShell adiciona um usuário externo a um laboratório no Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: bcad4e02105bff2dc78a4adc3ca614f997794d52
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166428"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Use o PowerShell para criar uma função personalizada em um laboratório no Azure DevTest Labs

Este script do PowerShell de exemplo cria uma função personalizada para usar em um laboratório no Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório**. O script exige que você tenha um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Obtém as operações para um provedor de recursos do Azure que são protegíveis usando o RBAC do Azure. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Lista todas as funções RBAC do Azure que estão disponíveis para atribuição. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Cria uma função personalizada. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script Azure Lab Services PowerShell podem ser encontrados nos [exemplos do Azure Lab Services PowerShell](../samples-powershell.md).

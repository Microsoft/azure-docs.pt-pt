---
title: PowerShell-adicionar uma imagem do Marketplace a um laboratório no Azure DevTest Labs
description: Esse script do PowerShell adiciona uma imagem do Marketplace a um laboratório no Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166451"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Use o PowerShell para adicionar uma imagem do Marketplace a um laboratório no Azure DevTest Labs

Este script do PowerShell de exemplo adiciona uma imagem do Marketplace a um laboratório no Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório**. O script exige que você tenha um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| Find-AzResource | Pesquisa recursos com base em parâmetros especificados. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtém recursos. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Criar um recurso. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script Azure Lab Services PowerShell podem ser encontrados nos [exemplos do Azure Lab Services PowerShell](../samples-powershell.md).

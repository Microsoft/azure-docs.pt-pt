---
title: 'Script do PowerShell: Adicionar uma imagem do marketplace a um laboratório no Azure DevTest Labs | Documentos da Microsoft'
description: Este script do PowerShell adiciona uma imagem do marketplace, a um laboratório no Azure DevTest Labs.
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
ms.openlocfilehash: 22c30e1c3b19e62489a08a702d8b35fbc4862e13
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728221"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Utilize o PowerShell para adicionar uma imagem do marketplace a um laboratório no Azure DevTest Labs

Este script do PowerShell de exemplo adiciona uma imagem do marketplace, a um laboratório no Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório**. O script requer que tenha um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Find-AzResource](/powershell/module/az.resources/find-azresource) | Procura de recursos com base em parâmetros especificados. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtém os recursos. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Crie um recurso. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos do script do PowerShell do Azure Lab Services adicionais podem ser encontrados no [exemplos do PowerShell do Azure Lab Services](../samples-powershell.md).

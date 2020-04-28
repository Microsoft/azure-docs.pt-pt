---
title: PowerShell - Criar imagem personalizada a partir do ficheiro VHD nos Serviços do Laboratório Azure
description: Este script PowerShell cria uma imagem personalizada a partir de um ficheiro VHD nos Serviços de Laboratório Azure.
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
ms.openlocfilehash: 38383462a665ced1ccb6c6a2f062fab0492eee9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169986"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Use powerShell para criar uma imagem personalizada a partir de um ficheiro VHD nos Serviços de Laboratório Azure

Este script da amostra PowerShell cria uma imagem personalizada de um ficheiro VHD nos Serviços de Laboratório Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório.** O guião requer que tenhas um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Recebe recursos. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Obtém as chaves de acesso para uma conta de Armazenamento do Azure. |
| [Implantação do Grupo New-AzResource](/powershell/module/az.resources/new-azresourcegroupdeployment) | Adiciona uma implantação Azure a um grupo de recursos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script seletiva de serviços do Laboratório Azure PowerShell podem ser encontradas nas [amostras powerShell dos Serviços de Laboratório Azure](../samples-powershell.md).

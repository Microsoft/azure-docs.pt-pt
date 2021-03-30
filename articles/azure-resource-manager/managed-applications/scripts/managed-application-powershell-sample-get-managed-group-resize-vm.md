---
title: Obtenha grupo de recursos geridos & redimensionar VMs - Azure PowerShell
description: Fornece o script da amostra Azure PowerShell que obtém um grupo de recursos gerido para uma aplicação gerida Azure. O guião redimensiona os VMs.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 602aaeb67ca081ebac71ca1d6d24a2de3c020603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86055992"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Obtenha recursos num grupo de recursos geridos e redimensione VMs com PowerShell

Este script obtém os recursos a partir de um grupo de recursos gerido e redimensiona as VMs nesse grupo de recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para implementar a aplicação gerida. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzManagedApplication](/powershell/module/az.resources/get-azmanagedapplication) | Lista as aplicações geridas. Forneça o nome do grupo de recursos para focar os resultados. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lista os recursos. Forneça um grupo de recursos e um tipo de recurso para focar o resultado. |
| [Update-AzVM](/powershell/module/az.compute/update-azvm) | Atualize um tamanho de máquina virtual. |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/get-started-azureps).

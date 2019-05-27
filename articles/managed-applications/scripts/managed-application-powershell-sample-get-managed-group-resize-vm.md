---
title: Exemplo do script de PowerShell do Azure – obter um grupo de recursos gerido e redimensionar VMs | Documentos da Microsoft
description: Exemplo de script do Azure PowerShell - obter um grupo de recursos gerido e redimensionar VMs
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 9e8930c95495673c0082a82757ed6d8137900b6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171483"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Obter recursos num grupo de recursos gerido e redimensionar VMs com o PowerShell

Este script obtém os recursos a partir de um grupo de recursos gerido e redimensiona as VMs nesse grupo de recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para implementar a aplicação gerida. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Lista as aplicações geridas. Forneça o nome do grupo de recursos para focar os resultados. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Lista os recursos. Forneça um grupo de recursos e o tipo de recurso para focar o resultado. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Atualize um tamanho de máquina virtual. |


## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

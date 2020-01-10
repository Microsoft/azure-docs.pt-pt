---
title: Script do Azure PowerShell de exemplo - criar uma área de trabalho do Log Analytics | Documentos da Microsoft
description: Script do Azure PowerShell de exemplo - criar uma área de trabalho do Log Analytics para
ms.service: azure-monitor
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 242ba4d8487e25ae0d6c5fd7dc1525942deb2a64
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363103"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Criar uma área de trabalho do Log Analytics com o PowerShell

Este script ajuda-o em funcionamento rapidamente com uma área de trabalho do Log Analytics do Azure, que é necessária se quiser voltar a recolher, analisar e efetuar ações nos dados.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar uma nova área de trabalho do Log Analytics na sua subscrição. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Obtém informações sobre uma área de trabalho existente. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Cria uma área de trabalho no grupo de recursos especificado e localização. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).


---
title: Exemplo de script Azure PowerShell-criar um espaço de trabalho Log Analytics | Microsoft Docs
description: Azure PowerShell exemplo de script – criar um espaço de trabalho Log Analytics para
ms.service: azure-monitor
ms.subservice: logs
ms.topic: sample
author: MGoedtel
ms.author: magoedte
ms.date: 09/07/2017
ms.openlocfilehash: 6aad6c6f0656bd317a808de13e340d5774cea49b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931914"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Criar um espaço de trabalho Log Analytics com o PowerShell

Esse script entra em funcionamento rapidamente com um espaço de trabalho Log Analytics do Azure, que é necessário se você quiser começar a coletar, analisar e executar ações em dados.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicação do script

Esse script usa os comandos a seguir para criar um novo espaço de trabalho Log Analytics em sua assinatura. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Obtém informações sobre um espaço de trabalho existente. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Cria um espaço de trabalho no grupo de recursos e no local especificados. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).


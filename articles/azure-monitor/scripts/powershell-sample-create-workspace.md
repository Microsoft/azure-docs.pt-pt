---
title: Criar espaço de trabalho Log Analytics - Azure PowerShell
description: Amostra de script Azure PowerShell - Criar um espaço de trabalho de Log Analytics para
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80054634"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Criar um espaço de trabalho log Analytics com powerShell

Este script coloca-o a funcionar rapidamente com um espaço de trabalho Azure Log Analytics, que é necessário se quiser começar a recolher, analisar e tomar medidas sobre os dados.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza comandos seguintes para criar um novo espaço de trabalho log Analytics na sua subscrição. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Obtém informações sobre um espaço de trabalho existente. |
| [New-AzOperationalInsightsWorkspacespace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Cria um espaço de trabalho no grupo e localização de recursos especificados. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).


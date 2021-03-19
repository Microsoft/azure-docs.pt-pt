---
title: 'PowerShell: Restaurar uma cópia de segurança da aplicação'
description: Saiba como utilizar o Azure PowerShell para automatizar a implementação e gestão do Serviço de Aplicações. Esta amostra mostra como restaurar uma aplicação a partir de uma cópia de segurança.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: f5e36122ef1362672e5cdee4f742b98354f0bc75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89075914"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Restaurar uma aplicação web a partir de uma cópia de segurança usando Azure PowerShell

Este script de amostra recupera uma cópia de segurança previamente concluída a partir de uma aplicação web existente e restaura-a ao sobrepor o seu conteúdo. 

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure. 

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Se já não precisar da aplicação web, use o seguinte comando para remover o grupo de recursos, a aplicação web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtém uma lista de cópias de segurança para uma aplicação Web. |
| [Restaurar-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Restaura uma aplicação web a partir de uma cópia de segurança previamente concluída. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver os exemplos do Azure Powershell adicionais para as Aplicações Web do Serviço de Aplicações do Azure em [Exemplos do Azure PowerShell](../samples-powershell.md).

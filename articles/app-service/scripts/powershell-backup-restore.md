---
title: Exemplo de script de Azure PowerShell – restaurar um aplicativo Web de um backup | Microsoft Docs
description: Exemplo de script de Azure PowerShell – restaurar um aplicativo Web de um backup
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 85cbcb4b01161e0004d38f08ae8c30fd398727ff
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087898"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Restaurar um aplicativo Web de um backup usando Azure PowerShell

Este script de exemplo recupera um backup concluído anteriormente de um aplicativo Web existente e o restaura substituindo seu conteúdo. 

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure. 

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Se você não precisar mais do aplicativo Web, use o comando a seguir para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtém uma lista de cópias de segurança para uma aplicação Web. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Restaura um aplicativo Web de um backup concluído anteriormente. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para as Aplicações Web do Serviço de Aplicações do Azure em [Exemplos do Azure PowerShell](../samples-powershell.md).

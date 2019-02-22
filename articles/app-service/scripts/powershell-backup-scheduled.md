---
title: Exemplo do Script do Azure PowerShell - Criar uma cópia de segurança agendada para uma aplicação Web | Microsoft Docs
description: Exemplo do Script do Azure PowerShell - Criar uma cópia de segurança agendada para uma aplicação Web
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 262706868fe286f2134e71f53330ebd2eeb66488
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587842"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>Criar uma cópia de segurança agendada para uma aplicação web com o PowerShell

Este script de exemplo cria uma aplicação Web no Serviço de Aplicações com os respetivos recursos relacionados e, em seguida, cria uma cópia de segurança agendada para o mesmo. 

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure. 

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, a aplicação Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma Conta de armazenamento. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Cria um contentor de armazenamento do Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Gera um token SAS para um contentor de armazenamento do Azure. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação Web. |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | Edita a configuração de cópia de segurança para a aplicação Web. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtém uma lista de cópias de segurança para uma aplicação Web. |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | Obtém a configuração de cópia de segurança para a aplicação Web. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para as Aplicações Web do Serviço de Aplicações do Azure em [Exemplos do Azure PowerShell](../samples-powershell.md).

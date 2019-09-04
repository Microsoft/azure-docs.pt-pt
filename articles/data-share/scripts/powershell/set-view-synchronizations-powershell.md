---
title: 'Script do PowerShell: Definir e exibir configurações de sincronização do compartilhamento de dados do Azure | Microsoft Docs'
description: Este script do PowerShell define e obtém configurações de sincronização de compartilhamento.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 14f1925649a665b0e63ac37ef9ec7e3469042e3e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242947"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Usar o PowerShell para monitorar o uso de um compartilhamento de dados enviado

Este script do PowerShell define e obtém configurações de sincronização de compartilhamento.

## <a name="sample-script"></a>Script de exemplo


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzDataShareSynchronizationSetting](/powershell/module/az.resources/new-azdatasharesynchronizationsettings) | Criar uma sincronização de compartilhamento. |
| [Get-AzDataShareSynchronizationSetting](/powershell/module/az.resources/get-azdatasharesynchronizationsetting) | Obtém as configurações de sincronização de uma sincronização de compartilhamento. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).

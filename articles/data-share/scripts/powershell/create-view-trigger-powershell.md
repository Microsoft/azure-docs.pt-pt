---
title: 'Script PowerShell: Criar e ver um instantâneo Azure Data Share Microsoft Docs'
description: Este script PowerShell cria e obtém gatilhos de instantâneo de partilha.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4f3148a4d5bd0d39ccfcf7e92e80300a7e19effa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "70307220"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Use o PowerShell para monitorizar o uso de uma partilha de dados enviada

Este script PowerShell cria e obtém gatilhos de instantâneo de partilha.

## <a name="sample-script"></a>Script de exemplo

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$subscriptionName = "<Share subscription name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new snapshot trigger
New-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName -Name $dataShareName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List snapshot triggers
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName  -Name $dataShareName

#Get a specific share snapshot trigger
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName -Name $dataShareName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Novo AzDataShareTrigger](/powershell/module/az.datashare/new-azdatasharetrigger?view=azps-2.6.0) | Crie um gatilho de instantâneo de partilha. |
| [Get-AzDataShareTrigger](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Obtém configurações de sincronização de uma sincronização de ações. |
|||

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de scripts Azure Data Share PowerShell podem ser encontradas nas [amostras powerShell de partilha de dados Azure](../../samples-powershell.md).

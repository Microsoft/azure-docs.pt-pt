---
title: 'Script PowerShell: Monitorizar o uso de uma partilha de dados Azure'
description: Este script PowerShell recupera métricas de utilização de uma partilha de dados enviada.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: e9ff7a29cba9b8e9ca058bfe742f484c5b495cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221320"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Use o PowerShell para monitorizar o uso de uma partilha de dados enviada

Este script PowerShell monitoriza o uso de dados listando as sincronizações de uma partilha de dados enviada e obtendo os detalhes de uma sincronização específica.

## <a name="sample-script"></a>Script de exemplo


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzDataShareSynchronização](/powershell/module/az.datashare/get-azdatasharesynchronization) | Enumerar sincronizações numa ação. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail) | Obtém detalhes de sincronização de uma sincronização de ações. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Amostras adicionais de scripts Azure Data Share PowerShell podem ser encontradas nas [amostras powerShell de partilha de dados Azure](../../samples-powershell.md).

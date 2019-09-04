---
title: 'Script do PowerShell: Criar um convite de compartilhamento de dados do Azure | Microsoft Docs'
description: Esse script do PowerShell envia um convite de compartilhamento de dados.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b1150d3a60e4ea7c22de5cfe7f9ea4a584c315d9
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242973"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Usar o PowerShell para monitorar o uso de um compartilhamento de dados enviado

Este script do PowerShell cria um convite de compartilhamento de dados.

## <a name="sample-script"></a>Script de exemplo


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Crie um convite de compartilhamento de dados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).

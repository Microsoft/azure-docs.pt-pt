---
title: 'PowerShell script: Aceite convite de uma partilha de dados Azure'
description: Este script PowerShell aceita convites de uma partilha de dados existente.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 24091bafac7ad5c558b975d52064f12715b3d622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221405"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Use o PowerShell para aceitar um convite de partilha de dados

Este script PowerShell aceita convites enviados a um consumidor.

## <a name="sample-script"></a>Script de exemplo
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzDataShareInvitação](/powershell/module/az.datashare/get-azdatashareinvitation) | Receber e lista enviar convites de partilha de dados. |
| [Nova AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | Criar uma subscrição de partilha de dados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Amostras adicionais de scripts Azure Data Share PowerShell podem ser encontradas nas [amostras powerShell de partilha de dados Azure](../../samples-powershell.md).

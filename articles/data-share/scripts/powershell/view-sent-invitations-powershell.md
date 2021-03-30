---
title: 'PowerShell script: Lista de convites de partilha de dados da Azure enviados a um consumidor'
description: Saiba como este script PowerShell recebe convites enviados a um consumidor e veja um exemplo do script que pode usar.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221218"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Use o PowerShell para obter um convite de partilha de dados

Este guião powerShell recebe convites enviados a um consumidor.

## <a name="sample-script"></a>Script de exemplo
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [Get-AzDataShareInvitação](/powershell/module/az.datashare/get-azdatashareinvitation) | Receber e lista enviar convites de partilha de dados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Amostras adicionais de scripts Azure Data Share PowerShell podem ser encontradas nas [amostras powerShell de partilha de dados Azure](../../samples-powershell.md).

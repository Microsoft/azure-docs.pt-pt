---
title: 'Roteiro PowerShell: Aceite convite de uma Partilha de Dados Do Azure [ Microsoft Docs'
description: Este script PowerShell aceita convites de uma partilha de dados existente.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70307337"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Use o PowerShell para aceitar um convite de partilha de dados

Este guião da PowerShell aceita convites enviados a um consumidor.

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
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Obtenha e lista envie convites de partilha de dados. |
| [Subscrição new-AzDataShare](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | Criar uma subscrição de partilha de dados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script de partilha de dados azure PowerShell podem ser encontradas nas [amostras de PowerShell de partilha](../../samples-powershell.md)de dados do Azure .


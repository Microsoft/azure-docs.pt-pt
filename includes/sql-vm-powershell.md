---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554936"
---
## <a name="start-your-powershell-session"></a>Iniciar a sessão do PowerShell
 

Execute o cmdlet [**da conta Connect-Az**](/powershell/module/Az.Accounts/Connect-AzAccount) e será apresentado com um ecrã de entrada para introduzir as suas credenciais. Utilize as mesmas credenciais que utiliza para iniciar sessão no Portal do Azure.

```powershell
Connect-AzAccount
```

Se tiver várias subscrições, utilize o [**cmdlet Set-AzContext**](/powershell/module/az.accounts/set-azcontext) para selecionar qual a subscrição que a sua sessão PowerShell deve utilizar. Para ver que subscrição a atual sessão powerShell está a usar, corra [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext). Para ver todas as suas subscrições, execute [**a Subscrição Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```
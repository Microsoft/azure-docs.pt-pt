---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184217"
---
## <a name="start-your-powershell-session"></a>Iniciar a sessão do PowerShell
 

Execute o cmdlet [**da conta Connect-Az**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) e será apresentado com um ecrã de entrada para introduzir as suas credenciais. Utilize as mesmas credenciais que utiliza para iniciar sessão no Portal do Azure.

```powershell
Connect-AzAccount
```

Se tiver várias subscrições, utilize o [**cmdlet Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) para selecionar qual a subscrição que a sua sessão PowerShell deve utilizar. Para ver que subscrição a atual sessão powerShell está a usar, corra [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Para ver todas as suas subscrições, execute [**a Subscrição Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```


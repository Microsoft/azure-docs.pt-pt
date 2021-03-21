---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070219"
---
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Utilize o cmdlet Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para criar um grupo de recursos chamado *myResourceGroup* na localização *este.* 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

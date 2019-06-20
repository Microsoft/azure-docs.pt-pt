---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184128"
---
Antes de iniciar esta configuração, tem de iniciar sessão sua conta do Azure. O cmdlet pede-lhe as credenciais de início de sessão para a sua conta do Azure. Depois de iniciar sessão, são transferidas as definições de conta para que estejam disponíveis para o Azure PowerShell. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../articles/powershell-azure-resource-manager.md).

Para iniciar sessão, abra a consola do PowerShell com privilégios elevados e ligue à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Connect-AzAccount
```

Se tiver múltiplas subscrições do Azure, verifique as subscrições da conta.

```powershell
Get-AzSubscription
```

Especifique a subscrição que pretende utilizar.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```

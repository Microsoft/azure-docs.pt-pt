---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119400"
---
 Se estiver a utilizar o Azure Cloud Shell, inicie sessão sua conta do Azure automaticamente depois de clicar em "Experimente". Para iniciar sessão localmente, abra a consola do PowerShell com privilégios elevados e execute o cmdlet para se ligar.

```azurepowershell
Connect-AzAccount
```

Se tiver mais de uma subscrição, obtenha uma lista das suas subscrições do Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Especifique a subscrição que pretende utilizar.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```
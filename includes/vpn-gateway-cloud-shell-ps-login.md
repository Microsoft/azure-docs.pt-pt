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
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320241"
---
Abra a consola do PowerShell com privilégios elevados.



Se estiver a executar localmente do Azure PowerShell, ligue à sua conta do Azure. O *Connect-AzAccount* cmdlet pede-lhe as credenciais. Após a autenticação, são transferidas as definições de conta para que fiquem disponíveis para o Azure PowerShell. Se não estiver a executar PowerShell localmente e estiver em vez disso, a utilizar o Azure Cloud Shell "Experimente" no navegador, ignore este passo primeiro. Irá ligar à sua conta do Azure automaticamente.

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
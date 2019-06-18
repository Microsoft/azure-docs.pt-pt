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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147071"
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
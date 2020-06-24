---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183992"
---
 Se estiver a utilizar o Azure Cloud Shell, inscreva-se automaticamente na sua conta Azure depois de clicar em 'Experimente'. Para iniciar seduca localmente, abra a sua consola PowerShell com privilégios elevados e coloque o cmdlet para ligar.

```azurepowershell
Connect-AzAccount
```

Se tiver mais de uma subscrição, obtenha uma lista das suas subscrições Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Especifique a subscrição que pretende utilizar.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```
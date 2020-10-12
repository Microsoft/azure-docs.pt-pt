---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77133609"
---
Abra a sua consola PowerShell com privilégios elevados.

Se estiver a executar a Azure PowerShell localmente, ligue-se à sua conta Azure. O *cmdlet Connect-AzAccount solicita-lhe* credenciais. Depois de autenticar, descarrega as definições da sua conta para que estejam disponíveis para a Azure PowerShell. Se estiver a utilizar a Azure Cloud Shell, não precisa de executar *o Connect-AzAccount*. A Azure Cloud Shell liga-se automaticamente à sua conta Azure.

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
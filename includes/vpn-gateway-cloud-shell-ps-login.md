---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061646"
---
Se estiver a executar o PowerShell localmente, abra a consola PowerShell com privilégios elevados e ligue-se à sua conta Azure. O *cmdlet Connect-AzAccount solicita-lhe* credenciais. Depois de autenticar, descarrega as definições da sua conta para que estejam disponíveis para a Azure PowerShell.

Se estiver a utilizar o Azure Cloud Shell em vez de executar o PowerShell localmente, irá notar que não precisa de executar *o Connect-AzAccount*. A Azure Cloud Shell conecta-se automaticamente à sua conta Azure depois de selecionar **Try It**.

1. Se estiver a executar o PowerShell localmente, inscreva-se.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Se tiver mais de uma subscrição, obtenha uma lista das suas subscrições Azure.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Especifique a subscrição que pretende utilizar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84465115"
---
O seguinte comando PowerShell negará todo o tráfego para o ponto final público da conta de armazenamento. Note que este comando tem o `-Bypass` parâmetro definido para `AzureServices` . Isto permitirá que serviços de primeira parte fidedignos, como o Azure File Sync, acedam à conta de armazenamento através do ponto final público.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```
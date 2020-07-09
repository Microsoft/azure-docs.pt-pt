---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465049"
---
O seguinte comando CLI negará todo o tráfego para o ponto final público da conta de armazenamento. Note que este comando tem o `-bypass` parâmetro definido para `AzureServices` . Isto permitirá que serviços de primeira parte fidedignos, como o Azure File Sync, acedam à conta de armazenamento através do ponto final público.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
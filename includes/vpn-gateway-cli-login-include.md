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
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121066"
---
Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/) e siga as instruções no ecrã. Para obter mais informações sobre o início de sessão, veja [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Se tiver mais de uma subscrição do Azure, liste as subscrições da conta.

```azurecli
az account list --all
```

Especifique a subscrição que pretende utilizar.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```

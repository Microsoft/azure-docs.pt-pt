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
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121205"
---
1. Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/) e siga as instruções no ecrã. Para obter mais informações sobre o início de sessão, veja [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Se tiver mais de uma subscrição do Azure, liste as subscrições da conta.

   ```azurecli
   az account list --all
   ```
3. Especifique a subscrição que pretende utilizar.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```

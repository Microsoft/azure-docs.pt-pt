---
title: Exemplo do Script da CLI do Azure - Repor as credenciais da conta | Microsoft Docs
description: Utilize o script da CLI do Azure para repor as credenciais da sua conta e voltar às definições de app.config.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 0a1a5bf9f192a7128da8843b79b60be0175cec23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092138"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Exemplo do Azure CLI: Redefinir as credenciais de conta

Neste artigo, o script da CLI do Azure mostra como repor as credenciais da sua conta e voltar às definições de app.config.

## <a name="prerequisites"></a>Pré-requisitos

[Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Passos seguintes

* [az ams](/cli/azure/ams)
* [Repor credenciais](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)

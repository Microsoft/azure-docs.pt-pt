---
title: Exemplo do Script da CLI do Azure - Repor as credenciais da conta | Microsoft Docs
description: Utilize o script da CLI do Azure para repor as credenciais da sua conta e voltar às definições de app.config.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 90ce5ec3a53e2ca707512e6f534d6b242f513d3b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269748"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Exemplo do Azure CLI: Redefinir as credenciais de conta

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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

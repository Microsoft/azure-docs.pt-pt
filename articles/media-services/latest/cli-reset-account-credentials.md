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
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: da997ec71655231876749d9f3aa65ba06fd3a1f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295840"
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

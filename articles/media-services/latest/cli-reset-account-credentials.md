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
ms.openlocfilehash: 09c93e2d851bea22e9d54dde35398f36335eb896
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967580"
---
# <a name="cli-example-reset-the-account-credentials"></a>Exemplo da CLI: Redefinir as credenciais da conta

Neste artigo, o script da CLI do Azure mostra como repor as credenciais da sua conta e voltar às definições de app.config.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

```
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup 
 ```

## <a name="next-steps"></a>Passos Seguintes

* [AZ AMS](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Redefinir credenciais](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-reset-credentials)

---
title: incluir ficheiro
description: incluir ficheiro
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 9a9aaf723b7b89b5b9c5611cea05c22c4003b99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755592"
---
### <a name="access-the-media-services-api"></a>Aceder à API dos Serviços de Multimédia

Para ligar às APIs dos Serviços de Multimédia do Azure, utilize a autenticação do principal de serviço do Azure AD. O comando seguinte cria uma aplicação do Azure AD e anexa um principal de serviço à conta. Deve utilizar os valores devolvidos para configurar a aplicação.

Antes de executar o script, deve substituir o `amsaccount` e `amsResourceGroup` pelos nomes que escolheu ao criar estes recursos. `amsaccount` é o nome da conta dos Serviços de Multimédia do Azure à qual o principal de serviço será anexado.

Se tiver acesso a várias subscrições, primeiro deslo sente a subscrição ativa da subscrição onde foi criada a conta Dos Serviços de Comunicação.

```azurecli
az account set --subscription subscriptionId
```

O seguinte comando devolve uma saída `json`:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Este comando produz uma resposta semelhante à seguinte:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Se pretender obter `xml` na resposta, utilize o seguinte comando:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

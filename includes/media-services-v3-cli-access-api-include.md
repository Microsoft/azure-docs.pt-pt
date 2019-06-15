---
title: incluir ficheiro
description: incluir ficheiro
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b0f93f950b55052ea8d8b31538c47226413dc82a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155764"
---
## <a name="access-the-media-services-api"></a>Aceder à API dos Serviços de Multimédia

Para ligar às APIs dos Serviços de Multimédia do Azure, utilize a autenticação do principal de serviço do Azure AD. O comando seguinte cria uma aplicação do Azure AD e anexa um principal de serviço à conta. Deve utilizar os valores devolvidos para configurar a aplicação.

Antes de executar o script, deve substituir a `amsaccount` e `amsResourceGroup` com os nomes que escolheu ao criar esses recursos. `amsaccount` é o nome da conta dos Serviços de Multimédia do Azure à qual o principal de serviço será anexado.

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

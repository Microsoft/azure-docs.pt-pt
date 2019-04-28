---
title: incluir ficheiro
description: incluir ficheiro
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309078"
---
## <a name="access-the-media-services-api"></a>Aceder à API dos Serviços de Multimédia

Para ligar às APIs dos Serviços de Multimédia do Azure, utilize a autenticação do principal de serviço do Azure AD. O comando seguinte cria uma aplicação do Azure AD e anexa um principal de serviço à conta. Deve utilizar os valores devolvidos para configurar a aplicação.

Antes de executar o script, pode substituir o `amsaccount` e o `amsResourceGroup` pelos nomes que escolheu ao criar estes recursos. `amsaccount` é o nome da conta dos Serviços de Multimédia do Azure à qual o principal de serviço será anexado.

O seguinte comando devolve uma saída `json`:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Este comando produz uma resposta semelhante à seguinte:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Se pretender obter `xml` na resposta, utilize o seguinte comando:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

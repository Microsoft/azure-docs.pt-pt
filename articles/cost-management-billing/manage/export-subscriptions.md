---
title: Exportar as informações de nível superior da sua subscrição do Azure
description: Descreve como pode ver todos os IDs de subscrição do Azure associados à sua conta.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 87135b309f0406528bcada1cd906dd2f8535d1ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202901"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportar e ver as informações de nível superior da sua subscrição
Se precisar de ver o conjunto de IDs de subscrição associados às suas credenciais de utilizador, [transfira um ficheiro .json com as informações da subscrição a partir do Centro de Contas do Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

O ficheiro .json transferido fornece as seguintes informações:
- Email (E-mail): o endereço de e-mail associado à sua conta.
- Puid: o identificador exclusivo associado à sua conta de faturação.
- SubscriptionIds (IDs de subscrição): uma lista de subscrições que pertencem à sua conta, enumeradas por ID de subscrição.

### <a name="subscriptionsjson-sample"></a>exemplo: subscriptions.json

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```

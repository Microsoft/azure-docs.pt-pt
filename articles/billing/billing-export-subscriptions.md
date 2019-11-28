---
title: Exportar as informações de nível superior da sua subscrição do Azure | Microsoft Docs
description: Descreve como pode ver todos os IDs de subscrição do Azure associados à sua conta.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 87c31e663529ea20d119753094133941b02e2756
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226271"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportar e ver as informações de nível superior da sua subscrição
Se precisar de ver o conjunto de IDs de subscrição associados às suas credenciais de utilizador, [transfira um ficheiro .json com as informações da subscrição a partir do Centro de Contas do Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

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

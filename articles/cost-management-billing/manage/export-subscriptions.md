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
ms.openlocfilehash: 553cc7fd27571ebc925e33f824060c023664a369
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991779"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportar e ver as informações de nível superior da sua subscrição
Se precisar de ver o conjunto de IDs de subscrição associados às suas credenciais de utilizador, [transfira um ficheiro .json com as informações da subscrição a partir do Centro de Contas do Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

O ficheiro .json transferido fornece as seguintes informações:
- Email: o endereço de email associado à sua conta.
- PUID: o identificador exclusivo associado à sua conta de cobrança.
- SubscriptionIds: uma lista de assinaturas que pertencem à sua conta, enumerada por ID de assinatura.

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

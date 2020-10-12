---
title: Esquemas de registos de registos de registos Azure Media Services - Azure
description: Este artigo demonstra os esquemas de registos de registos de registos da Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 8cfbe26458de630aaf411aade4a31cb4e9c72b17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295432"
---
# <a name="diagnostic-logs-schemas"></a>Esquemas dos registos de diagnósticos

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[O Azure Monitor](../../azure-monitor/overview.md) permite-lhe monitorizar métricas e registos de diagnóstico que o ajudam a compreender como as suas aplicações estão a funcionar. Pode monitorizar os registos de diagnóstico dos Serviços de Comunicação Social e criar alertas e notificações para as métricas e registos recolhidos. Você pode enviar registos para [Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), e exportá-los para [Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou usar serviços de terceiros.

Para obter informações [detalhadas, consulte as métricas do Monitor Azure](../../azure-monitor/platform/data-platform.md) e [os registos de diagnóstico do Monitor Azure](../../azure-monitor/platform/platform-logs-overview.md).

Este artigo descreve esquemas de registos de registos de media.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de registos de diagnóstico de alto nível

Para uma descrição detalhada do esquema de registos de diagnóstico de nível superior, consulte [serviços, esquemas e categorias de Registos de Diagnóstico Azure](../../azure-monitor/platform/resource-logs-schema.md).

## <a name="key-delivery-log-schema"></a>Esquema de registo de entrega de chaves

### <a name="properties"></a>Propriedades

Estas propriedades são específicas do esquema de registo de entrega de chaves.

|Nome|Descrição|
|---|---|
|keyId|A identificação da chave solicitada.|
|teclaType|Pode ser um dos seguintes valores: "Claro" (sem encriptação), "FairPlay", "PlayReady" ou "Widevine".|
|nome de política|O nome do Gestor de Recursos Azure da apólice.|
|tokenType|O tipo simbólico.|
|statusMessage|A mensagem de estado.|

### <a name="examples"></a>Exemplos

Propriedades do esquema de pedidos de entrega chave.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

[Monitorizar métricas de Serviços de Mídia e registos de diagnóstico](media-services-metrics-diagnostic-logs.md)

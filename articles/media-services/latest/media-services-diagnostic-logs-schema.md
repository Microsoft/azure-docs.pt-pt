---
title: Registos de diagnóstico da Azure Media Services schemas - Azure
description: Este artigo demonstra os registos de diagnóstico da Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75750883"
---
# <a name="diagnostic-logs-schemas"></a>Esquemas dos registos de diagnósticos

[O Monitor Azure](../../azure-monitor/overview.md) permite-lhe monitorizar métricas e registos de diagnóstico que o ajudam a compreender como as suas aplicações estão a funcionar. Pode monitorizar os registos de diagnóstico dos Serviços de Media e criar alertas e notificações para as métricas e registos recolhidos. Pode enviar registos para [o Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para os Hubs de [Eventos Azure,](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [Log Analytics,](https://azure.microsoft.com/services/log-analytics/)ou utilizar serviços de terceiros.

Para obter informações detalhadas, consulte as [Métricas do Monitor Azure](../../azure-monitor/platform/data-platform.md) e os registos de diagnóstico do [Monitor Azure](../../azure-monitor/platform/platform-logs-overview.md).

Este artigo descreve registos de diagnóstico dos Serviços De Media.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de diagnóstico de alto nível

Para uma descrição detalhada do esquema de registos de diagnóstico de alto nível, consulte [serviços suportados, schemas e categorias para registos](../../azure-monitor/platform/tutorial-dashboards.md)de diagnóstico azure .

## <a name="key-delivery-log-schema"></a>Esquema de registo de entrega de chaves

### <a name="properties"></a>Propriedades

Estas propriedades são específicas do esquema de registo de entrega da chave.

|Nome|Descrição|
|---|---|
|keyId|A identificação da chave solicitada.|
|chaveTipo|Pode ser um dos seguintes valores: "Clear" (sem encriptação), "FairPlay", "PlayReady" ou "Widevine".|
|nome político|O nome da apólice do Gestor de Recursos Azure.|
|tokenType|Do tipo de símbolo.|
|estadoMensagem|A mensagem de estado.|

### <a name="examples"></a>Exemplos

Propriedades da chave entrega solicita esquema.

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

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

[Monitor Media Services métricas e registos de diagnóstico](media-services-metrics-diagnostic-logs.md)

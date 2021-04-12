---
title: Referência de dados dos Serviços de Mídia de Monitorização
description: Material de referência importante necessário quando monitoriza os Serviços de Mídia
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 8908fd1acc64fb180121ac0b6a4dc38ce5a02572
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121171"
---
# <a name="monitoring-media-services-data-reference"></a>Referência de dados dos Serviços de Mídia de Monitorização

Este artigo abrange os dados que são úteis para a monitorização dos Serviços de Comunicação Social. Para obter mais informações sobre todas as métricas da plataforma suportadas no Azure Monitor, [reveja as métricas suportadas com o Azure Monitor](../../../azure-monitor/essentials/metrics-supported.md).

## <a name="media-services-metrics"></a>Métricas de Serviços de Mídia

As métricas são recolhidas a intervalos regulares, quer o valor mude ou não. São úteis para alertar porque podem ser amostrados com frequência, e um alerta pode ser disparado rapidamente com uma lógica relativamente simples.

Os Serviços de Comunicação Social apoiam métricas de monitorização dos seguintes recursos:

* Conta
* Ponto Final de Transmissão em Fluxo

### <a name="account"></a>Conta

Pode monitorizar as seguintes métricas de conta.

|Nome da métrica|Nome a apresentar|Description|
|---|---|---|
|Conta de Ativos|Contagem de ativos|Bens na sua conta.|
|AssetQuota|Quota de ativos|Quota de ativo na sua conta.|
|AssetQuotaUsedPercentage|Percentagem de quota de ativos utilizada|A percentagem da quota de ativos já utilizada.|
|ContentKeyPolicyCount|Contagem de políticas de chave de conteúdo|Políticas chave de conteúdo na sua conta.|
|ContentKeyPolicyQuota|Quota de política chave de conteúdo|Contingente de Políticas Chave de Conteúdo na sua conta.|
|ContentKeyPolicyQuotaUsedPercentage|Quota de política de conteúdo utilizada|A percentagem da quota de política de chave de conteúdo já utilizada.|
|StreamingPolicyCount|Contagem de políticas de streaming|Políticas de streaming na sua conta.|
|StreamingPolicyQuota|Quota de política de streaming|Quota de Políticas de Streaming na sua conta.|
|StreamingPolicyQuotaUsedPercentage|Percentagem utilizada pela quota de política de streaming|A percentagem da quota da Política de Streaming já utilizada.|

Deve também rever [as quotas e limites de conta.](../limits-quotas-constraints-reference.md)

### <a name="streaming-endpoint"></a>Ponto Final de Transmissão em Fluxo

São suportadas as [seguintes métricas de serviços](/rest/api/media/streamingendpoints) de streaming de media:

|Nome da métrica|Nome a apresentar|Description|
|---|---|---|
|Pedidos|Pedidos|Fornece o número total de pedidos HTTP servidos pelo Streaming Endpoint.|
|Saída|Saída|Egress bytes total por minuto por streaming Endpoint.|
|SucessoE2ELatency|Fim do sucesso para acabar com a latência|Duração do tempo a partir do momento em que o Streaming Endpoint recebeu o pedido para quando foi enviado o último byte da resposta.|
|Utilização da CPU| | Utilização do CPU para pontos finais de streaming premium. Estes dados não estão disponíveis para os pontos finais de streaming padrão. |
|Largura de banda da Egress | | Largura de banda de Egress em pedaços por segundo.|

## <a name="metric-dimensions"></a>Dimensões métricas

Para obter mais informações sobre as dimensões métricas, consulte [métricas multidimensionais.](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Registos do recurso

## <a name="media-services-diagnostic-logs"></a>Registos de diagnóstico dos Serviços de Comunicação Social

Os registos de diagnóstico fornecem dados ricos e frequentes sobre o funcionamento de um recurso Azure. Para mais informações, consulte [Como recolher e consumir dados de registo dos seus recursos Azure.](../../../azure-monitor/essentials/platform-logs-overview.md)

Os Serviços de Comunicação Social suportam os seguintes registos de diagnóstico:

* Entrega chave

### <a name="key-delivery"></a>Entrega chave

|Nome|Descrição|
|---|---|
|Pedido de serviço de entrega chave|Registos que mostram a informação do serviço de entrega chave. Para mais informações, consulte [os esquemas.](monitor-media-services-data-reference.md)|

## <a name="schemas"></a>Esquemas

Para uma descrição detalhada do esquema de registos de diagnóstico de nível superior, consulte [serviços, esquemas e categorias de Registos de Diagnóstico Azure](../../../azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Principais propriedades do esquema de registo de entrega

Estas propriedades são específicas do esquema de registo de entrega de chaves.

|Nome|Descrição|
|---|---|
|keyId|A identificação da chave solicitada.|
|teclaType|Pode ser um dos seguintes valores: "Claro" (sem encriptação), "FairPlay", "PlayReady" ou "Widevine".|
|nome de política|O nome do Gestor de Recursos Azure da apólice.|
|tokenType|O tipo simbólico.|
|statusMessage|A mensagem de estado.|

### <a name="example"></a>Exemplo

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

>[!NOTE]
> Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]

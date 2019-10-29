---
title: Envio em lote de saída na grade de eventos do Azure IoT Edge | Microsoft Docs
description: Envio em lote de saída na grade de eventos em IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7d6b83354baf3db5ddb65f94fee1c3dce2dcca94
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992460"
---
# <a name="output-batching"></a>Envio em lote de saída

A grade de eventos tem suporte para entregar mais de um evento em uma única solicitação de entrega. Esse recurso torna possível aumentar a taxa de transferência de entrega geral sem pagar as sobrecargas HTTP por solicitação. O envio em lote é desativado por padrão e pode ser ativado por assinatura.

> [!WARNING]
> A duração máxima permitida para processar cada solicitação de entrega não é alterada, embora o código do assinante precise fazer mais trabalho por solicitação em lote. O tempo limite de entrega padrão é de 60 segundos.

## <a name="batching-policy"></a>Política de envio em lote

O comportamento de envio em lote da grade de eventos pode ser personalizado por assinante, ajustando as duas configurações a seguir:

* Máximo de eventos por lote

  Essa configuração define um limite superior para o número de eventos que podem ser adicionados a uma solicitação de entrega em lote.

* Tamanho de lote preferencial em kilobytes

  Esse botão é usado para controlar ainda mais o número máximo de quilobytes que podem ser enviados por solicitação de entrega

## <a name="batching-behavior"></a>Comportamento de envio em lote

* Todos ou nenhum

  A grade de eventos opera com a semântica All-ou-None. Ele não dá suporte ao sucesso parcial de uma entrega em lote. Os assinantes devem ter cuidado para solicitar o máximo de eventos por lote, pois eles podem ser tratados razoavelmente em 60 segundos.

* Envio em lote otimista

  As configurações de política de envio em lote não são limites estritos no comportamento de envio em lote e são respeitadas em uma base de melhor esforço. Com as baixas taxas de evento, muitas vezes você observará que o tamanho do lote é menor que o máximo de eventos solicitados por lote.

* O padrão é definido como OFF

  Por padrão, a grade de eventos adiciona apenas um evento a cada solicitação de entrega. A maneira de ativar o envio em lote é definir qualquer uma das configurações mencionadas anteriormente no artigo no JSON da assinatura de evento.

* Valores predefinidos

  Não é necessário especificar as configurações (máximo de eventos por lote e tamanho de lote aproximado em quilobytes bytes) ao criar uma assinatura de evento. Se apenas uma configuração for definida, a grade de eventos usará valores padrão (configuráveis). Consulte as seções a seguir para obter os valores padrão e como substituí-los.

## <a name="turn-on-output-batching"></a>Ativar o envio em lote de saída

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Configurando valores máximos permitidos

As configurações de tempo de implantação a seguir controlam o valor máximo permitido ao criar uma assinatura de evento.

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| `api:deliveryPolicyLimits:maxpreferredBatchSizeInKilobytes` | Valor máximo permitido para o botão de `PreferredBatchSizeInKilobytes`. `1033`padrão.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Valor máximo permitido para o botão de `MaxEventsPerBatch`. `50`padrão.

## <a name="configuring-runtime-default-values"></a>Configurando valores padrão de tempo de execução

As configurações de tempo de implantação a seguir controlam o valor padrão de tempo de execução de cada botão quando ele não é especificado na assinatura do evento. Para reiterar, pelo menos um botão deve ser definido na assinatura do evento para ativar o comportamento de envio em lote.

| Nome da propriedade | Descrição |
| ------------- | ----------- |
| `broker:defaultMaxBatchSizeInBytes` | Tamanho máximo da solicitação de entrega quando apenas `MaxEventsPerBatch` é especificado. `1_058_576`padrão.
| `broker:defaultMaxEventsPerBatch` | Número máximo de eventos a serem adicionados a um lote quando somente `MaxBatchSizeInBytes` for especificado. `10`padrão.

---
title: Lotação de saída em Azure Event Grid IoT Edge  Microsoft Docs
description: Lotação de saída em Rede de Eventos na Borda IoT.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841752"
---
# <a name="output-batching"></a>Criação de batches de saída

A Event Grid tem suporte para entregar mais de um evento num único pedido de entrega. Esta funcionalidade permite aumentar a entrega global sem pagar as despesas gerais http/pedido. O lote amento é desligado por padrão e pode ser ligado por subscrição.

> [!WARNING]
> A duração máxima permitida para processar cada pedido de entrega não se altera, embora o código de subscrição tenha potencialmente de fazer mais trabalho por pedido em lota. O tempo de entrega é de60 segundos.

## <a name="batching-policy"></a>Política de lotação

O comportamento de lotação da Rede de Eventos pode ser personalizado por assinante, ajustando as seguintes duas definições:

* Eventos máximos por lote

  Esta definição estabelece um limite superior ao número de eventos que podem ser adicionados a um pedido de entrega loteado.

* Tamanho do lote preferido em kilobytes

  Este botão é usado para controlar ainda mais o número máximo de quilobytes que podem ser enviados por pedido de entrega

## <a name="batching-behavior"></a>Comportamento de lotação

* Todos ou nenhum

  A Grelha de Eventos funciona com semântica total ou nenhuma. Não suporta o sucesso parcial de uma entrega de lote. Os assinantes devem ter o cuidado de pedir apenas o máximo de eventos por lote que puderem manusear razoavelmente em 60 segundos.

* Lote otimista

  As definições de política de loteamento não são limites rigorosos para o comportamento do loteamento, e são respeitadas com o melhor esforço. A taxas baixas de eventos, você observará frequentemente o tamanho do lote sendo inferior aos eventos máximos solicitados por lote.

* O padrão está definido para OFF

  Por predefinição, a Grelha de Eventos só adiciona um evento a cada pedido de entrega. A forma de ligar o lote é definir qualquer uma das definições mencionadas anteriormente no artigo no evento subscrição JSON.

* Valores predefinidos

  Não é necessário especificar ambas as definições (Eventos máximos por lote e tamanho aproximado em bytes de quilo) ao criar uma subscrição de evento. Se apenas uma definição estiver definida, a Grelha de Eventos utiliza valores predefinidos (configuráveis). Consulte as seguintes secções para os valores predefinidos e como os substituir.

## <a name="turn-on-output-batching"></a>Ligar o lote de saída

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

## <a name="configuring-maximum-allowed-values"></a>Configurar valores máximos permitidos

As seguintes definições de tempo de implementação controlam o valor máximo permitido na criação de uma subscrição de evento.

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Valor máximo permitido para o botão `PreferredBatchSizeInKilobytes`. `1033`padrão .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valor máximo permitido para o botão `MaxEventsPerBatch`. `50`padrão .

## <a name="configuring-runtime-default-values"></a>Configurar os valores padrão do tempo de execução

As seguintes definições de tempo de implementação controlam o valor predefinido de cada botão quando não é especificado na Subscrição do Evento. Para reiterar, deve ser definido pelo menos um botão na Subscrição do Evento para ligar o comportamento do loteamento.

| Nome da propriedade | Descrição |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Tamanho máximo do pedido de entrega quando apenas `MaxEventsPerBatch` é especificado. `1_058_576`padrão .
| `broker__defaultMaxEventsPerBatch` | Número máximo de eventos para adicionar a um lote quando apenas `MaxBatchSizeInBytes` é especificado. `10`padrão .

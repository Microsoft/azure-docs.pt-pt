---
title: Lote de saída em Azure Event Grid IoT Edge / Microsoft Docs
description: Lote de saída em Grade de Eventos em IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171640"
---
# <a name="output-batching"></a>Criação de batches de saída

A Event Grid tem suporte para entregar mais do que um evento num único pedido de entrega. Esta funcionalidade permite aumentar a produção global sem pagar as despesas gerais de pedido http. O lote é desligado por predefinição e pode ser ligado por subscrição.

> [!WARNING]
> A duração máxima permitida para processar cada pedido de entrega não muda, mesmo que o código do assinante tenha potencialmente de fazer mais trabalho por pedido em lotado. O prazo de entrega é de 60 segundos.

## <a name="batching-policy"></a>Política de loteamento

O comportamento de loteamento da Grelha de Eventos pode ser personalizado por subscritor, ajustando as seguintes duas definições:

* Eventos máximos por lote

  Esta definição estabelece um limite superior para o número de eventos que podem ser adicionados a um pedido de entrega em lote.

* Tamanho do lote preferido em quilobytes

  Este botão é usado para controlar ainda mais o número máximo de quilobytes que podem ser enviados por pedido de entrega

## <a name="batching-behavior"></a>Comportamento de loteamento

* Tudo ou nenhum

  A Grade de Eventos funciona com semântica total ou nenhuma. Não suporta o sucesso parcial de uma entrega de lote. Os assinantes devem ter o cuidado de pedir apenas o máximo de eventos por lote que possam manusear razoavelmente em 60 segundos.

* Lote otimista

  As definições da política de loteamento não são limites rigorosos no comportamento do lote, e são respeitados com o melhor esforço. A baixas taxas de evento, você frequentemente observará o tamanho do lote sendo inferior aos eventos máximos solicitados por lote.

* O padrão está definido para OFF

  Por predefinição, a Grade de Eventos apenas adiciona um evento a cada pedido de entrega. A forma de ligar o lote é definir qualquer uma das definições mencionadas anteriormente no artigo na subscrição do evento JSON.

* Valores predefinidos

  Não é necessário especificar ambas as definições (Eventos máximos por lote e tamanho do lote aproximado em bytes de quilo) ao criar uma subscrição de eventos. Se apenas uma definição for definida, a Grelha de Eventos utiliza valores predefinidos (configuráveis). Consulte as seguintes secções para os valores predefinidos e como sobreponhá-las.

## <a name="turn-on-output-batching"></a>Ligue o lote de saída

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

## <a name="configuring-maximum-allowed-values"></a>Configuração dos valores máximos permitidos

As seguintes definições de tempo de implementação controlam o valor máximo permitido ao criar uma subscrição de evento.

| Nome da Propriedade | Descrição |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Valor máximo permitido para o `PreferredBatchSizeInKilobytes` botão. Predefinição `1033` .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valor máximo permitido para o `MaxEventsPerBatch` botão. Predefinição `50` .

## <a name="configuring-runtime-default-values"></a>Configurar valores de incumprimento do tempo de execução

As seguintes definições de tempo de implementação controlam o valor predefinido de cada botão quando este não está especificado na Subscrição do Evento. Para reiterar, pelo menos um botão deve ser ajustado na Subscrição do Evento para ligar o comportamento do lote.

| Nome da Propriedade | Descrição |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Tamanho máximo do pedido de entrega quando apenas `MaxEventsPerBatch` é especificado. Predefinição `1_058_576` .
| `broker__defaultMaxEventsPerBatch` | Número máximo de eventos a adicionar a um lote quando apenas `MaxBatchSizeInBytes` é especificado. Predefinição `10` .

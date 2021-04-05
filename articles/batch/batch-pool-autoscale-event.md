---
title: Evento de autoescala da piscina Azure Batch
description: Referência para o evento de autoescala da piscina Batch, que é emitida uma vez que uma escala automática de piscina é executada. O conteúdo do registo exporá a fórmula de autoescala e os resultados de avaliação para o pool.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852179"
---
# <a name="pool-autoscale-event"></a>Evento de dimensionamento automático de conjunto

 Este evento é emitido uma vez que uma escala automática de piscina é executada. O conteúdo do registo exporá a fórmula de autoescala e os resultados de avaliação para o pool.

 O exemplo a seguir mostra o corpo de um evento de escala automática de piscina para uma escala automática de piscina que falhou devido a dados de amostra insuficientes.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|String|A identificação da piscina.|
|`timestamp`|DateTime|A estampagem de tempo quando o escalonamento automático é executado.|
|`formula`|String|A fórmula definida para a escala automática.|
|`results`|String|Resultados da avaliação de todas as variáveis utilizadas na fórmula.|
|[`error`](#error)|Tipo Complexo|O erro detalhado para a escala automática.|

###  <a name="error"></a><a name="error"></a> erro

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`code`|String|Um identificador para o erro de escala automático. Os códigos são invariantes e destinam-se a ser consumidos programáticamente.|
|`message`|String|Uma mensagem que descreve o erro de escala automático, destinado a ser exibido numa interface de utilizador.|
|`values`|Matriz|Lista de pares de valor-nome que descrevem mais detalhes do erro de escala automático.|

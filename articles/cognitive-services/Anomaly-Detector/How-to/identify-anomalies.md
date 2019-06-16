---
title: Como utilizar a API de detetor de anomalias nos seus dados de séries de tempo
description: Aprenda a detetar anomalias nos seus dados como um lote ou nos dados de transmissão em fluxo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61432345"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Como: Utilize a API de detetor de anomalias nos seus dados de séries de tempo  

O [anomalias detetor de API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) fornece dois métodos de deteção de anomalias. Pode detetar anomalias como um lote durante os tempos para a série, ou os dados são gerados através da deteção o estado de anomalias do ponto de dados mais recentes. O modelo de deteção devolve resultados de anomalias, juntamente com o valor esperado de cada ponto de dados e da anomalia superior e inferior, limites de deteção. Pode utilizar estes valores para visualizar o intervalo de valores de normais e anomalias nos dados.

## <a name="anomaly-detection-modes"></a>Modos de deteção de anomalias 

A API de detetor de anomalias fornece modos de Deteção: batch e de transmissão em fluxo.

> [!NOTE]
> O pedido seguinte que URLs devem ser combinadas com o ponto final adequado para a sua subscrição. Por exemplo: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Deteção de batch

Para detetar anomalias ao longo de um lote de pontos de dados ao longo de um intervalo de tempo especificado, utilize o URI do pedido seguinte com os seus dados de séries de tempo: 

`/timeseries/entire/detect`. 

Ao enviar seus dados de séries de tempo ao mesmo tempo, a API irá gerar um modelo com a série completa e analisar cada ponto de dados com o mesmo.  

### <a name="streaming-detection"></a>Deteção de transmissão em fluxo

De forma contínua detetar anomalias nos dados de transmissão em fluxo, utilize o URI do pedido seguinte com o seu ponto de dados mais recentes: 

`/timeseries/last/detect'`. 

Ao enviar novos pontos de dados, como os gera, pode monitorizar os dados em tempo real. Um modelo será gerado com os pontos de dados, enviar e a API irá determinar se o ponto mais recente na série de tempo é uma anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Ajustar os limites de deteção de anomalias inferior e superior

Por predefinição, os limites superiores e inferiores de deteção de anomalias são calculados usando `expectedValue`, `upperMargin`, e `lowerMargin`. Se precisar de limites diferentes, é recomendado aplicar uma `marginScale` para `upperMargin` ou `lowerMargin`. Os limites serão calculados da seguinte forma:

|Limite  |Cálculo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Os exemplos seguintes mostram um resultado de API de detetor de anomalias em sensitivities diferentes.

### <a name="example-with-sensitivity-at-99"></a>Exemplo com sensibilidade em 99

![Sensibilidade predefinido](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exemplo com sensibilidade em 95

![Sensibilidade de 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exemplo com sensibilidade em 85

![Sensibilidade 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Próximos Passos

* [O que é a API de detetor de anomalias?](../overview.md)
* [Quickstart: Detetar anomalias nos seus dados de séries de tempo com a API de REST de detetor de anomalias](../quickstarts/detect-data-anomalies-csharp.md)
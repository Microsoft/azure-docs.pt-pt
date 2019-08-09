---
title: Como usar a API do detector de anomalias em seus dados de série temporal
titleSuffix: Azure Cognitive Services
description: Saiba como detectar anomalias em seus dados como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 4b97c52ec0ed076e1ab8aeada90f430b8ed87514
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854801"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Como: Usar a API do detector de anomalias em seus dados de série temporal  

A [API do detector](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) de anomalias fornece dois métodos de detecção de anomalias. Você pode detectar anomalias como um lote em toda a série temporal ou como seus dados são gerados detectando o status de anomalia do último ponto de dados. O modelo de detecção retorna resultados de anomalias junto com o valor esperado de cada ponto de dados e os limites de detecção de anomalias superior e inferior. Você pode usar esses valores para visualizar o intervalo de valores normais e anomalias nos dados.

## <a name="anomaly-detection-modes"></a>Modos de detecção de anomalias 

A API do detector de anomalias fornece modos de detecção: lote e streaming.

> [!NOTE]
> As URLs de solicitação a seguir devem ser combinadas com o ponto de extremidade apropriado para sua assinatura. Por exemplo: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Detecção de lote

Para detectar anomalias em um lote de pontos de dados em um determinado intervalo de tempo, use o seguinte URI de solicitação com seus dados de série temporal: 

`/timeseries/entire/detect`. 

Ao enviar seus dados de série temporal de uma vez, a API irá gerar um modelo usando a série inteira e analisará cada ponto de dados com ele.  

### <a name="streaming-detection"></a>Detecção de streaming

Para detectar continuamente anomalias em dados de streaming, use o seguinte URI de solicitação com o último ponto de dados: 

`/timeseries/last/detect'`. 

Ao enviar novos pontos de dados conforme você os gera, você pode monitorar seus dados em tempo real. Um modelo será gerado com os pontos de dados que você enviar, e a API determinará se o ponto mais recente na série temporal é uma anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Ajustando limites de detecção de anomalias inferiores e superiores

Por padrão, os limites superior e inferior para detecção de anomalias são calculados `upperMargin`usando `expectedValue`, `lowerMargin`e. Se você precisar de limites diferentes, é recomendável `marginScale` aplicar `upperMargin` um `lowerMargin`a ou. Os limites seriam calculados da seguinte maneira:

|Divisão  |Cálculo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Os exemplos a seguir mostram um resultado da API do detector de anomalias em diferentes diferenciação.

### <a name="example-with-sensitivity-at-99"></a>Exemplo com sensibilidade em 99

![Sensibilidade padrão](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exemplo com sensibilidade em 95

![sensibilidade de 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exemplo com sensibilidade em 85

![sensibilidade de 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Próximos Passos

* [O que é a API do detector de anomalias?](../overview.md)
* [Quickstart: Detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias](../quickstarts/detect-data-anomalies-csharp.md)

---
title: Como utilizar a API do Detetor de Anomalias nos dados da série de tempo
titleSuffix: Azure Cognitive Services
description: Saiba como detetar anomalias nos seus dados, quer como lote, quer em dados de streaming.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: b2cd5e32503953de874ab470ca3f9413d2b37d59
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014652"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Como: Utilizar a API do Detetor de Anomalias nos dados da série de tempo  

A [API do Detetor de Anomalias](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) fornece dois métodos de deteção de anomalias. Pode detetar anomalias como um lote ao longo da sua série de tempos, ou à medida que os seus dados são gerados através da deteção do estado de anomalia do último ponto de dados. O modelo de deteção devolve resultados de anomalias juntamente com o valor esperado de cada ponto de dados, e os limites superiores e inferiores de deteção de anomalias. pode utilizar estes valores para visualizar a gama de valores normais e anomalias nos dados.

## <a name="anomaly-detection-modes"></a>Modos de deteção de anomalias 

A API do Detetor de Anomalias fornece modos de deteção: lote e streaming.

> [!NOTE]
> Os URLs seguintes devem ser combinados com o ponto final apropriado para a sua subscrição. Por exemplo: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Deteção de lotes

Para detetar anomalias em todo um lote de pontos de dados ao longo de um determinado intervalo de tempo, utilize o seguinte pedido URI com os dados da sua série de tempo: 

`/timeseries/entire/detect`. 

Ao enviar os dados da série de tempo de uma só vez, a API gerará um modelo utilizando toda a série e analisará cada ponto de dados com ele.  

### <a name="streaming-detection"></a>Deteção de streaming

Para detetar continuamente anomalias nos dados de streaming, utilize o seguinte pedido URI com o seu último ponto de dados: 

`/timeseries/last/detect'`. 

Ao enviar novos pontos de dados à medida que os gera, pode monitorizar os seus dados em tempo real. Um modelo será gerado com os pontos de dados que envia, e a API determinará se o ponto mais recente da série-tempo é uma anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Ajustar os limites de deteção de anomalias inferiores e superiores

Por predefinição, os limites superior e inferior para deteção de anomalias são calculados utilizando `expectedValue` `upperMargin` , e . `lowerMargin` . Se necessitar de limites diferentes, recomendamos aplicar `marginScale` um a `upperMargin` ou `lowerMargin` . Os limites seriam calculados da seguinte forma:

|Fronteira  |Cálculo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Os exemplos a seguir mostram um resultado de API do Detetor de Anomalias em diferentes sensibilidades.

### <a name="example-with-sensitivity-at-99"></a>Exemplo com sensibilidade aos 99

![Sensibilidade padrão](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exemplo com sensibilidade aos 95 anos

![99 Sensibilidade](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exemplo com sensibilidade aos 85 anos

![85 Sensibilidade](../media/sensitivity_85.png)

## <a name="next-steps"></a>Passos Seguintes

* [O que é a API do Detetor de Anomalias?](../overview.md)
* [Quickstart: Detetar anomalias nos dados da sua série de tempo utilizando a API do Detetor de Anomalias](../quickstarts/detect-data-anomalies-csharp.md)

---
title: Melhores práticas com a API do Detetor de Anomalias
titleSuffix: Azure Cognitive Services
description: Aprenda sobre as melhores práticas ao detetar anomalias com a API do Detetor de Anomalias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721616"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Boas práticas para usar a API do Detetor de Anomalias

O Detetor de Anomalias API é um serviço de deteção de anomalias apátridas. A precisão e desempenho dos seus resultados pode ser impactada por:

* Como os dados da série de tempo são preparados.
* Os parâmetros DaPI do Detetor de Anomalias que foram usados.
* O número de pontos de dados no seu pedido de API. 

Use este artigo para aprender sobre as melhores práticas para usar a API obtendo os melhores resultados para os seus dados. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando utilizar o lote (inteiro) ou a última (última) deteção de anomalias de ponto

O ponto final de deteção do lote do Detetor de Anomalias permite detetar anomalias através de dados inteiros da série de horários. Neste modo de deteção, é criado e aplicado um único modelo estatístico em cada ponto do conjunto de dados. Se a sua série de tempo tiver as características abaixo, recomendamos a utilização da deteção de lotes para pré-visualizar os seus dados numa chamada API.

* Uma série de horáriosazonal, com anomalias ocasionais.
* Uma série de tempo de tendência plana, com picos/mergulhos ocasionais. 

Não recomendamos a utilização da deteção de anomalias de lote para monitorização de dados em tempo real, ou a sua utilização em dados de séries temporais que não tenham características acima. 

* A deteção de lotes cria e aplica apenas um modelo, a deteção para cada ponto é feita no contexto de séries inteiras. Se os dados da série de tempo forem para cima e para baixo sem sazonalidade, alguns pontos de mudança (dips e picos nos dados) podem ser perdidos pelo modelo. Do mesmo modo, alguns pontos de alteração menos significativos do que os mais tarde no conjunto de dados não podem ser contabilizados como significativos o suficiente para serem incorporados no modelo.

* A deteção do lote é mais lenta do que detetar o estado de anomalia do último ponto ao fazer a monitorização de dados em tempo real, devido ao número de pontos que estão a ser analisados.

Para monitorização de dados em tempo real, recomendamos a deteção do estado de anomalia do seu último ponto de dados. Ao aplicar continuamente a deteção de pontos mais recentes, a monitorização de dados de streaming pode ser feita de forma mais eficiente e precisa.

O exemplo abaixo descreve o impacto que estes modos de deteção podem ter no desempenho. A primeira imagem mostra o resultado da deteção contínua do estado da anomalia no último ponto ao longo de 28 pontos de dados previamente vistos. Os pontos vermelhos são anomalias.

![Uma imagem que mostra a deteção de anomalias usando o ponto mais recente](../media/last.png)

Abaixo está o mesmo conjunto de dados utilizando a deteção de anomalias do lote. O modelo construído para a operação tem ignorado várias anomalias, marcadas por retângulos.

![Uma imagem que mostra a deteção de anomalias utilizando o método do lote](../media/entire.png)

## <a name="data-preparation"></a>Preparação de dados

O Detetor de Anomalias API aceita dados da série de tempo formatados num objeto de pedido JSON. Uma série de tempo pode ser qualquer dado numérico registado ao longo do tempo em ordem sequencial. Pode enviar janelas dos dados da sua série de tempo para o ponto final da API do Detetor de Anomalias para melhorar o desempenho da API. O número mínimo de pontos de dados que pode enviar é de 12 pontos, e o máximo é de 8640 pontos. [A granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) é definida como a taxa a que os seus dados são amostrados. 

Os pontos de dados enviados para a API do Detetor de Anomalias devem ter um carimbo de tempo universal coordenado válido (UTC) e um valor numérico. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Se os seus dados forem recolhidos num intervalo de tempo `customInterval` não padrão, pode especificá-lo adicionando o atributo no seu pedido. Por exemplo, se a sua série for amostrada a cada 5 minutos, pode adicionar o seguinte ao seu pedido JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Pontos de dados em falta

Os pontos de dados em falta são comuns em conjuntos de dados de séries de tempo uniformemente distribuídos, especialmente aqueles com uma granularidade fina (um pequeno intervalo de amostragem. Por exemplo, os dados recolhidos a cada poucos minutos). Perder menos de 10% do número esperado de pontos nos seus dados não deve ter um impacto negativo nos resultados da deteção. Considere preencher lacunas nos seus dados com base nas suas características como substituir pontos de dados de um período anterior, interpolação linear ou uma média móvel.

### <a name="aggregate-distributed-data"></a>Dados distribuídos agregados

O Detetor de Anomalias API funciona melhor numa série de tempo distribuída uniformemente. Se os seus dados forem distribuídos aleatoriamente, deverá agregar por uma unidade de tempo, como por minuto, hora ou diariamente, por exemplo.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Deteção de anomalias em dados com padrões sazonais

Se souber que os dados da série tem um padrão sazonal (um que ocorre em intervalos regulares), pode melhorar a precisão e o tempo de resposta da API. 

Especificar um `period` quando constrói o seu pedido JSON pode reduzir a latência de deteção de anomalias em até 50%. O `period` é um inteiro que especifica aproximadamente quantos pontos de dados a série de tempo leva para repetir um padrão. Por exemplo, uma série de tempo com `period` um `7`ponto de dados por dia teria uma série de tempo `period` `7*24`com um ponto por hora (com o mesmo padrão semanal) teria um de . Se não tem a certeza dos padrões dos seus dados, não precisa especificar este parâmetro.

Para obter os `period`melhores resultados, forneça 4's de ponto de dados, mais um adicional. Por exemplo, os dados horários com um padrão semanal descrito acima devem`7 * 24 * 4 + 1`fornecer 673 pontos de dados no organismo de pedido ().

### <a name="sampling-data-for-real-time-monitoring"></a>Dados de amostragem para monitorização em tempo real

Se os seus dados de streaming forem recolhidos num curto intervalo (por exemplo, segundos ou minutos), o envio do número recomendado de pontos de dados pode exceder o número máximo permitido pela API do Detetor de Anomalias (8640 pontos de dados). Se os seus dados mostrarem um padrão sazonal estável, considere enviar uma amostra dos dados da sua série de tempo num intervalo de tempo maior, como horas. A amostragem dos seus dados desta forma também pode melhorar visivelmente o tempo de resposta da API. 

## <a name="next-steps"></a>Passos seguintes

* [O que é a API do Detetor de Anomalias?](../overview.md)
* [Quickstart: Detete anomalias nos dados da série de tempo utilizando a API DO DETETOR de Anomalias](../quickstarts/detect-data-anomalies-csharp.md)

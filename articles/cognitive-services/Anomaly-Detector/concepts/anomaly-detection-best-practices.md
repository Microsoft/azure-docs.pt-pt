---
title: Melhores práticas com a API do Detetor de Anomalias
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as melhores práticas ao detetar anomalias com a API do Detetor de Anomalias.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: mbullwin
ms.openlocfilehash: 43ccde054a9630b251aa6c206028d29c7c699316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98936195"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Melhores práticas para a utilização do Detetor de AnomaliaS API

A API do Detetor de Anomalias é um serviço apátrida de deteção de anomalias. A precisão e desempenho dos seus resultados podem ser impactados por:

* Como os seus dados da série de tempo são preparados.
* Os parâmetros de API do Detetor de Anomalias que foram usados.
* O número de pontos de dados no seu pedido de API. 

Use este artigo para aprender sobre as melhores práticas para usar a API para obter os melhores resultados para os seus dados. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando utilizar o lote (inteiro) ou a mais recente (última) deteção de anomalias de ponto

O ponto final de deteção do lote do Detetor de Anomalias permite detetar anomalias através de todos os dados da série Times. Neste modo de deteção, é criado e aplicado um único modelo estatístico a cada ponto do conjunto de dados. Se a sua série de tempos tiver as características abaixo, recomendamos a utilização da deteção de lotes para visualizar os seus dados numa chamada API.

* Uma série de tempo sazonal, com anomalias ocasionais.
* Uma série de tempo de tendência plana, com picos/mergulhos ocasionais. 

Não recomendamos a utilização de deteção de anomalias em lote para monitorização de dados em tempo real, ou a sua utilização em dados da série de tempo que não têm as características acima. 

* A deteção de lotes cria e aplica apenas um modelo, a deteção para cada ponto é feita no contexto de toda a série. Se os dados das séries de tempo aumentarem e descerem sem sazonalidade, alguns pontos de mudança (mergulhos e picos nos dados) podem ser ignorados pelo modelo. Da mesma forma, alguns pontos de alteração menos significativos do que os mais tarde no conjunto de dados podem não ser contabilizados como suficientemente significativos para serem incorporados no modelo.

* A deteção de lotes é mais lenta do que detetar o estado de anomalia do último ponto ao fazer a monitorização de dados em tempo real, devido ao número de pontos analisados.

Para monitorização de dados em tempo real, recomendamos a deteção do estado de anomalia do seu último ponto de dados apenas. Aplicando continuamente a deteção de pontos mais recentes, a monitorização de dados de streaming pode ser feita de forma mais eficiente e precisa.

O exemplo abaixo descreve o impacto que estes modos de deteção podem ter no desempenho. A primeira imagem mostra o resultado de detetar continuamente o estado de anomalia no último ponto ao longo de 28 pontos de dados previamente vistos. Os pontos vermelhos são anomalias.

![Uma imagem mostrando deteção de anomalias usando o ponto mais recente](../media/last.png)

Abaixo está o mesmo conjunto de dados usando a deteção de anomalias de lote. O modelo construído para a operação tem ignorado várias anomalias, marcadas por retângulos.

![Uma imagem mostrando deteção de anomalias usando o método do lote](../media/entire.png)

## <a name="data-preparation"></a>Preparação de dados

A API do Detetor de Anomalias aceita dados de séries de tempo formatados num objeto de pedido JSON. Uma série de tempo pode ser qualquer dado numérico registado ao longo do tempo em ordem sequencial. Pode enviar janelas dos dados da série de tempo para o ponto final da API do Detetor de Anomalias para melhorar o desempenho da API. O número mínimo de pontos de dados que pode enviar é de 12, e o máximo é de 8640 pontos. [A granularidade](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) é definida como a taxa em que os seus dados são recolhidos. 

Os pontos de dados enviados para a API do Detetor de Anomalias devem ter uma timetamp universal coordenada válida (UTC) e um valor numérico. 

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

Se os seus dados forem recolhidos num intervalo de tempo não normalizado, pode especi-lo adicionando o `customInterval` atributo no seu pedido. Por exemplo, se a sua série for amostrada a cada 5 minutos, pode adicionar o seguinte ao seu pedido JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Pontos de dados em falta

Os pontos de dados em falta são comuns em conjuntos de dados de séries de tempo distribuídos uniformemente, especialmente aqueles com uma granularidade fina (Um pequeno intervalo de amostragem. Por exemplo, os dados recolhidos a cada poucos minutos). Perder menos de 10% do número esperado de pontos nos seus dados não deve ter um impacto negativo nos seus resultados de deteção. Considere preencher lacunas nos seus dados com base nas suas características como substituir pontos de dados de um período anterior, interpolação linear ou uma média móvel.

### <a name="aggregate-distributed-data"></a>Dados distribuídos agregados

A API do Detetor de Anomalias funciona melhor numa série de tempo distribuída uniformemente. Se os seus dados forem distribuídos aleatoriamente, deve agregar por uma unidade de tempo, como por minuto, hora ou dia-a-dia.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Deteção de anomalias em dados com padrões sazonais

Se souber que os dados da sua série de tempo têm um padrão sazonal (que ocorre em intervalos regulares), pode melhorar a precisão e o tempo de resposta da API. 

Especificar um `period` quando constrói o seu pedido JSON pode reduzir a latência de deteção de anomalias em até 50%. É `period` um número inteiro que especifica aproximadamente quantos pontos de dados a série de tempo leva para repetir um padrão. Por exemplo, uma série de tempo com um ponto de dados por dia teria um `period` `7` como, e uma série de tempo com um ponto por hora (com o mesmo padrão semanal) teria um `period` de  `7*24` . Se não tem a certeza dos padrões dos seus dados, não precisa de especificar este parâmetro.

Para obter os melhores resultados, forneça quatro `period` pontos de dados, mais um adicional. Por exemplo, os dados de hora com um padrão semanal, tal como descrito acima, devem fornecer 673 pontos de dados no organismo de pedido `7 * 24 * 4 + 1` ().

### <a name="sampling-data-for-real-time-monitoring"></a>Dados de amostragem para monitorização em tempo real

Se os seus dados de streaming forem recolhidos num curto intervalo (por exemplo, segundos ou minutos), o envio do número recomendado de pontos de dados pode exceder o número máximo permitido pela API do Detetor de Anomalias (8640 pontos de dados). Se os seus dados mostram um padrão sazonal estável, considere enviar uma amostra dos dados da sua série de tempo num intervalo de tempo maior, como horas. A amostragem dos seus dados desta forma também pode melhorar visivelmente o tempo de resposta da API. 

## <a name="next-steps"></a>Passos seguintes

* [O que é a API do Detetor de Anomalias?](../overview.md)
* [Quickstart: Detetar anomalias nos dados da série de tempo usando o Detetor de Anomalias](../quickstarts/client-libraries.md)

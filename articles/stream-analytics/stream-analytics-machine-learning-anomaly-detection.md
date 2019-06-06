---
title: Deteção de anomalias no Azure Stream Analytics
description: Este artigo descreve como utilizar o Azure Stream Analytics e o Azure Machine Learning em conjunto, para detetar anomalias.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: fdf98a0c0c40010bb55955b54dc7b04db8e199f5
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493267"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Deteção de anomalias no Azure Stream Analytics

Disponível na cloud e no Azure IoT Edge, Azure Stream Analytics oferece incorporado do machine learning capacidades de deteção de anomalias com base que podem ser usadas para monitorar as anomalias mais que duas: temporários e persistentes. Com o **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint** funções, pode executar a deteção de anomalias diretamente na sua tarefa de Stream Analytics.

Os modelos de machine learning partem do princípio de uma série de tempo de amostragem de maneira uniforme. Se não for uniforme a série de tempo, pode inserir um passo de agregação com uma janela em cascata antes de chamar a deteção de anomalias.

As operações do machine learning não suportam as tendências de sazonalidade ou número multi correlações.

## <a name="model-accuracy-and-performance"></a>Precisão de modelo e o desempenho

Em geral, precisão do modelo é aprimorado com mais dados na janela deslizante. Os dados na janela deslizante especificado são tratados como parte do respetivo intervalo normal de valores para esse período de tempo. O modelo de apenas os histórico de eventos de janela deslizante para verificar se o evento atual é anómalo. À medida que passa a janela deslizante, os valores antigos são expulsos da formação do modelo.

As funções funcionam através do estabelecimento de um determinado normal, com base no que eles já viu até agora. Valores atípicos são identificados por comparação com o estabelecido normal, dentro do nível de confiança. O tamanho da janela deve basear-se nos eventos mínimo necessários para preparar o modelo para o comportamento normal, para que quando ocorrer uma anomalia, seria capaz de reconhecê-lo.

Tenha em atenção que o tempo de resposta do modelo aumenta com o tamanho de histórico, porque tem de comparar em relação a um número mais elevado de eventos passados. Recomenda-se para incluir apenas o número necessário de eventos para um melhor desempenho.

As lacunas na série de tempo podem ser o resultado do modelo não receber eventos em determinados pontos no tempo. Esta situação é manipulada pela análise de Stream com o imputation. O tamanho de histórico, bem como uma duração de tempo, para a mesma janela deslizante é utilizado para calcular a média da taxa em que os eventos são esperados para chegar.

## <a name="spike-and-dip"></a>Pico e dip

Anomalias temporárias num fluxo de eventos de série de tempo são conhecidas como picos e quedas. Picos e descidas podem ser monitorizadas com o operador de Machine Learning, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalias de pico e dip](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor que o primeiro, a classificação calculada para a menor oscilação provavelmente não é significativa suficiente em comparação com a classificação para o pico primeiro no nível de confiança especificado. Pode tentar reduzir a definição de nível de confiança do modelo para capturar essas anomalias. No entanto, se começar a obter demasiados alertas, pode utilizar um intervalo mais alto de confiança.

A seguinte consulta de exemplo parte do princípio de uma taxa uniforme de entrada de um evento por segundo numa janela deslizante de 2 minutos com um histórico de eventos de 120. A instrução SELECT final extrai e devolve a pontuação e anomalias Estado com um nível de confiança de 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Alterar ponto

Anomalias persistentes num fluxo de eventos de série de tempo são as alterações na distribuição de valores no fluxo de eventos, como alterações no nível e tendências. No Stream Analytics, essas anomalias são detetadas com o Machine Learning com base [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operador.

Alterações persistentes duram muito mais do que os picos e quedas e podem indicar o evento ou eventos catastróficos. Alterações persistentes não são normalmente visíveis literal, mas pode ser detectadas com o **AnomalyDetection_ChangePoint** operador.

A imagem seguinte é um exemplo de uma alteração de nível:

![Exemplo de anomalias de nível de alteração](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

A imagem seguinte é um exemplo de uma alteração de tendência:

![Exemplo de anomalias de alteração de tendência](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

A seguinte consulta de exemplo parte do princípio de uma taxa uniforme de entrada de um evento por segundo numa janela deslizante de 20 minutos com um tamanho de histórico de eventos de 1200. A instrução SELECT final extrai e devolve a pontuação e anomalias Estado com um nível de confiança de 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Deteção de anomalias com machine learning no Azure Stream Analytics

O vídeo seguinte demonstra como detectar uma anomalia em tempo real com as funções do machine learning no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


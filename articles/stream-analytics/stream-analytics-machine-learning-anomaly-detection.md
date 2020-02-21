---
title: Deteção de anomalias no Azure Stream Analytics
description: Este artigo descreve como utilizar o Azure Stream Analytics e o Azure Machine Learning em conjunto, para detetar anomalias.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525537"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Deteção de anomalias no Azure Stream Analytics

Disponível tanto na nuvem como no Azure IoT Edge, o Azure Stream Analytics oferece capacidades de deteção de anomalias baseadas em aprendizagem automática incorporadas que podem ser usadas para monitorizar as duas anomalias mais frequentes: temporárias e persistentes. Com as funções **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint,** pode efetuar a deteção de anomalias diretamente no seu trabalho de Stream Analytics.

Os modelos de aprendizagem automática assumem uma série de tempo uniformemente amostrada. Se a série de tempo não for uniforme, pode inserir um passo de agregação com uma janela caindo antes de chamar a deteção de anomalias.

As operações de aprendizagem automática não suportam tendências de sazonalidade ou correlações multi-variadas neste momento.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Deteção de anomalias utilizando machine learning no Azure Stream Analytics

O vídeo seguinte demonstra como detetar uma anomalia em tempo real usando funções de machine learning no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Comportamento do modelo

Geralmente, a precisão do modelo melhora com mais dados na janela deslizante. Os dados da janela deslizante especificada são tratados como parte da sua gama normal de valores para esse período de tempo. O modelo apenas considera o histórico do evento sobre a janela deslizante para verificar se o evento atual é anómalo. À medida que a janela deslizante se move, os valores antigos são despejados do treino do modelo.

As funções funcionam estabelecendo um certo normal com base no que viram até agora. Os outliers são identificados comparando com o normal estabelecido, dentro do nível de confiança. O tamanho da janela deve basear-se nos eventos mínimos necessários para treinar o modelo para o comportamento normal, de modo a que, quando ocorrer uma anomalia, possa reconhecê-lo.

O tempo de resposta do modelo aumenta com o tamanho da história porque precisa de se comparar com um maior número de eventos anteriores. Recomenda-se que inclua apenas o número necessário de eventos para um melhor desempenho.

Lacunas na série de tempo podem ser resultado de o modelo não receber eventos em determinados pontos do tempo. Esta situação é tratada pelo Stream Analytics utilizando a lógica de imputação. O tamanho da história, bem como a duração do tempo, para a mesma janela deslizante é usado para calcular a taxa média a que os eventos devem chegar.

Um gerador de anomalias disponível [aqui](https://aka.ms/asaanomalygenerator) pode ser usado para alimentar um Hub iot com dados com diferentes padrões de anomalia. Um trabalho ASA pode ser configurado com estas funções de deteção de anomalias para ler a partir deste Hub Iot e detetar anomalias.

## <a name="spike-and-dip"></a>Espigão e mergulho

Anomalias temporárias num fluxo de eventos de série seletiva são conhecidas como picos e mergulhos. Os picos e os mergulhos podem ser monitorizados utilizando o operador baseado em Machine Learning, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalia de pico e mergulho](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor do que o primeiro, a pontuação calculada para o pico menor provavelmente não é significativa o suficiente em comparação com a pontuação para o primeiro pico dentro do nível de confiança especificado. Pode tentar diminuir o nível de confiança do modelo para detetar tais anomalias. No entanto, se começar a receber muitos alertas, pode usar um intervalo de confiança mais elevado.

A seguinte consulta de exemplo pressupõe uma taxa de entrada uniforme de um evento por segundo numa janela deslizante de 2 minutos com um histórico de 120 eventos. A declaração final SELECT extrai e produz o estado da pontuação e anomalia com um nível de confiança de 95%.

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

## <a name="change-point"></a>Ponto de mudança

Anomalias persistentes num fluxo de eventos de série seletiva são mudanças na distribuição de valores no fluxo de eventos, como alterações de nível e tendências. No Stream Analytics, tais anomalias são detetadas utilizando o operador [de AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) baseado em Machine Learning.

As mudanças persistentes duram muito mais do que picos e mergulhos e podem indicar eventos catastróficos. As alterações persistentes não são geralmente visíveis a olho nu, mas podem ser detetadas com o operador **AnomalyDetection_ChangePoint.**

A imagem que se segue é um exemplo de uma mudança de nível:

![Exemplo de anomalia de mudança de nível](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

A imagem que se segue é um exemplo de uma mudança de tendência:

![Exemplo de anomalia de mudança de tendência](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

A seguinte consulta de exemplo pressupõe uma taxa de entrada uniforme de um evento por segundo numa janela deslizante de 20 minutos com um tamanho histórico de 1200 eventos. A declaração final SELECT extrai e produz o estado da pontuação e anomalia com um nível de confiança de 80%.

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

## <a name="performance-characteristics"></a>Características de desempenho

O desempenho destes modelos depende do tamanho da história, duração da janela, carga do evento e se é utilizada a divisão do nível de função. Esta secção discute estas configurações e fornece amostras para como sustentar taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

* **Tamanho da história** - Estes modelos executam linearmente com **o tamanho da história.** Quanto mais tempo o tamanho da história, mais tempo os modelos demoram a marcar um novo evento. Isto porque os modelos comparam o novo evento com cada um dos eventos anteriores no amortecedor de história.
* **Duração** da janela - A **duração** da janela deve refletir o tempo que demora a receber tantos eventos conforme especificado pelo tamanho da história. Sem tantos eventos na janela, o Azure Stream Analytics imputaria valores em falta. Assim, o consumo de CPU é uma função do tamanho da história.
* Carga de **eventos** - Quanto maior for a carga do **evento,** mais trabalho é realizado pelos modelos, o que impacta o consumo de CPU. O trabalho pode ser dimensionado tornando-o embaraçosamente paralelo, assumindo que faz sentido que a lógica empresarial use mais divisórias de entrada.
* A divisão do **nível de função** - Divisão do nível de **função** é feita utilizando ```PARTITION BY``` dentro da chamada da função de deteção de anomalias. Este tipo de partição adiciona uma sobrecarga, uma vez que o Estado precisa de ser mantido para vários modelos ao mesmo tempo. A divisão do nível de função é usada em cenários como a divisão do nível do dispositivo.

### <a name="relationship"></a>Relação
O tamanho da história, a duração da janela e a carga total do evento estão relacionados da seguinte forma:

janelaDuração (em ms) = 1000 * historySize / (Total De entrada eventos por sec / Contagem de partição de entrada)

Ao dividir a função por dispositivoId, adicione "PARTITION BY deviceId" à chamada da função de deteção de anomalias.

### <a name="observations"></a>Observações
O quadro seguinte inclui as observações de um único nó (6 SU) para o caso não dividido:

| Tamanho da história (eventos) | Duração da janela (Ms) | Total de eventos de entrada por seg |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

O quadro seguinte inclui as observações de um único nó (6 SU) para o caso dividido:

| Tamanho da história (eventos) | Duração da janela (Ms) | Total de eventos de entrada por seg | Contagem de dispositivos |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

O código de amostra para executar as configurações não divisórias acima está localizado no [repo streaming à escala](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) de amostras azure. O código cria um trabalho de análise de fluxo sem divisão de nível de função, que usa o Event Hub como entrada e saída. A carga de entrada é gerada através de clientes de teste. Cada evento de entrada é um documento json de 1KB. Os eventos simulam um dispositivo IoT que envia dados JSON (para dispositivos até 1K). O tamanho da história, a duração da janela e a carga total do evento são variados em mais de 2 divisórias de entrada.

> [!Note]
> Para obter uma estimativa mais precisa, personalize as amostras para se adaptar ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificação de estrangulamentos
Utilize o painel Metrics no seu trabalho azure Stream Analytics para identificar estrangulamentos no seu pipeline. Reveja os eventos de **entrada/saída** para a produção e ["Watermark Delay" ou "Watermark Delay"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **Eventos atrasados** para ver se o trabalho está a acompanhar a taxa de entrada. Para as métricas do Event Hub, procure **pedidos throttled** e ajuste as Unidades threshold em conformidade. Para as métricas Cosmos DB, reveja **a Max consumiu RU/s por gama** de teclas de partição em Âmbito de Entrada para garantir que as suas gamas-chave de partição são uniformemente consumidas. Para O Azure SQL DB, monitorize **o Log IO** e **o CPU**.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


---
title: Deteção de anomalias em Azure Stream Analytics
description: Este artigo descreve como usar a Azure Stream Analytics e Azure Machine Learning juntos para detetar anomalias.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: c57a3920dac3e18e248109fafdf61fdfa871c54d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123715"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Deteção de anomalias em Azure Stream Analytics

Disponível tanto na nuvem como no Azure IoT Edge, o Azure Stream Analytics oferece capacidades incorporadas de deteção de anomalias baseadas em aprendizagem automática que podem ser usadas para monitorizar as duas anomalias mais frequentes: temporárias e persistentes. Com as **funções AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint,** pode realizar a deteção de anomalias diretamente no seu trabalho stream Analytics.

Os modelos de aprendizagem automática assumem uma série de tempo uniformemente amostrada. Se a série de tempo não for uniforme, pode inserir um passo de agregação com uma janela de tropeçar antes de chamar a deteção de anomalias.

As operações de machine learning não suportam tendências de sazonalidade ou correlações multi-variantes neste momento.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Deteção de anomalias utilizando machine learning em Azure Stream Analytics

O vídeo que se segue demonstra como detetar uma anomalia em tempo real utilizando funções de machine learning no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Comportamento do modelo

Geralmente, a precisão do modelo melhora com mais dados na janela deslizante. Os dados na janela deslizante especificada são tratados como parte da sua gama normal de valores para esse período de tempo. O modelo apenas considera o histórico do evento sobre a janela deslizante para verificar se o evento atual é anómalo. À medida que a janela deslizante se move, os valores antigos são despejados do treino do modelo.

As funções funcionam estabelecendo um certo normal com base no que viram até agora. Os outliers são identificados comparando com o normal estabelecido, dentro do nível de confiança. O tamanho da janela deve basear-se nos eventos mínimos necessários para treinar o modelo para o comportamento normal, de modo a que, quando uma anomalia ocorre, seja capaz de reconhecê-lo.

O tempo de resposta do modelo aumenta com o tamanho da história porque precisa de se comparar com um maior número de eventos passados. Recomenda-se apenas incluir o número necessário de eventos para um melhor desempenho.

As lacunas nas séries tempor feiras podem ser o resultado de o modelo não receber eventos em determinados pontos do tempo. Esta situação é tratada pela Stream Analytics utilizando a lógica de imputação. O tamanho da história, bem como a duração do tempo, para a mesma janela de deslizamento é usado para calcular a taxa média a que os eventos são esperados.

Um gerador de anomalia disponível [aqui](https://aka.ms/asaanomalygenerator) pode ser usado para alimentar um Iot Hub com dados com diferentes padrões de anomalia. Um trabalho asa pode ser configurado com estas funções de deteção de anomalias para ler a partir deste Iot Hub e detetar anomalias.

## <a name="spike-and-dip"></a>Espigão e mergulho

Anomalias temporárias num fluxo de eventos de séries de tempo são conhecidas como picos e mergulhos. Os picos e os mergulhos podem ser monitorizados utilizando o operador baseado em Machine Learning, [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalia de pico e dip](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor do que o primeiro, a pontuação calculada para o pico menor provavelmente não é significativa o suficiente em comparação com a pontuação para o primeiro pico dentro do nível de confiança especificado. Pode tentar diminuir o nível de confiança do modelo para detetar tais anomalias. No entanto, se começar a receber demasiados alertas, pode usar um intervalo de confiança mais elevado.

A seguinte consulta de exemplo pressupõe uma taxa de entrada uniforme de um evento por segundo numa janela deslizante de 2 minutos com uma história de 120 eventos. A declaração final SELECT extrai e produz o estado de pontuação e anomalia com um nível de confiança de 95%.

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

Anomalias persistentes num fluxo de eventos de séries de tempo são alterações na distribuição de valores no fluxo de eventos, como alterações de nível e tendências. No Stream Analytics, tais anomalias são detetadas utilizando o operador [de AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) baseado em Machine Learning.

As alterações persistentes duram muito mais tempo do que picos e mergulhos e podem indicar eventos catastróficos. As alterações persistentes não são geralmente visíveis a olho nu, mas podem ser detetadas com o **operador AnomalyDetection_ChangePoint.**

A imagem a seguir é um exemplo de uma mudança de nível:

![Exemplo de anomalia de mudança de nível](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

A imagem a seguir é um exemplo de uma mudança de tendência:

![Exemplo de anomalia de mudança de tendência](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

A seguinte consulta de exemplo pressupõe uma taxa de entrada uniforme de um evento por segundo numa janela deslizante de 20 minutos com um tamanho histórico de 1200 eventos. A declaração final SELECT extrai e produz o estado de pontuação e anomalia com um nível de confiança de 80%.

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

O desempenho destes modelos depende do tamanho da história, da duração da janela, da carga do evento e da utilização da partição do nível da função. Esta secção discute estas configurações e fornece amostras para como sustentar as taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

* **Tamanho da história** - Estes modelos funcionam linearmente com **o tamanho da história.** Quanto mais tempo o tamanho da história, mais tempo os modelos demoram a marcar um novo evento. Isto porque os modelos comparam o novo evento com cada um dos eventos anteriores no tampão de história.
* **Duração da janela** - A **duração da janela** deve refletir o tempo que leva para receber o número de eventos especificados pelo tamanho da história. Sem tantos eventos na janela, o Azure Stream Analytics imputaria valores em falta. Assim, o consumo de CPU é uma função do tamanho da história.
* **Carga de eventos** - Quanto maior for a carga do **evento,** mais trabalho é realizado pelos modelos, que impacta o consumo de CPU. O trabalho pode ser escalonado tornando-o embaraçoso paralelo, assumindo que faz sentido que a lógica empresarial use mais divisórias de entrada.
* **Partição do nível da função**  -  **A partição do nível da função** é feita ```PARTITION BY``` utilizando-se dentro da chamada de função de deteção de anomalias. Este tipo de partição adiciona uma sobrecarga, uma vez que o estado precisa de ser mantido para vários modelos ao mesmo tempo. A partição do nível da função é usada em cenários como a partição do nível do dispositivo.

### <a name="relationship"></a>Relação
O tamanho da história, a duração da janela e a carga total do evento estão relacionados da seguinte forma:

janelaDuration (em ms) = 1000 * historySize / (Total de Eventos de Entrada por Seg / Contagem de Partição de Entrada)

Ao dividir a função por deviceId, adicione "PARTITION BY deviceId" à chamada de função de deteção de anomalias.

### <a name="observations"></a>Observações
O quadro seguinte inclui as observações de produção de um único nó (6 SU) para o caso não dividido:

| Tamanho da história (eventos) | Duração da janela (ms) | Total de eventos de entrada por seg |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2.200 |
| 600 | 728 | 1,650 |
| 6000 | 10,910 | 1100 |

O quadro seguinte inclui as observações de produção de um único nó (6 SU) para o caso dividido:

| Tamanho da história (eventos) | Duração da janela (ms) | Total de eventos de entrada por seg | Contagem de dispositivos |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1100 | 10 |
| 600 | 10,910 | 1100 | 10 |
| 6000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6000 | 2,181,819 | <550 | 100 |

O código de amostra para executar as configurações não partitioned acima está localizado no [repo de streaming à escala](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) de amostras de Azure. O código cria um trabalho de análise de fluxo sem divisórias de nível de função, que usa o Event Hub como entrada e saída. A carga de entrada é gerada utilizando clientes de teste. Cada evento de entrada é um documento json 1KB. Os eventos simulam um dispositivo IoT enviando dados JSON (para dispositivos até 1K). O tamanho da história, a duração da janela e a carga total do evento são variados em 2 divisórias de entrada.

> [!Note]
> Para uma estimativa mais precisa, personalize as amostras para se adaptar ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificar estrangulamentos
Utilize o painel métrica no seu trabalho Azure Stream Analytics para identificar estrangulamentos no seu oleoduto. **Reveja os eventos de entrada/saída** para produção e ["Atraso de marca de água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **eventos retrospados** para ver se o trabalho está a acompanhar a taxa de entrada. Para as métricas do Event Hub, procure **pedidos de aceleração** e ajuste as Unidades limiares em conformidade. Para as métricas da Cosmos DB, reveja **max consumiu RU/s por intervalo de chaves de partição** sob a produção para garantir que as suas gamas de chaves de partição são consumidas uniformemente. Para Azure SQL DB, monitorize **Log IO** e **CPU** .

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)
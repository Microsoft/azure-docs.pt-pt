---
title: Deteção de anomalias no Azure Stream Analytics
description: Este artigo descreve como utilizar o Azure Stream Analytics e o Azure Machine Learning em conjunto, para detetar anomalias.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612343"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Deteção de anomalias no Azure Stream Analytics

Disponível na cloud e no Azure IoT Edge, Azure Stream Analytics oferece incorporado do machine learning capacidades de deteção de anomalias com base que podem ser usadas para monitorar as anomalias mais que duas: temporários e persistentes. Com o **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint** funções, pode executar a deteção de anomalias diretamente na sua tarefa de Stream Analytics.

Os modelos de machine learning partem do princípio de uma série de tempo de amostragem de maneira uniforme. Se não for uniforme a série de tempo, pode inserir um passo de agregação com uma janela em cascata antes de chamar a deteção de anomalias.

As operações do machine learning não suportam as tendências de sazonalidade ou número multi correlações neste momento.

## <a name="model-behavior"></a>Comportamento de modelo

Em geral, precisão do modelo é aprimorado com mais dados na janela deslizante. Os dados na janela deslizante especificado são tratados como parte do respetivo intervalo normal de valores para esse período de tempo. O modelo de apenas os histórico de eventos de janela deslizante para verificar se o evento atual é anómalo. À medida que passa a janela deslizante, os valores antigos são expulsos da formação do modelo.

As funções funcionam através do estabelecimento de um determinado normal, com base no que eles já viu até agora. Valores atípicos são identificados por comparação com o estabelecido normal, dentro do nível de confiança. O tamanho da janela deve basear-se nos eventos mínimo necessários para preparar o modelo para o comportamento normal, para que quando ocorrer uma anomalia, seria capaz de reconhecê-lo.

Tempo de resposta do modelo aumenta com tamanho de histórico, porque tem de comparar em relação a um número mais elevado de eventos passados. Recomenda-se para incluir apenas o número necessário de eventos para um melhor desempenho.

As lacunas na série de tempo podem ser o resultado do modelo não receber eventos em determinados pontos no tempo. Esta situação é manipulada pela análise de Stream usando a lógica de imputation. O tamanho de histórico, bem como uma duração de tempo, para a mesma janela deslizante é utilizado para calcular a média da taxa em que os eventos são esperados para chegar.

Um gerador de anomalias disponível [aqui](https://aka.ms/asaanomalygenerator) pode ser usado para alimentar um Iot Hub com os dados com padrões de anomalias diferentes. Uma tarefa ASA pode ser configurada com estas funções de deteção de anomalias para ler este Iot Hub e detetar anomalias.

## <a name="spike-and-dip"></a>Pico e dip

Anomalias temporárias num fluxo de eventos de série de tempo são conhecidas como picos e quedas. Picos e descidas podem ser monitorizadas com o operador de Machine Learning, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalias de pico e dip](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor que o primeiro, a classificação calculada para a menor oscilação provavelmente não é significativa suficiente em comparação com a classificação para o pico primeiro no nível de confiança especificado. Pode tentar diminuir o nível de confiança do modelo para detectar essas anomalias. No entanto, se começar a obter demasiados alertas, pode utilizar um intervalo mais alto de confiança.

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

## <a name="performance-characteristics"></a>Características de desempenho

O desempenho desses modelos depende do tamanho de histórico, a duração da janela, a carga do evento, e se a criação de partições ao nível da função é usada. Esta seção aborda estas configurações e fornece exemplos de como manter as taxas de ingestão de 1K, 5K e 10 mil eventos por segundo.

* **Tamanho de histórico** -estes modelos realizar-se linearmente com **tamanho de histórico**. Mais tempo o tamanho de histórico, mais tempo demoram os modelos para um novo evento de pontuação. Isso ocorre porque os modelos de comparam o novo evento com cada um dos últimos eventos na memória intermédia de histórico.
* **Duração da janela** – a **duração da janela** deve refletir o tempo que demora para receber os eventos conforme especificado pelo tamanho do histórico. Sem que muitos eventos na janela, o Azure Stream Analytics seria impute valores em falta. Por conseguinte, o consumo da CPU é uma função do tamanho do histórico.
* **Carga do evento** - quanto maior a **carga do evento**, quanto mais trabalho que é executada por modelos, que afeta o consumo da CPU. A tarefa pode ser ampliada, tornando constrangedoramente paralelas, partindo do princípio de que faz sentido para a lógica de negócio utilizar mais de entrada partições.
* **Criação de partições ao nível da função** - **criação de partições ao nível da função** é feito utilizando ```PARTITION BY``` dentro da chamada de função de deteção de anomalias. Este tipo de criação de partições adiciona uma sobrecarga, como o estado precisa ser mantido para vários modelos ao mesmo tempo. Criação de partições ao nível da função é utilizada em cenários como a criação de partições ao nível do dispositivo.

### <a name="relationship"></a>Relação
O tamanho de histórico, a duração de janela e a carga total de eventos estão relacionados da seguinte forma:

windowDuration (em ms) = 1000 * historySize / (Total entrada eventos por segundo / número de partições de entrada)

Quando a função de particionamento por deviceId, adicione "PARTICIONAR por deviceId" para a chamada de função de deteção de anomalias.

### <a name="observations"></a>Observações
A tabela seguinte inclui as observações de débito para um único nó (6 SU) para o caso de não-particionada:

| Tamanho de histórico (eventos) | Duração da janela (ms) | Eventos de entrada total por seg |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

A tabela seguinte inclui as observações de débito para um único nó (6 SU) para o caso particionado:

| Tamanho de histórico (eventos) | Duração da janela (ms) | Eventos de entrada total por seg | Contagem de dispositivos |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

Código de exemplo para executar as configurações não-particionada acima está localizado na [repositório de transmissão em fluxo em escala](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) dos exemplos do Azure. O código cria uma tarefa do stream analytics com nenhuma função ao nível de criação de partições, que usa o Hub de eventos como entrada e saída. A carga de entrada é gerada usando clientes de teste. Cada evento de entrada é um documento de json de 1KB. Eventos de simular um dispositivo de IoT, envio de dados JSON (para até 1 de K de dispositivos). O tamanho de histórico, a duração de janela e a carga total de eventos são diversificados durante 2 partições de entrada.

> [!Note]
> Para uma estimativa mais exata, personalize os exemplos para se ajustarem ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificar afunilamentos
Utilize o painel de métricas na sua tarefa do Azure Stream Analytics para identificar afunilamentos no seu pipeline. Revisão **eventos de entrada/saída** para o débito e ["Marca d'água atraso"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **eventos pendentes** para ver se a tarefa é acompanhar a taxa de entrada. Para métricas do Hub de eventos, procure **pedidos limitados** e ajustar as unidades de limiar em conformidade. Para métricas do Cosmos DB, reveja **Max RU/s consumidas por intervalo de chaves de partição** em débito para garantir que sua partição intervalos de chaves uniformemente são consumidos. Para a BD do SQL do Azure, monitorize **Log IO** e **CPU**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Deteção de anomalias com machine learning no Azure Stream Analytics

O vídeo seguinte demonstra como detectar uma anomalia em tempo real com as funções do machine learning no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


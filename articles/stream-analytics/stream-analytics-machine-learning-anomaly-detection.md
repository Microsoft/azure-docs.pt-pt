---
title: Detecção de anomalias no Azure Stream Analytics
description: Este artigo descreve como utilizar o Azure Stream Analytics e o Azure Machine Learning em conjunto, para detetar anomalias.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e29ac6671d71ea02b432c9843541796984737c8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459606"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detecção de anomalias no Azure Stream Analytics

Disponível tanto na nuvem quanto no Azure IoT Edge, o Azure Stream Analytics oferece recursos internos de detecção de anomalias baseados em Machine Learning que podem ser usados para monitorar as duas anomalias que ocorrem com mais frequência: temporária e persistente. Com as funções **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint** , você pode executar a detecção de anomalias diretamente em seu trabalho de Stream Analytics.

Os modelos de aprendizado de máquina assumem uma série temporal de amostra uniforme. Se a série temporal não for uniforme, você poderá inserir uma etapa de agregação com uma janela em cascata antes de chamar a detecção de anomalias.

As operações de Machine Learning não dão suporte a tendências sazonalidade ou correlações entre variate no momento.

## <a name="model-behavior"></a>Comportamento do modelo

Em geral, a precisão do modelo melhora com mais dados na janela deslizante. Os dados na janela deslizante especificada são tratados como parte de seu intervalo normal de valores para esse período de tempo. O modelo só considera o histórico de eventos na janela deslizante para verificar se o evento atual é anormal. À medida que a janela deslizante é movida, os valores antigos são removidos do treinamento do modelo.

As funções operam estabelecendo um certo normal com base no que eles viram até agora. As exceções são identificadas comparando-se com o normal estabelecido, dentro do nível de confiança. O tamanho da janela deve ser baseado nos eventos mínimos necessários para treinar o modelo para comportamento normal para que, quando ocorrer uma anomalia, seja capaz de reconhecê-lo.

O tempo de resposta do modelo aumenta com o tamanho do histórico porque ele precisa comparar com um número maior de eventos anteriores. É recomendável incluir apenas o número necessário de eventos para melhorar o desempenho.

As lacunas na série temporal podem ser um resultado do modelo não receber eventos em determinados pontos no tempo. Essa situação é manipulada por Stream Analytics usando a lógica imputação. O tamanho do histórico, bem como uma duração de tempo, para a mesma janela deslizante é usado para calcular a taxa média na qual os eventos devem chegar.

Um gerador de anomalias disponível [aqui](https://aka.ms/asaanomalygenerator) pode ser usado para alimentar um hub IOT com dados com padrões de anomalia diferentes. Um trabalho ASA pode ser configurado com essas funções de detecção de anomalias para ler esse Hub IOT e detectar anomalias.

## <a name="spike-and-dip"></a>Pico e DIP

Anomalias temporárias em um fluxo de eventos de série temporal são conhecidas como picos e DIPs. Picos e DIPs podem ser monitorados usando o operador baseado em Machine Learning, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalias de pico e DIP](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor do que o primeiro, a pontuação computada para o pico menor provavelmente não será significativa o suficiente em comparação com a Pontuação do primeiro pico no nível de confiança especificado. Você pode tentar diminuir o nível de confiança do modelo para detectar tais anomalias. No entanto, se você começar a obter muitos alertas, poderá usar um intervalo de confiança mais alto.

A consulta de exemplo a seguir pressupõe uma taxa de entrada uniforme de um evento por segundo em uma janela deslizante de 2 minutos com um histórico de 120 eventos. A instrução SELECT final extrai e gera a pontuação e o status de anomalia com um nível de confiança de 95%.

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

## <a name="change-point"></a>Ponto de alteração

Anomalias persistentes em um fluxo de eventos de série temporal são alterações na distribuição de valores no fluxo de eventos, como alterações de nível e tendências. Em Stream Analytics, essas anomalias são detectadas usando o operador de [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) com base em Machine Learning.

As alterações persistentes duram muito mais do que picos e DIPs e podem indicar eventos catastróficos. As alterações persistentes geralmente não são visíveis para os olhos de nu, mas podem ser detectadas com o operador de **AnomalyDetection_ChangePoint** .

A imagem a seguir é um exemplo de uma alteração de nível:

![Exemplo de anomalia de alteração de nível](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

A imagem a seguir é um exemplo de uma alteração de tendência:

![Exemplo de anomalia de alteração de tendência](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

A consulta de exemplo a seguir pressupõe uma taxa de entrada uniforme de um evento por segundo em uma janela deslizante de 20 minutos com um tamanho de histórico de 1200 eventos. A instrução SELECT final extrai e gera a pontuação e o status de anomalia com um nível de confiança de 80%.

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

O desempenho desses modelos depende do tamanho do histórico, da duração da janela, da carga do evento e se o particionamento no nível da função é usado. Esta seção aborda essas configurações e fornece exemplos de como sustentar taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

* **Tamanho do histórico** -esses modelos são executados linearmente com o **tamanho do histórico**. Quanto maior o tamanho do histórico, mais longos os modelos levam para pontuar um novo evento. Isso ocorre porque os modelos comparam o novo evento com cada um dos eventos passados no buffer de histórico.
* **Duração da janela** – a **duração da janela** deve refletir o tempo necessário para receber tantos eventos quantos forem especificados pelo tamanho do histórico. Sem esses muitos eventos na janela, Azure Stream Analytics imputar valores ausentes. Portanto, o consumo de CPU é uma função do tamanho do histórico.
* **Carga de eventos** -quanto maior a **carga de eventos**, mais trabalho é executado pelos modelos, o que afeta o consumo da CPU. O trabalho pode ser escalado horizontalmente, tornando-o embaraçosamente paralelo, pressupondo que faça sentido para que a lógica comercial use mais partições de entrada.
* O **particionamento de nível de função** - particionamento de nível de **função** é feito usando ```PARTITION BY``` na chamada de função de detecção de anomalias. Esse tipo de particionamento adiciona uma sobrecarga, pois o estado precisa ser mantido para vários modelos ao mesmo tempo. O particionamento no nível de função é usado em cenários como o particionamento no nível do dispositivo.

### <a name="relationship"></a>Relação
O tamanho do histórico, a duração da janela e a carga total do evento são relacionados da seguinte maneira:

windowDuration (em MS) = 1000 * historySize/(total de eventos de entrada por segundo/contagem de partições de entrada)

Ao particionar a função por DeviceID, adicione "PARTITION BY DeviceID" à chamada de função de detecção de anomalias.

### <a name="observations"></a>Observações
A tabela a seguir inclui as observações de taxa de transferência para um único nó (6 SU) para o caso não particionado:

| Tamanho do histórico (eventos) | Duração da janela (MS) | Total de eventos de entrada por segundo |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1\.650 |
| 6,000 | 10.910 | 1100 |

A tabela a seguir inclui as observações de taxa de transferência para um único nó (6 SU) para o caso particionado:

| Tamanho do histórico (eventos) | Duração da janela (MS) | Total de eventos de entrada por segundo | Contagem de dispositivos |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1\.091 | 1100 | 10 |
| 600 | 10.910 | 1100 | 10 |
| 6,000 | 218.182 | <550 | 10 |
| 60 | 21.819 | 550 | 100 |
| 600 | 218.182 | 550 | 100 |
| 6,000 | 2\.181.819 | <550 | 100 |

O código de exemplo para executar as configurações não particionadas acima está localizado no [repositório streaming em escala](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) dos exemplos do Azure. O código cria um trabalho do Stream Analytics sem particionamento de nível de função, que usa o Hub de eventos como entrada e saída. A carga de entrada é gerada usando clientes de teste. Cada evento de entrada é um documento JSON 1 KB. Eventos simulam um dispositivo IoT enviando dados JSON (para dispositivos com até 1K). O tamanho do histórico, a duração da janela e a carga total do evento são variados em 2 partições de entrada.

> [!Note]
> Para obter uma estimativa mais precisa, personalize os exemplos para se ajustar ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificando afunilamentos
Use o painel métricas em seu trabalho de Azure Stream Analytics para identificar afunilamentos em seu pipeline. Examine os **eventos de entrada/saída** para obter a taxa de transferência e o ["atraso da marca d' água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **eventos de registro** posterior para ver se o trabalho está acompanhando a taxa de entrada. Para as métricas do hub de eventos, procure **solicitações limitadas** e ajuste as unidades de limite de acordo. Para Cosmos DB métricas, examine o **intervalo máximo consumido de ru/s por chave de partição** em taxa de transferência para garantir que os intervalos de chaves de partição sejam consumidos uniformemente. Para o banco de BD SQL do Azure, monitore e **/s de log** e **CPU**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Detecção de anomalias usando o aprendizado de máquina no Azure Stream Analytics

O vídeo a seguir demonstra como detectar uma anomalia em tempo real usando funções de aprendizado de máquina no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


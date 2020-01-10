---
title: Dimensionar funções de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como dimensionar Stream Analytics trabalhos que usam Machine Learning funções, configurando o particionamento e as unidades de fluxo.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dedffab0b17515cedc54569d5debf6d29b273644
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458741"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Dimensionar seu trabalho de Stream Analytics com funções Azure Machine Learning Studio (clássicas)

Este artigo discute como dimensionar com eficiência Azure Stream Analytics trabalhos que usam Azure Machine Learning funções. Para obter informações sobre como dimensionar Stream Analytics trabalhos em geral, consulte o artigo [dimensionando trabalhos](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função de Azure Machine Learning no Stream Analytics?

Uma função Machine Learning no Stream Analytics pode ser usada como uma chamada de função regular na linguagem de consulta Stream Analytics. Em segundo plano, no entanto, essas chamadas de função são realmente Azure Machine Learning solicitações de serviço Web.

Você pode melhorar a taxa de transferência de Machine Learning solicitações de serviço Web por meio de "envio em lote" de várias linhas na mesma chamada à API de serviço Web. Esse agrupamento é chamado de mini-lote. Para obter mais informações, consulte [Azure Machine Learning Studio (clássico) serviços Web](../machine-learning/studio/consume-web-services.md). O suporte para Azure Machine Learning Studio (clássico) no Stream Analytics está em versão prévia.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar um trabalho de Stream Analytics com Machine Learning funções

Há dois parâmetros para configurar a função de Machine Learning usada pelo seu trabalho de Stream Analytics:

* Tamanho do lote das chamadas de função Machine Learning.
* O número de unidades de streaming (SUs) provisionadas para o trabalho de Stream Analytics.

Para determinar os valores apropriados para o SUs, decida se deseja otimizar a latência do trabalho de Stream Analytics ou a taxa de transferência de cada SU. O SUs sempre pode ser adicionado a um trabalho para aumentar a taxa de transferência de uma consulta de Stream Analytics bem particionada. O SUs adicional aumenta o custo da execução do trabalho.

Determine a *tolerância* de latência para seu trabalho de Stream Analytics. Aumentar o tamanho do lote aumentará a latência de suas solicitações de Azure Machine Learning e a latência do trabalho de Stream Analytics.

Aumentar o tamanho do lote permite que o trabalho de Stream Analytics processe **mais eventos** com o **mesmo número** de Machine Learning solicitações de serviço da Web. O aumento da latência de serviço Web de Machine Learning geralmente é sublinear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais econômico para um serviço Web Machine Learning em qualquer situação em questão. O tamanho de lote padrão para solicitações de serviço Web é 1000. Você pode alterar esse tamanho padrão usando a [API REST do Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente do PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Depois de decidir sobre um tamanho de lote, você pode definir o número de unidades de streaming (SUs), com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre unidades de streaming, consulte [Stream Analytics trabalhos de escala](stream-analytics-scale-jobs.md).

Cada 6 SUs Obtém 20 conexões simultâneas com o serviço Web Machine Learning. No entanto, um trabalho SU e 3 trabalhos SU recebem 20 conexões simultâneas.  

Se seu aplicativo gerar 200.000 eventos por segundo e o tamanho do lote for 1000, a latência do serviço Web resultante será de 200 ms. Essa taxa significa que cada conexão pode fazer cinco solicitações para o serviço Web Machine Learning a cada segundo. Com 20 conexões, o trabalho de Stream Analytics pode processar eventos 20.000 em eventos de 200 MS e 100.000 em um segundo.

Para processar eventos 200.000 por segundo, o trabalho de Stream Analytics precisa de 40 conexões simultâneas, que vão para 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho de Stream Analytics para o ponto de extremidade do serviço Web Machine Learning – cada 6 SUs tem 20 conexões simultâneas para Machine Learning serviço Web no máximo.

![Dimensionar Stream Analytics com funções de Machine Learning dois exemplos de trabalho](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Dimensionar Stream Analytics com funções de Machine Learning dois exemplos de trabalho")

Em geral, ***B*** para o tamanho do lote, ***L*** para a latência do serviço Web no tamanho do lote B em milissegundos, a taxa de transferência de um trabalho de Stream Analytics com ***N*** SUS é:

![Stream Analytics de escala com fórmula de funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics de escala com fórmula de funções de Machine Learning")

Você também pode configurar o ' máximo de chamadas simultâneas ' no serviço Web do Machine Learning. É recomendável definir esse parâmetro para o valor máximo (200 no momento).

Para obter mais informações sobre essa configuração, consulte o [artigo de dimensionamento para Machine Learning Web Services](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo – Análise de Sentimento
O exemplo a seguir inclui um trabalho Stream Analytics com a função de Machine Learning de análise de sentimentos, conforme descrito no [tutorial de integração do Stream Analytics Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é uma consulta particionada totalmente simples seguida pela função de **sentimentos** , conforme mostrado no exemplo a seguir:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vamos examinar a configuração necessária para criar um trabalho Stream Analytics, que faz a análise de sentimentos de tweets a uma taxa de 10.000 tweets por segundo.

Usando um SU, seria possível que essa Stream Analytics o trabalho manipule o tráfego? O trabalho pode acompanhar a entrada usando o tamanho de lote padrão de 1000. A latência padrão da análise de sentimentos Machine Learning serviço Web (com um tamanho de lote padrão de 1000) não cria mais que um segundo de latência.

A latência **geral** ou de ponta a ponta do trabalho Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada nesse Stream Analytics trabalho, *especialmente* as chamadas de função Machine Learning. Com um tamanho de lote de 1000, uma taxa de transferência de 10.000 eventos leva cerca de 10 solicitações para o serviço Web. Mesmo com um SU, há conexões simultâneas suficientes para acomodar esse tráfego de entrada.

Se a taxa de eventos de entrada aumentar por 100x, o trabalho Stream Analytics precisará processar tweets 1 milhão por segundo. Há duas opções para realizar a escala aumentada:

1. Aumente o tamanho do lote.
2. Particione o fluxo de entrada para processar os eventos em paralelo.

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, você precisará provisionar mais SUs para ter mais solicitações simultâneas de serviço Web de Machine Learning. Esse número maior de SUs aumenta o **custo**do trabalho.

Vamos examinar o dimensionamento usando as seguintes medidas de latência para cada tamanho de lote:

| Latência | Tamanho do batch |
| --- | --- |
| 200 ms | 1000-lotes de eventos ou abaixo |
| 250 ms | 5\.000-lotes de eventos |
| 300 ms | 10.000-lotes de eventos |
| 500 ms | 25.000-lotes de eventos |

1. Usando a primeira opção (**não** Provisionando mais o SUS). O tamanho do lote pode ser aumentado para **25.000**. O aumento do tamanho do lote dessa maneira permitirá que o trabalho processe eventos 1 milhão com 20 conexões simultâneas com o serviço Web Machine Learning (com uma latência de 500 MS por chamada). Portanto, a latência adicional do trabalho de Stream Analytics devido às solicitações de função de sentimentos em relação às solicitações de serviço Web do Machine Learning será aumentada de **200 MS** para **500 MS**. No entanto, o tamanho do lote **não pode** ser aumentado infinitamente, pois os serviços web do Machine Learning exigem que o tamanho da carga de uma solicitação seja de 4 MB ou menor, e o tempo limite das solicitações de serviço web após 100 segundos de operação.
1. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço Web de 200-MS, a cada 20 conexões simultâneas com o serviço Web seria capaz de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Portanto, para processar eventos 1 milhão por segundo, o trabalho precisaria de 60 SUs. Em comparação com a primeira opção, Stream Analytics trabalho faria mais solicitações em lote de serviço Web, gerando um custo maior.

Abaixo está uma tabela para a taxa de transferência do trabalho de Stream Analytics para diferentes tamanhos de SUs e de lote (em número de eventos por segundo).

| tamanho do lote (latência ML) | 500 (200 ms) | 1\.000 (200 MS) | 5\.000 (250 MS) | 10.000 (300 MS) | 25.000 (500 MS) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5000 |20,000 |30,000 |50 000 |
| **3 SUs** |2,500 |5000 |20,000 |30,000 |50 000 |
| **6 SUs** |2,500 |5000 |20,000 |30,000 |50 000 |
| **12 SUs** |5000 |10,000 |40,000 |60,000 |100.000 |
| **18 SUs** |7\.500 |15 000 |60,000 |90,000 |150,000 |
| **24 SUs** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50 000 |200,000 |300,000 |500,000 |

Agora, você já deve ter uma boa compreensão de como Machine Learning funções no Stream Analytics funcionam. É provável que você também compreenda que os Stream Analytics trabalhos de "pull" de fontes de dados e cada "pull" retorna um lote de eventos para o trabalho de Stream Analytics processar. Como esse modelo de pull afeta o Machine Learning solicitações de serviço Web?

Normalmente, o tamanho do lote que definimos para Machine Learning funções não será exatamente divisível pelo número de eventos retornados por cada trabalho de Stream Analytics "pull". Quando isso ocorre, o serviço Web Machine Learning é chamado com lotes "parciais". O uso de lotes parciais evita a incorrência de sobrecarga de latência de trabalho adicional na União de eventos de pull para pull.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramento relacionadas à função
Na área monitor de um trabalho de Stream Analytics, são adicionadas três métricas adicionais relacionadas à função. Eles são **solicitações de função**, **eventos de função** e solicitações de **função com falha**, conforme mostrado no gráfico abaixo.

![Dimensionar Stream Analytics com métricas de funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Dimensionar Stream Analytics com métricas de funções de Machine Learning")

Os são definidos da seguinte maneira:

**Solicitações de função**: o número de solicitações de função.

**Eventos de função**: o número de eventos nas solicitações de função.

**Solicitações de função com falha**: o número de solicitações de função com falha.

## <a name="key-takeaways"></a>Principais argumentos

Para dimensionar um trabalho de Stream Analytics com funções Machine Learning, considere os seguintes fatores:

1. A taxa de eventos de entrada.
2. A latência tolerada para o trabalho de Stream Analytics em execução (e, portanto, o tamanho do lote das solicitações de serviço Web do Machine Learning).
3. O SUs provisionado Stream Analytics e o número de Machine Learning solicitações de serviço da Web (os custos adicionais relacionados à função).

Uma consulta Stream Analytics totalmente particionada foi usada como exemplo. Se uma consulta mais complexa for necessária, o [Fórum de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) será um excelente recurso para obter ajuda adicional da equipe de Stream Analytics.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Stream Analytics, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

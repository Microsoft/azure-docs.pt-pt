---
title: Funções de aprendizagem automática em escala em Azure Stream Analytics
description: Este artigo descreve como dimensionar os trabalhos do Stream Analytics que utilizam funções de Machine Learning, configurando unidades de partição e streaming.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: c03b9fbd06b0a9bc4a70b0fb3a78bc9ce136b704
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835585"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Escala o seu trabalho de Stream Analytics com funções azure machine learning studio (clássico)

> [!TIP]
> É altamente recomendado utilizar [UDFs de Aprendizagem Automática Azure](machine-learning-udf.md) em vez do Azure Machine Learning Studio (clássico) UDF para melhorar o desempenho e a fiabilidade.

Este artigo discute como escalar eficientemente os trabalhos do Azure Stream Analytics que utilizam funções de Aprendizagem Automática Azure. Para obter informações sobre como escalar os empregos da Stream Analytics em geral, consulte o artigo Trabalhos de [escala.](stream-analytics-scale-jobs.md)

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função de Aprendizagem automática Azure no Stream Analytics?

Uma função de Machine Learning no Stream Analytics pode ser usada como uma chamada de função regular na linguagem de consulta Stream Analytics. Nos bastidores, no entanto, estas chamadas de função são na verdade pedidos de Web Service de Aprendizagem automática Azure.

Pode melhorar a produção de pedidos de serviço web Machine Learning através de "loting" de várias linhas na mesma chamada API do serviço web. Este agrupamento é chamado de mini-lote. Para mais informações, consulte [o Azure Machine Learning Studio (clássico) Web Services](../machine-learning/studio/consume-web-services.md). O suporte para o Azure Machine Learning Studio (clássico) no Stream Analytics está em pré-visualização.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configure um trabalho de Stream Analytics com funções de Machine Learning

Existem dois parâmetros para configurar a função de Machine Learning utilizada pelo seu trabalho de Stream Analytics:

* Tamanho do lote das chamadas da função Machine Learning.
* O número de Unidades de Streaming (UsS) prevista para o trabalho de Stream Analytics.

Para determinar os valores adequados para as SUs, decida se gostaria de otimizar a latência do trabalho da Stream Analytics ou a entrada de cada SU. As SUs podem sempre ser adicionadas a um trabalho para aumentar a entrada de uma consulta de Stream Analytics bem dividida. As USS adicionais aumentam o custo de funcionamento do trabalho.

Determine a *tolerância* à latência para o seu trabalho em Stream Analytics. Aumentar o tamanho do lote aumentará a latência dos seus pedidos de Aprendizagem automática Azure e a latência do trabalho de Stream Analytics.

O aumento do tamanho do lote permite que o trabalho do Stream Analytics processe **mais eventos** com o **mesmo número** de pedidos de serviço web machine learning. O aumento da latência do serviço web machine learning é geralmente sublinear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais rentável para um serviço web machine learning em qualquer situação. O tamanho do lote padrão para pedidos de serviço web é 1000. Pode alterar este tamanho predefinido utilizando o [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Uma vez decidido sobre um tamanho de lote, pode definir o número de unidades de streaming (UsS), com base no número de eventos que a função precisa de processar por segundo. Para obter mais informações sobre unidades de streaming, consulte trabalhos à [escala Stream Analytics.](stream-analytics-scale-jobs.md)

A cada 6 SUs obtêm 20 ligações simultâneas ao serviço web machine learning. No entanto, 1 emprego na UA e 3 postos de trabalho na UA obtêm 20 ligações simultâneas.  

Se a sua aplicação gerar 200.000 eventos por segundo, e o tamanho do lote for de 1000, então a latência do serviço web resultante é de 200 ms. Esta tarifa significa que cada ligação pode fazer cinco pedidos para o serviço web machine learning a cada segundo. Com 20 ligações, o trabalho da Stream Analytics pode processar 20.000 eventos em 200 ms e 100.000 eventos num segundo.

Para processar 200.000 eventos por segundo, o trabalho da Stream Analytics precisa de 40 conexões simultâneas, que saem para 12 US. O diagrama seguinte ilustra os pedidos do trabalho do Stream Analytics para o ponto final do serviço web machine learning – Cada 6 SUs tem 20 ligações simultâneas ao serviço web Machine Learning no máximo.

![Scale Stream Analytics com funções de aprendizagem automática dois exemplos de trabalho](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Scale Stream Analytics com funções de aprendizagem automática dois exemplos de trabalho")

Em geral, ***B*** para o tamanho do lote, ***L*** para a latência do serviço web no tamanho do lote B em milissegundos, a entrada de um trabalho stream analytics com ***N*** SUs é:

![Scale Stream Analytics com Fórmula de Funções de Aprendizagem automática](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Scale Stream Analytics com Fórmula de Funções de Aprendizagem automática")

Também pode configurar as "chamadas max simultâneas" no serviço web machine learning. Recomenda-se definir este parâmetro no valor máximo (200 atualmente).

Para obter mais informações sobre esta definição, reveja o artigo de [Escala para Serviços Web de Aprendizagem Automática](../machine-learning/studio/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Exemplo – Análise de Sentimentos
O exemplo seguinte inclui um trabalho de Stream Analytics com a função de análise de sentimento machine learning, como descrito no tutorial de [integração](stream-analytics-machine-learning-integration-tutorial.md)de machine learning stream analytics .

A consulta é uma simples consulta totalmente dividida seguida pela função **de sentimento,** como mostra o seguinte exemplo:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vamos examinar a configuração necessária para criar um trabalho de Stream Analytics, que faz análise de sentimento de tweets a uma taxa de 10.000 tweets por segundo.

Usando 1 SU, este trabalho da Stream Analytics pode lidar com o tráfego? O trabalho pode acompanhar a entrada utilizando o tamanho padrão do lote de 1000. A latência padrão do serviço web de análise de sentimentos Machine Learning (com um tamanho padrão de lote de 1000) não cria mais do que um segundo de latência.

A latência **geral** ou de ponta a ponta do trabalho do Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada neste trabalho de Stream Analytics, *especialmente* as chamadas da função Machine Learning. Com um lote de 1000, uma entrada de 10.000 eventos leva cerca de 10 pedidos para o serviço web. Mesmo com uma SU, existem ligações simultâneas suficientes para acomodar este tráfego de entrada.

Se a taxa de eventos de entrada aumentar em 100x, então o trabalho do Stream Analytics precisa processar 1.000.000 tweets por segundo. Existem duas opções para realizar a escala aumentada:

1. Aumente o tamanho do lote.
2. Partição do fluxo de entrada para processar os eventos em paralelo.

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, terá de fornecer mais US para ter pedidos de serviço web machine learning mais simultâneos. Este maior número de US, aumenta o **custo**do emprego.

Vamos olhar para a escala utilizando as seguintes medidas de latência para cada tamanho do lote:

| Latência | Tamanho do lote |
| --- | --- |
| 200 ms | Lotees de 1000 eventos ou abaixo |
| 250 ms | Lotes de 5.000 eventos |
| 300 ms | Lotees de 10.000 eventos |
| 500 ms | Lotees de 25.000 eventos |

1. Utilizando a primeira opção **(não** disponibilizando mais US). O tamanho do lote pode ser aumentado para **25.000**. O aumento do tamanho do lote desta forma permitirá que o trabalho processe 1.000.000 eventos com 20 conexões simultâneas ao serviço web machine learning (com uma latência de 500 ms por chamada). Assim, a latência adicional do trabalho do Stream Analytics devido aos pedidos de função de sentimento contra os pedidos de serviço web machine learning seria aumentada de **200 ms** para **500 ms**. No entanto, o tamanho do lote **não pode** ser aumentado infinitamente, uma vez que os serviços web machine learning exigem que o tamanho da carga útil de um pedido seja de 4 MB ou menor, e o serviço web solicita tempo após 100 segundos de funcionamento.
1. Utilizando a segunda opção, o tamanho do lote é deixado em 1000, com latência do serviço web de 200 ms, cada 20 ligações simultâneas ao serviço web seriam capazes de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Assim, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 US. Em comparação com a primeira opção, o trabalho do Stream Analytics faria mais pedidos de lote de serviçoweb, gerando por sua vez um custo acrescido.

Abaixo está uma tabela para a entrada do trabalho stream analytics para diferentes SUs e tamanhos de lote (em número de eventos por segundo).

| tamanho do lote (latência ML) | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2.500 |5000 |20 000 |30,000 |50 000 |
| **3 SUs** |2.500 |5000 |20 000 |30,000 |50 000 |
| **6 SUs** |2.500 |5000 |20 000 |30,000 |50 000 |
| **12 SUs** |5000 |10,000 |40.000 |60 000 |100 000 |
| **18 SUs** |7.500 |15 000 |60 000 |90.000 |150.000 |
| **24 SUs** |10,000 |20 000 |80.000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50 000 |200,000 |300,000 |500 000 |

Por esta altura, já deve ter uma boa compreensão de como funciona o Machine Learning no Stream Analytics. Provavelmente também compreende que os trabalhos do Stream Analytics "retiram" dados de fontes de dados e cada "pull" devolve um lote de eventos para o trabalho do Stream Analytics processar. Como é que este modelo de puxar afeta os pedidos de serviço web machine learning?

Normalmente, o tamanho do lote que definimos para funções de Machine Learning não será exatamente divisível pelo número de eventos devolvidos por cada trabalho de Stream Analytics "pull". Quando isto ocorre, o serviço web machine learning é chamado com lotes "parciais". A utilização de lotes parciais evita incorrer em custos adicionais de latência de trabalho em eventos de acoplar de puxar a puxar.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitorização relacionadas com a função
Na área do Monitor de um trabalho de Stream Analytics, foram adicionadas três métricas adicionais relacionadas com a função. São pedidos de **FUNÇÃO, EVENTOS** **DE FUNÇÃO** e PEDIDOS DE **FUNÇÃO FALHADOS,** como indicado no gráfico abaixo.

![Análise de fluxo de escala com métricas de funções de aprendizagem automática](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Análise de fluxo de escala com métricas de funções de aprendizagem automática")

Os são definidos da seguinte forma:

**PEDIDOS DE FUNÇÃO**: O número de pedidos de função.

**EVENTOS DE FUNÇÃO**: Os números dos eventos nos pedidos de função.

PEDIDOS DE **FUNÇÃO FALHADOS**: O número de pedidos de função falhados.

## <a name="key-takeaways"></a>Principais takeaways

Para escalar um trabalho de Stream Analytics com funções de Machine Learning, considere os seguintes fatores:

1. A taxa de evento sinuoso.
2. A latência tolerada para o trabalho de Streaming Analytics em execução (e, portanto, o tamanho do lote dos pedidos de serviço web machine learning).
3. O stream analytics sUs provisionado e o número de pedidos de serviço web machine learning (os custos adicionais relacionados com a função).

Uma consulta de Stream Analytics totalmente dividida foi usada como um exemplo. Se for necessária uma consulta mais complexa, o Microsoft Q&Uma página de [perguntas para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) é um ótimo recurso para obter ajuda adicional da equipa stream analytics.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Stream Analytics, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

---
title: Funciona o Estúdio de Aprendizagem de Máquinas de Escala (clássico) em Azure Stream Analytics
description: Este artigo descreve como escalar os trabalhos do Stream Analytics que utilizam funções do Machine Learning Studio (clássico), configurando unidades de partição e streaming.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: d2fe8445d41f88852c6c9d4db84f4e1b03183a2e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015537"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Escalar o seu trabalho stream analytics com funções Azure Machine Learning Studio (clássico)

> [!TIP]
> É altamente recomendado usar [UDFs de aprendizagem de máquinas Azure](machine-learning-udf.md) em vez de Azure Machine Learning Studio (clássico) UDF para melhorar o desempenho e a fiabilidade.

Este artigo discute como escalar eficientemente os trabalhos do Azure Stream Analytics que usam funções do Azure Machine Learning Studio (clássico). Para obter informações sobre como escalar os empregos stream Analytics em geral veja o artigo [Trabalhos de escala.](stream-analytics-scale-jobs.md)

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>O que é uma função studio (clássica) em Stream Analytics?

Uma função de Machine Learning Studio (clássico) em Stream Analytics pode ser usada como uma chamada de função regular na linguagem de consulta Stream Analytics. Nos bastidores, no entanto, estas chamadas de função são na verdade pedidos de Studio (clássico) do Serviço Web.

Pode melhorar a produção de pedidos de serviço web studio (clássico) "lotando" várias linhas juntas na mesma chamada de serviço web API. Este agrupamento é chamado de mini-lote. Para mais informações, consulte [o Azure Machine Learning Studio (clássico) Web Services](../machine-learning/classic/consume-web-services.md). O suporte para o Studio (clássico) em Stream Analytics está em pré-visualização.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Configure um trabalho stream analytics com funções studio (clássicas)

Existem dois parâmetros para configurar a função Studio (clássica) usada pelo seu trabalho stream Analytics:

* Tamanho do lote das chamadas de função Studio (clássico).
* O número de Unidades de Streaming (SUs) previstas para o trabalho stream analytics.

Para determinar os valores adequados para as SUs, decida se pretende otimizar a latência do trabalho stream Analytics ou a produção de cada SU. As SUs podem ser sempre adicionadas a um trabalho para aumentar a produção de uma consulta de Stream Analytics bem dividida. As SUs adicionais aumentam o custo de gerir o trabalho.

Determine a *tolerância* de latência para o seu trabalho stream Analytics. Aumentar o tamanho do lote aumentará a latência dos seus pedidos studio (clássicos) e a latência do trabalho stream Analytics.

Aumentar o tamanho do lote permite ao trabalho stream Analytics processar **mais eventos** com o **mesmo número** de pedidos de serviço web Studio (clássico). O aumento da latência do serviço web Studio (clássico) é geralmente sublinear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais eficiente para um serviço web Studio (clássico) em qualquer situação. O tamanho predefinido do lote para pedidos de serviço web é de 1000. Pode alterar este tamanho predefinido utilizando a [API stream Analytics REST](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Uma vez decidido o tamanho do lote, pode definir o número de unidades de streaming (SUs), com base no número de eventos que a função precisa de processar por segundo. Para obter mais informações sobre unidades de streaming, consulte [os trabalhos de escala stream Analytics](stream-analytics-scale-jobs.md).

Cada 6 SUs obtém 20 ligações simultâneas ao serviço web Studio (clássico). No entanto, 1 emprego de SU e 3 empregos de SU obtêm 20 ligações simultâneas.  

Se a sua aplicação gerar 200.000 eventos por segundo, e o tamanho do lote for de 1000, então a latência do serviço web resultante é de 200 ms. Esta taxa significa que cada ligação pode fazer cinco pedidos para o serviço web Studio (clássico) a cada segundo. Com 20 ligações, o trabalho stream Analytics pode processar 20.000 eventos em 200 ms e 100.000 eventos num segundo.

Para processar 200.000 eventos por segundo, o trabalho stream Analytics precisa de 40 ligações simultâneas, que saem para 12 SUs. O diagrama seguinte ilustra os pedidos do trabalho stream Analytics para o ponto final do serviço web Studio (clássico) – Cada 6 SUs tem 20 ligações simultâneas ao serviço web Studio (clássico) no máximo.

![Scale Stream Analytics com Estúdio (clássico) Funções dois exemplos de trabalho](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Scale Stream Analytics com Estúdio (clássico) Funções dois exemplos de trabalho")

Em geral, ***B*** para o tamanho do lote, ***L*** para a latência do serviço web no tamanho do lote B em milissegundos, o resultado de um trabalho stream Analytics com ***SUs N*** é:

![Scale Stream Analytics com Fórmula de Funções de Estúdio (clássico)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Scale Stream Analytics com Fórmula de Funções de Estúdio (clássico)")

Também pode configurar as chamadas 'max simultâneos' no serviço web Studio (clássico). Recomenda-se definir este parâmetro para o valor máximo (200 atualmente).

Para obter mais informações sobre este cenário, reveja o [artigo de Escala para Machine Learning Studio (clássico) Web Services](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Exemplo – Análise de Sentimentos
O exemplo a seguir inclui um trabalho stream Analytics com a função de análise de sentimento Studio (clássico), como descrito no [tutorial de integração de machine learning (clássico) stream Analytics Machine Learning Studio ( clássico).](stream-analytics-machine-learning-integration-tutorial.md)

A consulta é uma consulta simples e totalmente dividida seguida pela função de **sentimento,** como mostra o exemplo seguinte:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vamos examinar a configuração necessária para criar um trabalho stream Analytics, que faz a análise de sentimento de tweets a uma taxa de 10.000 tweets por segundo.

Usando 1 SU, este trabalho da Stream Analytics pode lidar com o tráfego? O trabalho pode acompanhar a entrada utilizando o tamanho predefinido do lote de 1000. A latência padrão do serviço web Studio (clássico) de análise de sentimento (com um tamanho padrão de lote de 1000) não cria mais do que um segundo de latência.

A latência **geral** ou de ponta a ponta do stream Analytics seria normalmente de alguns segundos. Veja mais detalhadamente este trabalho de Stream Analytics, *especialmente* as chamadas de função Studio (clássicas). Com um lote de 1000, uma produção de 10.000 eventos leva cerca de 10 pedidos para o serviço web. Mesmo com um SU, existem ligações simultâneas suficientes para acomodar este tráfego de entrada.

Se a taxa de entrada de eventos aumentar em 100x, então o trabalho stream Analytics precisa processar 1.000.000 tweets por segundo. Existem duas opções para realizar a escala aumentada:

1. Aumente o tamanho do lote.
2. Partição do fluxo de entrada para processar os eventos em paralelo.

Com a primeira opção, a **latência** do emprego aumenta.

Com a segunda opção, terá de providenciar mais SUs para ter pedidos de serviço web mais simultâneos (clássicos) do Studio (clássico). Este maior número de SUs, aumenta o **custo**do emprego.

Vejamos a escala usando as seguintes medidas de latência para cada tamanho do lote:

| Latência | Tamanho do lote |
| --- | --- |
| 200 ms | Lotes de 1000 eventos ou abaixo |
| 250 ms | Lotes de 5.000 eventos |
| 300 ms | Lotes de 10.000 eventos |
| 500 ms | Lotes de 25.000 eventos |

1. Utilizando a primeira opção **(não** fornecendo mais SUs). O tamanho do lote pode ser aumentado para **25.000**. Aumentar o tamanho do lote desta forma permitirá ao trabalho processar 1.000.000 eventos com 20 ligações simultâneas ao serviço web Studio (clássico) (com uma latência de 500 ms por chamada). Assim, a latência adicional do trabalho stream Analytics devido aos pedidos de função de sentimento contra os pedidos de serviço web do Studio (clássico) seria aumentada de **200 para** **500 ms**. No entanto, o tamanho do lote **não pode** ser aumentado infinitamente, uma vez que os serviços web do Studio (clássico) requerem que o tamanho da carga útil de um pedido seja 4 MB ou menor, e o serviço web solicita tempo de operação após 100 segundos de funcionamento.
1. Utilizando a segunda opção, o tamanho do lote é deixado a 1000, com latência de serviço web de 200 ms, cada 20 ligações simultâneas ao serviço web seria capaz de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Assim, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 SUs. Em comparação com a primeira opção, o trabalho stream Analytics faria mais pedidos de lote de serviço web, gerando, por sua vez, um custo acrescido.

Abaixo está uma tabela para a produção do trabalho stream Analytics para diferentes SUs e tamanhos de lote (em número de eventos por segundo).

| tamanho do lote (latência ML) | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2.500 |5000 |20 000 |30,000 |50 000 |
| **3 SUs** |2.500 |5000 |20 000 |30,000 |50 000 |
| **6 SUs** |2.500 |5000 |20 000 |30,000 |50 000 |
| **12 SUs** |5000 |10,000 |40.000 |60 000 |100.000 |
| **18 SUs** |7.500 |15 000 |60 000 |90.000 |150.000 |
| **24 SUs** |10,000 |20 000 |80.000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50 000 |200,000 |300,000 |500 000 |

Por esta altura, já deves ter uma boa compreensão de como funciona o Studio (clássico) no Stream Analytics. Você provavelmente também entende que os trabalhos stream Analytics "retiram" dados de fontes de dados e cada "pull" devolve um lote de eventos para o trabalho stream Analytics para processar. Como é que este modelo de puxar impacta os pedidos de serviço web studio (clássicos) do serviço web?

Normalmente, o tamanho do lote que definimos para as funções studio (clássicas) não será exatamente divisível pelo número de eventos devolvidos por cada trabalho do Stream Analytics "pull". Quando isto ocorre, o serviço web Studio (clássico) é chamado com lotes "parciais". A utilização de lotes parciais evita incorrer em despesas adicionais de latência de trabalho em eventos de auxão para puxar.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitorização relacionadas com a função
Na área monitor de um trabalho stream analytics, foram adicionadas três métricas adicionais relacionadas com a função. São **PEDIDOS DE FUNÇÃO,** **EVENTOS DE FUNÇÃO** E **PEDIDOS DE FUNÇÃO FALHADOS,** como mostra o gráfico abaixo.

![Scale Stream Analytics com Métricas de Funções de Estúdio (clássico)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Scale Stream Analytics com Métricas de Funções de Estúdio (clássico)")

Os são definidos da seguinte forma:

**PEDIDOS DE FUNÇÃO**: O número de pedidos de função.

**EVENTOS DE FUNÇÃO**: Os números dos pedidos de função.

**PEDIDOS DE FUNÇÃO FALHADO :** O número de pedidos de função falhados.

## <a name="key-takeaways"></a>Principais takeaways

Para escalar um trabalho stream analytics com funções studio (clássicas), considere os seguintes fatores:

1. A taxa de entrada do evento.
2. A latência tolerada para o trabalho de Stream Analytics em execução (e, portanto, o tamanho do lote dos pedidos de serviço web studio (clássico).
3. As SUs stream analytics a forquisiadas e o número de pedidos de serviço web studio (clássicos) (os custos adicionais relacionados com a função).

Uma consulta stream analytics totalmente dividida foi usada como um exemplo. Se for necessária uma consulta mais complexa, o [Microsoft Q&Página de perguntas para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) é um ótimo recurso para obter ajuda adicional da equipa do Stream Analytics.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre stream analytics, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

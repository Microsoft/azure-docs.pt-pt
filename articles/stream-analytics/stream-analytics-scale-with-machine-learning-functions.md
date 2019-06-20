---
title: Dimensione funções de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics que utilizam funções de Machine Learning, através da configuração de unidades de criação de partições e o stream.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: db14f8240dea95eb073a0a653c2798f02fbb7c35
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67162589"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Dimensionar a sua tarefa do Stream Analytics com as funções do Azure Machine Learning Studio
É simples configurar uma tarefa do Stream Analytics e executar alguns dados de exemplo através do mesmo. O que podemos fazer quando for necessário executar a mesma tarefa com o maior volume de dados? Ela requer a compreender como configurar a tarefa do Stream Analytics, para que ele pode ser dimensionada. Neste documento, vamos nos concentrar nos aspetos de especiais de dimensionar tarefas do Stream Analytics com as funções de Machine Learning. Para obter informações sobre como dimensionar tarefas do Stream Analytics em geral, consulte o artigo [Dimensionar tarefas](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?
Uma função de Machine Learning no Stream Analytics pode ser utilizada como uma chamada de função regulares no idioma de consulta do Stream Analytics. No entanto, nos bastidores, as chamadas de função são, na verdade, os pedidos de serviço Web do Azure Machine Learning Studio. Serviços web Machine Learning suportam "criação de batches" várias linhas, a chamada de mini-batch, a mesma API chamada de serviço web, para melhorar a produtividade geral. Para obter mais informações, consulte [serviços de Web do Azure Machine Learning Studio](../machine-learning/studio/consume-web-services.md). Suporte para o Azure Machine Learning Studio no Stream Analytics está em pré-visualização.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar uma tarefa de Stream Analytics com as funções de Machine Learning
Ao configurar uma função de Machine Learning tarefa do Stream Analytics, há dois parâmetros a serem considerados, o tamanho de lote das chamadas de função do Machine Learning e as unidades de transmissão em fluxo (SUs) aprovisionadas para a tarefa do Stream Analytics. Para determinar os valores adequados para o SUs, primeiro uma decisão deve ser feita entre a latência e débito, ou seja, a latência da tarefa do Stream Analytics e débito de cada SU. SUs sempre podem ser adicionados a uma tarefa para aumentar o débito de uma consulta do Stream Analytics também particionada, embora SUs adicionais aumentam o custo de executar a tarefa.

Por isso é importante determinar a *tolerância* de latência em executar uma tarefa do Stream Analytics. Latência adicional da execução de pedidos de serviço do Azure Machine Learning naturalmente aumentarão com o tamanho de lote, o que constitui a latência de tarefa do Stream Analytics. Por outro lado, aumentar o tamanho do lote permite que a tarefa de Stream Analytics processar * mais eventos com o *mesmo número* do Machine Learning pedidos de serviço da web. Muitas vezes, o aumento da latência de serviço web Machine Learning é sublinear para o aumento do tamanho de lote, pelo que é importante considerar o tamanho do lote mais económica para um serviço web do Machine Learning em qualquer determinada situação. O tamanho de lote predefinido para o serviço web solicita é 1000 e pode ser modificado utilizando o [API de REST do Stream Analytics](https://msdn.microsoft.com/library/mt653706.aspx "API de REST do Stream Analytics") ou a [cliente de PowerShell para o Stream Análise](stream-analytics-monitor-and-manage-jobs-use-powershell.md "cliente de PowerShell para o Stream Analytics").

Depois de ter sido determinado um tamanho de lote, o número de transmissão em fluxo (SUs) de unidades podem ser determinadas, com base no número de eventos que a função precisa para processar por segundo. Para obter mais informações sobre unidades de transmissão em fluxo, consulte [tarefas de escala do Stream Analytics](stream-analytics-scale-jobs.md).

Em geral, há 20 ligações simultâneas para o serviço web do Machine Learning para todos os 6 SUs, exceto pelo fato de um de SU de tarefas e as tarefas SU 3 obtém 20 ligações em simultâneo, também.  Por exemplo, se a taxa de dados de entrada é 200 000 eventos por segundo e o tamanho do lote é deixado para o padrão de 1000 a latência de serviço web resultante com o mini-batch de 1000 eventos é de 200 ms. Isso significa que cada ligação, pode tornar cinco pedidos para o serviço web do Machine Learning num segundo. 20 ligações, a tarefa do Stream Analytics pode processar e, portanto, 100 000 eventos de 20 000 eventos em 200 ms num segundo. Portanto, para processar 200 000 eventos por segundo, a tarefa do Stream Analytics precisa 40 ligações em simultâneo, até 12 SUs. O diagrama seguinte ilustra os pedidos da tarefa do Stream Analytics para o ponto de extremidade de serviço da web de Machine Learning – todos os 6 SUs tem 20 ligações em simultâneo ao serviço web Machine Learning, no máximo.

![Dimensionar o Stream Analytics com o exemplo de tarefa duas funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "dimensionamento Stream Analytics com o exemplo de tarefa duas funções do Machine Learning")

Em geral, ***B*** para o tamanho de lote ***L*** da tarefa para a latência do serviço web com o tamanho de lote B em milissegundos, com a taxa de transferência do Stream Analytics ***N*** SUs é:

![Dimensionar o Stream Analytics com a fórmula de funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "dimensionar o Stream Analytics com a fórmula de funções do Machine Learning")

Uma consideração adicional pode ser as "máximas de chamadas simultâneas" no lado do serviço web Machine Learning, é recomendado que defina esta opção para o valor máximo (200 atualmente).

Para obter mais informações sobre esta definição, consulte a [artigo de dimensionamento para serviços Web Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo – análise de sentimentos
O exemplo seguinte inclui uma tarefa do Stream Analytics com a função de Machine Learning, a análise de sentimentos, conforme descrito no [tutorial de integração de aprendizagem do Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é um simples totalmente particionados consulta seguida de **sentimentos** funcione, conforme mostrado no exemplo a seguir:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```
Considere o seguinte cenário; com um débito de 10 000 tweets por segundo tem de ser criada uma tarefa do Stream Analytics para executar a análise de sentimentos dos tweets (eventos). Utilizar 1 SU, esta tarefa de Stream Analytics seria capaz de lidar com o tráfego? Com o tamanho de lote de padrão de 1000 a tarefa deve ser capaz de manter-se a entrada. Ainda mais a função de Machine Learning foi adicionada deve gerar não mais do que um segundo de latência, o que é gerais de latência de padrão de análise de sentimentos serviço web Machine Learning (com um tamanho de lote de padrão de 1000). A tarefa de Stream Analytics **geral** ou latência de ponto-a-ponto, normalmente, seria alguns segundos. Tenha uma visão mais detalhada nesta tarefa de Stream Analytics *especialmente* as chamadas de função do Machine Learning. Com o tamanho de lote que 1000, um débito de 10 000 eventos demorar cerca de 10 pedidos ao serviço web. Mesmo com um SU, há suficiente conexões simultâneas para acomodar este tráfego de entrada.

Se aumentar a taxa de eventos de entrada por 100 vezes, a tarefa do Stream Analytics necessita de processar tweets 1 000 000 por segundo. Existem duas opções para realizar a escala maior:

1. Aumente o tamanho de lote, ou
2. O fluxo de entrada para processar os eventos em paralelo de partição

Com a primeira opção, a tarefa **latência** aumenta.

Com a segunda opção, mais SUs precisaria ser aprovisionados e, portanto, gerar pedidos de serviço da web de Machine Learning mais em simultâneo. Isso significa que a tarefa **custo** aumenta.

Suponha que a latência da análise de sentimentos serviço web Machine Learning é 200 ms para lotes de 1000 eventos ou abaixo, 250 ms para lotes de evento de 5.000, 300 ms para lotes de eventos de 10 000 ou 500 ms para lotes de eventos de 25.000.

1. Com a primeira opção (**não** aprovisionamento mais SUs). O tamanho de lote pode ser aumentado para **25.000**. Isso por sua vez, permitiria que a tarefa processar 1 000 000 eventos com 20 ligações simultâneas para o serviço web do Machine Learning (com uma latência de 500 ms por chamada). Para que a latência adicional da tarefa de Stream Analytics devido a pedidos de função de sentimento contra os pedidos de serviço da web de Machine Learning poderia ser aumentada de **200 ms** ao **500 ms**. No entanto, tamanho do lote **não é possível** ser aumentado infinitamente a serviços web Machine Learning requer que o tamanho da carga de um pedido de ser de 4 MB ou menor tempo limite de pedidos de serviço de web após 100 segundos da operação.
2. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço da web de 200 ms, todas as 20 ligações simultâneas para o serviço web seria capazes de processar 1000 * 20 * 5 eventos = 100 000 por segundo. Portanto, para processar 1 000 000 eventos por segundo, a tarefa precisaria 60 SUs. Em comparação com a primeira opção, a tarefa do Stream Analytics tornaria o mais batch solicitações de web service, por sua vez, gerando um custo maior.

Segue-se uma tabela para o débito de tarefa do Stream Analytics para SUs diferentes e tamanhos de batch (num número de eventos por segundo).

| tamanho de lote (latência de ML) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUs** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 SUs** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300,000 |500,000 |

Agora, já deverá ter uma boa compreensão de como funcionam as funções de Machine Learning no Stream Analytics. Provavelmente também compreender que dados de tarefas do Stream Analytics "pull" de origens de dados e cada "pull" retorna um lote de eventos para a tarefa de Stream Analytics processar. Como é que esse impacto de modelo de extração o Machine Learning web pedidos de serviço?

Normalmente, o tamanho do lote a definir para as funções de Machine Learning não exatamente será divisível pelo número de eventos devolvidos por cada tarefa do Stream Analytics "pull". Quando isto ocorrer, o serviço web do Machine Learning é chamado com "parciais" lotes. Isso é feito para não incorrem em sobrecarga de latência de tarefa adicionais nos eventos de agregação de pull para extração.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitorização relacionados com a função
Na área de Monitor de uma tarefa do Stream Analytics, foram adicionadas três métricas adicionais relacionados com a função. Eles são pedidos de função, eventos de função e pedidos FALHADOS de função, conforme mostrado no gráfico abaixo.

![Dimensionar o Stream Analytics com a métrica de funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "dimensionar o Stream Analytics com a métrica de funções do Machine Learning")

O estão definidos do seguinte modo:

**PEDIDOS DE FUNÇÃO**: O número de pedidos de função.

**EVENTOS DE FUNÇÃO**: O número de eventos nos pedidos de função.

**PEDIDOS DE FUNÇÃO FALHADA**: O número de pedidos de função falhada.

## <a name="key-takeaways"></a>Principais pedidas
Para resumir os pontos principais, para dimensionar uma tarefa do Stream Analytics com as funções de Machine Learning, tem de ser considerados os seguintes itens:

1. A taxa de eventos de entrada
2. A latência tolerada para a tarefa de Stream Analytics em execução (e, portanto, o tamanho do lote dos pedidos de serviço web do Machine Learning)
3. O SUs de análise de Stream aprovisionado e o número de pedidos de serviço web Machine Learning (relacionados com a função custos adicionais)

Uma consulta do Stream Analytics totalmente particionada foi utilizada como exemplo. Se uma consulta mais complexa for necessária, o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) é um ótimo recurso para obter ajuda adicional da Equipe do Stream Analytics.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o Stream Analytics, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

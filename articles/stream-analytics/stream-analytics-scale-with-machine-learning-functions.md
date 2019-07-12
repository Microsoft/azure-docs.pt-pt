---
title: Dimensione funções de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics que utilizam funções de Machine Learning, através da configuração de unidades de criação de partições e o stream.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3d478c2421066c8347622f9064c479bb8255b112
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621755"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Dimensionar a sua tarefa do Stream Analytics com as funções do Azure Machine Learning Studio

Este artigo discute como dimensionar eficientemente a tarefas de Azure Stream Analytics que utilizam as funções do Azure Machine Learning. Para obter informações sobre como dimensionar tarefas do Stream Analytics em geral, consulte o artigo [Dimensionar tarefas](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?

Uma função de Machine Learning no Stream Analytics pode ser utilizada como uma chamada de função regulares no idioma de consulta do Stream Analytics. No entanto, nos bastidores, essas chamadas de função são, na verdade, os pedidos de serviço da Web do Azure Machine Learning.

Pode melhorar o débito de pedidos de serviço web Machine Learning "criação de batches" várias linhas em conjunto na mesma chamada de API de serviço web. Este agrupamento é chamado de um mini lote de. Para obter mais informações, consulte [serviços de Web do Azure Machine Learning Studio](../machine-learning/studio/consume-web-services.md). Suporte para o Azure Machine Learning Studio no Stream Analytics está em pré-visualização.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar uma tarefa de Stream Analytics com as funções de Machine Learning

Há dois parâmetros para configurar a função de Machine Learning utilizada pela sua tarefa do Stream Analytics:

* Tamanho do lote das chamadas de função de Machine Learning.
* O número de unidades transmissão em fluxo (SUs) aprovisionada para a tarefa do Stream Analytics.

Para determinar os valores adequados para o SUs, decida se pretende otimizar a latência de tarefa do Stream Analytics ou o débito de cada SU. SUs sempre podem ser adicionados a uma tarefa para aumentar o débito de uma consulta do Stream Analytics bem particionada. SUs adicionais de aumentar o custo de executar a tarefa.

Determinar a latência *tolerância* para a sua tarefa do Stream Analytics. Aumentar o tamanho do batch irá aumentar a latência dos seus pedidos de serviço do Azure Machine Learning e a latência de tarefa do Stream Analytics.

Aumentar o tamanho de lote permite que a tarefa de Stream Analytics processar **mais eventos** com o **mesmo número** do Machine Learning pedidos de serviço da web. O aumento da latência de serviço web Machine Learning é normalmente sublinear para o aumento do tamanho de lote. 

É importante considerar o tamanho do lote mais económica para um serviço web do Machine Learning em qualquer determinada situação. O tamanho do lote padrão para solicitações de web service é 1000. Pode alterar este tamanho predefinido a utilizar o [API de REST do Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API de REST do Stream Analytics") ou o [cliente de PowerShell para o Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Depois de decidir um tamanho de lote, pode definir o número de unidades transmissão em fluxo (SUs), com base no número de eventos que a função precisa para processar por segundo. Para obter mais informações sobre unidades de transmissão em fluxo, consulte [tarefas de escala do Stream Analytics](stream-analytics-scale-jobs.md).

Todos os 6 SUs obtém 20 ligações simultâneas para o serviço web do Machine Learning. No entanto, 1 tarefa SU e 3 tarefas SU obtém 20 ligações em simultâneo.  

Se seu aplicativo gera 200 000 eventos por segundo e o tamanho do lote é 1000, em seguida, a latência do serviço web resultante é 200 ms. Essa taxa significa que cada ligação pode fazer cinco pedidos para o serviço web do Machine Learning cada segundo. 20 ligações, a tarefa do Stream Analytics pode processar os 20 000 eventos em 200 ms e de 100.000 eventos num segundo.

Para processar 200 000 eventos por segundo, a tarefa do Stream Analytics tem de 40 ligações em simultâneo, até 12 SUs. O diagrama seguinte ilustra os pedidos da tarefa do Stream Analytics para o ponto de extremidade de serviço da web de Machine Learning – todos os 6 SUs tem 20 ligações em simultâneo ao serviço web Machine Learning, no máximo.

![Dimensionar o Stream Analytics com o exemplo de tarefa duas funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "dimensionamento Stream Analytics com o exemplo de tarefa duas funções do Machine Learning")

Em geral, ***B*** para o tamanho de lote ***L*** da tarefa para a latência do serviço web com o tamanho de lote B em milissegundos, com a taxa de transferência do Stream Analytics ***N*** SUs é:

![Dimensionar o Stream Analytics com a fórmula de funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "dimensionar o Stream Analytics com a fórmula de funções do Machine Learning")

Também pode configurar as "chamadas simultâneas máx." no serviço web Machine Learning. É recomendado que defina este parâmetro para o valor máximo (200 atualmente).

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

Vamos examinar a configuração necessária para criar uma tarefa do Stream Analytics, que faz a análise de sentimentos de tweets a uma taxa de 10 000 tweets por segundo.

Utilizar 1 SU, poderia esta tarefa de Stream Analytics processar o tráfego? A tarefa pode manter-se a entrada com o tamanho de lote de padrão de 1000. A latência de padrão de análise de sentimentos serviço web Machine Learning (com um tamanho de lote de padrão de 1000) cria não mais do que um segundo de latência.

A tarefa de Stream Analytics **geral** ou latência de ponto-a-ponto, normalmente, seria alguns segundos. Tenha uma visão mais detalhada nesta tarefa de Stream Analytics *especialmente* as chamadas de função do Machine Learning. Com um tamanho de lote de 1000, um débito de 10 000 eventos demora cerca de 10 pedidos ao serviço web. Mesmo com um SU, há suficiente conexões simultâneas para acomodar este tráfego de entrada.

Se aumentar a taxa de eventos de entrada por 100 vezes, a tarefa do Stream Analytics necessita de processar tweets 1 000 000 por segundo. Existem duas opções para realizar a escala maior:

1. Aumente o tamanho de lote.
2. Particione o fluxo de entrada para processar os eventos em paralelo.

Com a primeira opção, a tarefa **latência** aumenta.

Com a segunda opção, terá de aprovisionar mais SUs ter pedidos de serviço da web de Machine Learning mais em simultâneo. Este número maior de SUs, aumenta a tarefa **custo**.

Vamos examinar o dimensionamento utilizando as seguintes medidas de latência para cada tamanho de lote:

| Latência | Tamanho do batch |
| --- | --- |
| 200 ms | lotes de 1000 eventos ou abaixo |
| 250 ms | evento de 5.000 lotes |
| 300 ms | lotes de 10 000 eventos |
| 500 ms | lotes de eventos de 25.000 |

1. Com a primeira opção (**não** aprovisionamento mais SUs). O tamanho de lote pode ser aumentado para **25.000**. Aumentar o tamanho de lote desta forma, permitirá que a tarefa processar 1 000 000 eventos com 20 ligações simultâneas para o serviço web do Machine Learning (com uma latência de 500 ms por chamada). Para que a latência adicional da tarefa de Stream Analytics devido a pedidos de função de sentimento contra os pedidos de serviço da web de Machine Learning poderia ser aumentada de **200 ms** ao **500 ms**. No entanto, tamanho do lote **não é possível** aumenta infinitamente a serviços web Machine Learning requer que o tamanho da carga de um pedido de ser de 4 MB ou menor e a tempo limite de pedidos de serviço da web após 100 segundos da operação.
1. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço da web de 200 ms, todas as 20 ligações simultâneas para o serviço web seria capazes de processar 1000 * 20 * 5 eventos = 100 000 por segundo. Portanto, para processar 1 000 000 eventos por segundo, a tarefa precisaria 60 SUs. Em comparação com a primeira opção, a tarefa do Stream Analytics tornaria o mais batch solicitações de web service, por sua vez, gerando um custo maior.

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

Normalmente, o tamanho do lote a definir para as funções de Machine Learning não exatamente será divisível pelo número de eventos devolvidos por cada tarefa do Stream Analytics "pull". Quando isto ocorrer, o serviço web do Machine Learning é chamado com "parciais" lotes. Usar lotes parciais evita a incorrer em sobrecarga de latência de tarefa adicionais nos eventos de agregação de pull para extração.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitorização relacionados com a função
Na área de Monitor de uma tarefa do Stream Analytics, foram adicionadas três métricas adicionais relacionados com a função. Eles estão **pedidos de função**, **eventos de função** e **pedidos FALHADOS de função**, conforme mostrado no gráfico abaixo.

![Dimensionar o Stream Analytics com a métrica de funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "dimensionar o Stream Analytics com a métrica de funções do Machine Learning")

O estão definidos do seguinte modo:

**PEDIDOS DE FUNÇÃO**: O número de pedidos de função.

**EVENTOS DE FUNÇÃO**: O número de eventos nos pedidos de função.

**PEDIDOS DE FUNÇÃO FALHADA**: O número de pedidos de função falhada.

## <a name="key-takeaways"></a>Principais pedidas

Para dimensionar uma tarefa do Stream Analytics com as funções de Machine Learning, considere os seguintes fatores:

1. A taxa de eventos de entrada.
2. A latência tolerada para a tarefa de Stream Analytics em execução (e, portanto, o tamanho do lote dos pedidos de serviço web do Machine Learning).
3. O SUs de análise de Stream aprovisionado e o número de pedidos de serviço web Machine Learning (relacionados com a função custos adicionais).

Uma consulta do Stream Analytics totalmente particionada foi utilizada como exemplo. Se uma consulta mais complexa for necessária, o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) é um ótimo recurso para obter ajuda adicional da Equipe do Stream Analytics.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o Stream Analytics, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

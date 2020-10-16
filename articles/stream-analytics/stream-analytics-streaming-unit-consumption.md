---
title: Unidades de streaming em Azure Stream Analytics
description: Este artigo descreve a definição de Unidades de Streaming e outros fatores que impactam o desempenho no Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: e568051bfd5ac58f283eac7f9dc8a72be5c9dbbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079681"
---
# <a name="understand-and-adjust-streaming-units"></a>Compreender e ajustar as Unidades de Transmissão em Fluxo

As Unidades de Streaming (SUs) representam os recursos informáticos que são atribuídos para executar um trabalho stream analytics. Quanto mais SUs, mais recursos de CPU e de memória são alocados ao trabalho. Esta capacidade permite-lhe focar na lógica de consulta e abstrata a necessidade de gerir o hardware para executar o seu trabalho stream Analytics em tempo útil.

Para obter processamento de fluxos com baixa latência, os trabalhos do Azure Stream Analytics fazem todos os processamentos na memória. Quando se esgota a memória, o trabalho de streaming falha. Como resultado, para um trabalho de produção, é importante monitorizar o uso de recursos de um trabalho de streaming, e certificar-se de que há recursos suficientes para manter os empregos funcionando 24 horas por dia.

A métrica de utilização de SU %, que varia entre 0% e 100%, descreve o consumo de memória da sua carga de trabalho. Para um trabalho de streaming com pegada mínima, esta métrica é geralmente entre 10% a 20%. Se a utilização de SU% for elevada (acima de 80%), ou se os eventos de entrada forem retrocedidos (mesmo com uma baixa utilização de SU% uma vez que não mostra o uso do CPU), a sua carga de trabalho provavelmente requer mais recursos computativos, o que requer que aumente o número de SUs. É melhor manter a métrica de SU abaixo dos 80% para responder a picos ocasionais. A Microsoft recomenda definir um alerta sobre 80% su utilização métrica para evitar a exaustão dos recursos. Para mais informações, consulte [Tutorial: Configurar alertas para trabalhos Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Configure as Unidades de Streaming Analytics (SUs)
1. Inscreva-se no [portal Azure](https://portal.azure.com/)

2. Na lista de recursos, encontre o trabalho stream Analytics que deseja escalar e, em seguida, abri-lo. 

3. Na página de trabalho, sob o **título Configure,** selecione **Scale**.O número predefinido de SUs é 3 quando se cria um emprego.

    ![Configuração de trabalho do portal Azure Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Use o slider para definir as SUs para o trabalho. Note que está limitado a definições de SU específicas. 
5. Pode alterar o número de SUs atribuídos ao seu trabalho mesmo quando está a funcionar. Isto não é possível se o seu trabalho utilizar uma [saída não dividida](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) ou tiver uma consulta em [várias etapas com diferentes valores PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values). Talvez te restrisses a escolher entre um conjunto de valores de SU quando o trabalho está a funcionar. 

## <a name="monitor-job-performance"></a>Monitorizar o desempenho do trabalho
Utilizando o portal Azure, pode rastrear o resultado de um trabalho:

![Azure Stream Analytics monitoriza empregos][img.stream.analytics.monitor.job]

Calcular o rendimento esperado da carga de trabalho. Se a produção for inferior ao esperado, afina a partição de entrada, afina a consulta e adicione SUs ao seu trabalho.

## <a name="how-many-sus-are-required-for-a-job"></a>How many SUs are required for a job? (Quantas SUs são necessárias para um trabalho?)

A escolha do número de SUs necessárias para um determinado trabalho depende da configuração da partição para as entradas e da consulta definida dentro do trabalho. A página **Escala** permite-lhe definir o número certo de SUs. É uma boa prática atribuir mais SUs do que o necessário. O motor de processamento Stream Analytics otimiza para a latência e produção à custa da atribuição de memória adicional.

Em geral, a melhor prática é começar com 6 SUs para consultas que não usam **PARTITION BY**. Em seguida, determine o ponto doce utilizando um método de tentativa e erro no qual modifica o número de SUs depois de passar quantidades representativas de dados e examinar a métrica de utilização su%. O número máximo de unidades de streaming que podem ser utilizadas por um trabalho stream Analytics depende do número de passos na consulta definida para o trabalho e do número de divisórias em cada passo. Pode saber mais sobre os limites [aqui.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job)

Para obter mais informações sobre a escolha do número certo de SUs, consulte esta página: [Scale Azure Stream Analytics para aumentar a produção](stream-analytics-scale-jobs.md)

> [!Note]
> A escolha de quantas SUs são necessárias para um determinado trabalho depende da configuração da partição para as entradas e da consulta definida para o trabalho. Pode selecionar até à sua quota em SUs para um emprego. Por padrão, cada subscrição da Azure tem uma quota de até 500 SUs para todos os trabalhos de análise numa região específica. Para aumentar as SUs para as suas subscrições para além desta quota, contacte o [Microsoft Support](https://support.microsoft.com). Os valores válidos para SUs por trabalho são 1, 3, 6, e acima em incrementos de 6.

## <a name="factors-that-increase-su-utilization"></a>Factors that increase SU% utilization (Fatores que aumentam a percentagem de utilização de SUs) 

Os elementos de consulta temporal (orientados para o tempo) são o conjunto central de operadores estatais fornecidos pela Stream Analytics. O Stream Analytics gere internamente o estado destas operações em nome do utilizador, gerindo o consumo de memória, verificando a resiliência e recuperação do Estado durante as atualizações de serviço. Apesar de o Stream Analytics gerir totalmente os estados, existem várias recomendações de boas práticas que os utilizadores devem considerar.

Note que um trabalho com lógica de consulta complexa pode ter uma alta utilização de SU% mesmo quando não está continuamente recebendo eventos de entrada. Isto pode acontecer depois de um súbito aumento nos eventos de entrada e saída. O trabalho pode continuar a manter o estado na memória se a consulta for complexa.

A utilização por SU% pode subitamente cair para 0 por um curto período antes de voltar aos níveis esperados. Isto acontece devido a erros transitórios ou atualizações iniciadas pelo sistema. Aumentar o número de unidades de streaming para um trabalho pode não reduzir a utilização por SU% se a sua consulta não for [totalmente paralela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

Ao comparar a utilização durante um período de tempo, utilize [métricas de taxa](stream-analytics-monitoring.md)de eventos . As métricas InputEvents e OutputEvents mostram quantos eventos foram lidos e processados. Existem métricas que indicam também o número de erros, como erros de deserialização. Quando o número de eventos por unidade de tempo aumenta, su% aumenta na maioria dos casos.

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta imponente em elementos temporais
Uma das capacidades únicas do trabalho do Azure Stream Analytics é realizar um processamento imponente, como agregados com janelas, juntas temporais e funções analíticas temporais. Cada um destes operadores guarda informações estatais.O tamanho máximo da janela para estes elementos de consulta é de sete dias. 

O conceito de janela temporal aparece em vários elementos de consulta stream analytics:
1. Agregados com janelas: GRUPO BY de Tumbling, Hopping e Janelas deslizantes

2. Junções temporais: JUNTE-SE com função DATEDIFF

3. Funções analíticas temporais: ISFIRST, LAST e LAG com DURAÇÃO LIMITE

Os seguintes fatores influenciam a memória utilizada (parte da métrica das unidades de streaming) pelos trabalhos do Stream Analytics:

## <a name="windowed-aggregates"></a>Agregados com janelas
A memória consumida (tamanho do estado) para um agregado com janelas nem sempre é diretamente proporcional ao tamanho da janela. Em vez disso, a memória consumida é proporcional à cardinalidade dos dados, ou ao número de grupos em cada janela de cada vez.


Por exemplo, na seguinte consulta, o número associado `clusterid` é a cardinalidade da consulta. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Para mitigar quaisquer problemas causados por alta cardinalidade na consulta anterior, pode enviar eventos para o Event Hub divididos `clusterid` por , e escalar a consulta, permitindo que o sistema processe cada partição de entrada separadamente usando **PARTITION BY** como mostrado no exemplo abaixo:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de `clusterid` valores que entram em cada nó é reduzido, reduzindo assim a cardinalidade do grupo por operador. 

As divisórias do Event Hub devem ser divididas pela chave de agrupamento para evitar a necessidade de um passo de redução. Para mais informações, consulte [a visão geral do Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Junções temporais
A memória consumida (tamanho do estado) de uma junção temporal é proporcional ao número de eventos na sala de manobra temporal da junta, que é a taxa de entrada do evento multiplicada pelo tamanho da sala de manobra. Por outras palavras, a memória consumida por juntas é proporcional ao intervalo de tempo DateDiff multiplicado pela taxa média de evento.

O número de eventos incomparáveis na junção afeta a utilização da memória para a consulta. A seguinte consulta está à procura de encontrar as visualizações de anúncios que geram cliques:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Neste exemplo, é possível que muitos anúncios sejam mostrados e poucas pessoas clicam nele e é necessário manter todos os eventos na janela de tempo. A memória consumida é proporcional ao tamanho da janela e à velocidade dos eventos. 

Para remediar isto, envie eventos para o Event Hub divididos pelas teclas de união (ID neste caso), e escale a consulta, permitindo que o sistema processe cada partição de entrada separadamente usando  **PARTITION BY** como mostrado:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de eventos que entram em cada nó é reduzido, reduzindo assim o tamanho do estado mantido na janela de união. 

## <a name="temporal-analytic-functions"></a>Funções analíticas temporais
A memória consumida (tamanho do estado) de uma função analítica temporal é proporcional à taxa de evento multiplicar-se pela duração.A memória consumida por funções analíticas não é proporcional ao tamanho da janela, mas sim a contagem de divisórias em cada janela de tempo.

A remediação é semelhante à junção temporal. Pode escalonar a consulta utilizando **a PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Tampão fora de ordem 
O utilizador pode configurar o tamanho do tampão fora de ordem no painel de configuração de pedido de evento. O tampão é utilizado para conter as entradas durante a duração da janela e reencomenda-las. O tamanho do tampão é proporcional à taxa de entrada do evento multiplicar-se pelo tamanho da janela fora de ordem. O tamanho da janela padrão é 0. 

Para remediar o transbordo do tampão fora de ordem, escalone a consulta utilizando **partição by**. Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de eventos que entram em cada nó é reduzido, reduzindo assim o número de eventos em cada tampão reencamí o pedido. 

## <a name="input-partition-count"></a>Contagem de divisórias de entrada 
Cada divisão de entrada de uma entrada de trabalho tem um tampão. Quanto maior for o número de entradas, mais recurso o trabalho consome. Para cada unidade de streaming, o Azure Stream Analytics pode processar cerca de 1 MB/s de entrada. Portanto, pode otimizar combinando o número de unidades de streaming Stream Analytics com o número de divisórias no seu Event Hub. 

Normalmente, um trabalho configurado com uma unidade de streaming é suficiente para um Event Hub com duas divisórias (que é o mínimo para o Event Hub). Se o Event Hub tiver mais divisórias, o seu trabalho stream Analytics consome mais recursos, mas não necessariamente utiliza a produção extra fornecida pelo Event Hub. 

Para um trabalho com 6 unidades de streaming, poderá necessitar de 4 ou 8 divisórias do Centro de Eventos. No entanto, evite demasiadas divisórias desnecessárias, uma vez que isso provoca uma utilização excessiva dos recursos. Por exemplo, um Centro de Eventos com 16 divisórias ou maiores num trabalho stream Analytics que tem 1 unidade de streaming. 

## <a name="reference-data"></a>Dados de referência 
Os dados de referência na ASA são carregados na memória para uma procura rápida. Com a implementação atual, cada operação de junção com dados de referência mantém uma cópia dos dados de referência na memória, mesmo que se junte aos mesmos dados de referência várias vezes. Para consultas com **PARTITION BY,** cada divisória tem uma cópia dos dados de referência, para que as divisórias sejam totalmente dissociadas. Com o efeito multiplicador, o uso da memória pode rapidamente ficar muito alto se se juntar a dados de referência várias vezes com várias divisórias.  

### <a name="use-of-udf-functions"></a>Utilização de funções UDF
Quando adiciona uma função UDF, o Azure Stream Analytics carrega o tempo de execução JavaScript na memória. Isto vai afetar a SU.

## <a name="next-steps"></a>Passos seguintes
* [Criar consultas paralizáveis no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Escala Azure Stream Analytics empregos para aumentar a produção](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

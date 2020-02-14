---
title: Unidades de streaming em Azure Stream Analytics
description: Este artigo descreve a definição de Unidades de Streaming e outros fatores que impactam o desempenho no Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: dd7579c97e2166e2822ee5674bbcd5a8ad64d2c7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201497"
---
# <a name="understand-and-adjust-streaming-units"></a>Compreender e ajustar as Unidades de Transmissão em Fluxo

As Unidades de Streaming (UsS) representam os recursos informáticos que são atribuídos para executar um trabalho de Stream Analytics. Quanto mais SUs, mais recursos de CPU e de memória são alocados ao trabalho. Esta capacidade permite-lhe focar-se na lógica da consulta e abstraa a necessidade de gerir o hardware para executar o seu trabalho de Stream Analytics em tempo útil.

Para obter processamento de fluxos com baixa latência, os trabalhos do Azure Stream Analytics fazem todos os processamentos na memória. Quando se está a ficar sem memória, o trabalho de streaming falha. Como resultado, para um trabalho de produção, é importante monitorizar o uso de recursos de um trabalho de streaming, e garantir que há recursos suficientes para manter os postos de trabalho em funcionamento 24 horas por dia, 7 dias por semana.

A métrica de utilização da SU %, que varia entre 0% e 100%, descreve o consumo de memória da sua carga de trabalho. Para um trabalho de streaming com pegada mínima, esta métrica costuma estar entre 10% a 20%. Se a utilização de SU% for baixa e os eventos de entrada ficarem atrasados, a sua carga de trabalho provavelmente requer mais recursos computacionais, o que requer que aumente o número de SUs. É melhor manter a métrica da SU abaixo dos 80% para responder a picos ocasionais. A Microsoft recomenda a definição de um alerta sobre a métrica de utilização de 80% da SU para evitar a exaustão dos recursos. Para mais informações, consulte [Tutorial: Instale alertas para trabalhos da Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Configure Stream Analytics Streaming Units (SUs)
1. Iniciar sessão no [portal do Azure](https://portal.azure.com/)

2. Na lista de recursos, encontre o trabalho de Stream Analytics que pretende escalar e depois abra-o. 

3. Na página de trabalho, sob a rubrica **Configuração,** selecione **Escala**. 

    ![Configuração de trabalho do portal Azure Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Use o slider para definir as SUs para o trabalho. Note que está limitado a definições específicas de SU. 
5. Pode alterar o número de SUs atribuídas ao seu trabalho mesmo quando está em funcionamento. Isto não é possível se o seu trabalho utilizar uma [saída não dividida](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) ou tiver uma consulta em [várias etapas com diferentes valores de PARTIÇÃO Por](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values). O seu trabalho também deve ter pelo menos 6 SUs para alterar este cenário quando o trabalho está em execução. Talvez te tenhas restringido a escolher entre um conjunto de valores da SU quando o trabalho está a decorrer. 

## <a name="monitor-job-performance"></a>Monitorizar o desempenho do trabalho
Utilizando o portal Azure, pode rastrear a entrada de um trabalho:

![Azure Stream Analytics monitoriza empregos][img.stream.analytics.monitor.job]

Calcular a entrada esperada da carga de trabalho. Se a entrada for inferior ao esperado, sintonize a partição de entrada, sintonize a consulta e adicione SUs ao seu trabalho.

## <a name="how-many-sus-are-required-for-a-job"></a>Quantas SUs são necessárias para um trabalho?

Escolher o número de USS necessários para um determinado trabalho depende da configuração da partição para as inputs e da consulta que é definida dentro do trabalho. A página **Escala** permite-lhe definir o número certo de UsS. É uma boa prática atribuir mais US do que o necessário. O motor de processamento Stream Analytics otimiza para latência e saída ao custo de alocar memória adicional.

Em geral, a melhor prática é começar com 6 SUs para consultas que não usam **PARTITION BY**. Em seguida, determine o ponto doce utilizando um método de tentativa e erro no qual modifica o número de USS depois de passar quantidades representativas de dados e examinar a métrica de utilização do SU% O número máximo de unidades de streaming que podem ser utilizadas por um trabalho stream analytics depende do número de passos na consulta definida para o trabalho e do número de divisórias em cada passo. Pode saber mais sobre os limites [aqui.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job)

Para obter mais informações sobre a escolha do número certo de UsS, consulte esta página: [Scale Azure Stream Analytics empregos para aumentar a sua entrada](stream-analytics-scale-jobs.md)

> [!Note]
> Escolher quantas UsS são necessárias para um determinado trabalho depende da configuração da partição para as inputs e da consulta definida para o trabalho. Pode selecionar até à sua quota em SUs para um trabalho. Por predefinição, cada subscrição do Azure tem uma quota de até 500 US para todos os trabalhos de análise numa região específica. Para aumentar as SUs para as suas subscrições para além desta quota, contacte o [Microsoft Support](https://support.microsoft.com). Os valores válidos para As ES por trabalho são 1, 3, 6, e acima em incrementos de 6.

## <a name="factors-that-increase-su-utilization"></a>Fatores que aumentam a utilização dos SU% 

Os elementos de consulta temporal (orientados para o tempo) são o conjunto central de operadores estatais fornecidos pela Stream Analytics. O Stream Analytics gere o estado destas operações internamente em nome do utilizador, gerindo o consumo de memória, verificando a resiliência e recuperação do Estado durante as atualizações de serviço. Apesar de o Stream Analytics gerir plenamente os Estados, existem várias recomendações de boas práticas que os utilizadores devem considerar.

Note que um trabalho com lógica de consulta complexa pode ter uma alta utilização de SU% mesmo quando não está recebendo continuamente eventos de entrada. Isto pode acontecer após um aumento repentino de eventos de entrada e saída. O trabalho pode continuar a manter o estado na memória se a consulta for complexa.

A utilização de SU% pode subitamente cair para 0 por um curto período antes de voltar aos níveis esperados. Isto acontece devido a erros transitórios ou atualizações iniciadas pelo sistema. O aumento do número de unidades de streaming para um trabalho pode não reduzir a utilização de SU% se a sua consulta não for [totalmente paralela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

Ao comparar a utilização durante um período de tempo, utilize [métricas](stream-analytics-monitoring.md)da taxa de evento . As métricas inputEvents e OutputEvents mostram quantos eventos foram lidos e processados. Existem métricas que indicam o número de erros também, tais como erros de desserialização. Quando o número de eventos por unidade de tempo aumenta, a SU% aumenta na maioria dos casos.

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta com monitoração de Estado nos elementos temporais
Um da capacidade única de trabalho do Azure Stream Analytics consiste em efetuar o processamento com monitoração de estado, tais como funções de análise temporais, associações temporais e agregados em janelas. Cada um destes operadores guarda informações estatais. O tamanho máximo da janela para estes elementos de consulta é de sete dias. 

O conceito de janela temporal é apresentado em vários elementos de consulta do Stream Analytics:
1. Agregados com janelas: GROUP BY de tumbling, hopping e janelas deslizantes

2. Adesão temporal: Junte-se à função DATEDIFF

3. Funções analíticas temporais: ISFIRST, LAST e LAG com DURAÇÃO LIMITE

Os seguintes fatores influenciam a memória usada (parte da métrica das unidades de streaming) por trabalhos stream analytics:

## <a name="windowed-aggregates"></a>Agregados com janelas
A memória consumida (tamanho do estado) para um agregado com janelas nem sempre é diretamente proporcional ao tamanho da janela. Em vez disso, a memória consumida é proporcional à cardinalidade dos dados, ou ao número de grupos em cada janela de tempo.


Por exemplo, na seguinte consulta, o número associado à `clusterid` é a cardinalidade da consulta. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

A fim de mitigar quaisquer problemas causados pela elevada cardeal na consulta anterior, pode enviar eventos para o Event Hub divididos por `clusterid`, e escalar a consulta, permitindo que o sistema processe cada partição de entrada separadamente usando **partition BY,** como mostra o exemplo abaixo:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de valores `clusterid` que entram em cada nó é assim reduzido, reduzindo assim a cardinalidade do grupo por operador. 

As divisórias do Event Hub devem ser divididas pela chave de agrupamento para evitar a necessidade de um passo de redução. Para mais informações, consulte a [visão geral do Event Hubs.](../event-hubs/event-hubs-what-is-event-hubs.md) 

## <a name="temporal-joins"></a>Adesão temporal
A memória consumida (tamanho do estado) de uma junta temporal é proporcional ao número de eventos na sala de manobra temporal da união, que é a taxa de entrada de eventos multiplicada pelo tamanho da sala de manobra. Por outras palavras, a memória consumida por adesão é proporcional à faixa de tempo DateDiff multiplicada pela taxa média de eventos.

O número de eventos incomparáveis na união afeta a utilização da memória para a consulta. A seguinte consulta está à procura de encontrar as visualizações de anúncios que geram cliques:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Neste exemplo, é possível que muitos anúncios sejam mostrados e poucas pessoas clicam nele e é necessário manter todos os eventos na janela do tempo. A memória consumida é proporcional ao tamanho da janela e à velocidade dos eventos. 

Para remediar isto, envie eventos para o Event Hub divididos pelas teclas de união (ID neste caso), e esforce a consulta permitindo que o sistema processe cada partição de entrada separadamente usando **partition BY,** como mostrado:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de eventos que entram em cada nó é reduzido reduzindo assim o tamanho do estado mantido na janela de adesão. 

## <a name="temporal-analytic-functions"></a>Funções analíticas temporais
A memória consumida (tamanho do estado) de uma função analítica temporal é proporcional à taxa de evento multiplicada pela duração. A memória consumida por funções analíticas não é proporcional ao tamanho da janela, mas sim à contagem de divisórias em cada janela de tempo.

A reparação é semelhante à união temporal. Pode esdimensionar a consulta utilizando **partition BY**. 

## <a name="out-of-order-buffer"></a>Tampão fora de ordem 
O utilizador pode configurar o tamanho do tampão fora de ordem no painel de configuração de Encomenda de Eventos. O tampão é utilizado para reter as inputs durante a duração da janela e reencomendá-las. O tamanho do tampão é proporcional à taxa de entrada do evento multiplicar-se pelo tamanho da janela fora de ordem. O tamanho da janela padrão é 0. 

Para remediar o transbordo do tampão fora de ordem, esforce a consulta utilizando **partition BY**. Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de eventos que entram em cada nó é reduzido reduzindo assim o número de eventos em cada tampão de ordem. 

## <a name="input-partition-count"></a>Contagem de partição de entrada 
Cada partição de entrada de uma entrada de trabalho tem um tampão. Quanto maior for o número de divisórias de entrada, mais recursos o trabalho consome. Para cada unidade de streaming, o Azure Stream Analytics pode processar cerca de 1 MB/s de entrada. Portanto, pode otimizar combinando o número de unidades de streaming Stream Analytics com o número de divisórias no seu Hub de Eventos. 

Tipicamente, um trabalho configurado com uma unidade de streaming é suficiente para um Hub de Eventos com duas divisórias (que é o mínimo para O Hub de Eventos). Se o Event Hub tiver mais divisórias, o seu trabalho no Stream Analytics consome mais recursos, mas não necessariamente utiliza a entrada extra fornecida pelo Event Hub. 

Para um trabalho com 6 unidades de streaming, pode necessitar de 4 ou 8 divisórias do Centro de Eventos. No entanto, evite demasiadas divisórias desnecessárias, uma vez que isso causa o uso excessivo de recursos. Por exemplo, um Event Hub com 16 divisórias ou maior num trabalho de Stream Analytics que tem 1 unidade de streaming. 

## <a name="reference-data"></a>Dados de referência 
Os dados de referência na ASA são carregados na memória para uma rápida procura. Com a implementação atual, cada operação de adesão com dados de referência mantém uma cópia dos dados de referência na memória, mesmo que se junte aos mesmos dados de referência várias vezes. Para consultas com **PARTITION BY,** cada partição tem uma cópia dos dados de referência, pelo que as divisórias estão totalmente dissociadas. Com o efeito multiplicador, o uso da memória pode rapidamente ficar muito alto se se juntar com dados de referência várias vezes com várias divisórias.  

### <a name="use-of-udf-functions"></a>Utilização de funções UDF
Quando adiciona uma função UDF, o Azure Stream Analytics carrega o tempo de execução do JavaScript na memória. Isto afetará a U.A.

## <a name="next-steps"></a>Passos seguintes
* [Criar consultas paralelas no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Empregos na Scale Azure Stream Analytics aumentam a sua entrada](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

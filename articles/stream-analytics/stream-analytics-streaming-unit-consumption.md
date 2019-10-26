---
title: Unidades de streaming no Azure Stream Analytics
description: Este artigo descreve a configuração de unidades de streaming e outros fatores que afetam o desempenho em Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: a4811da398fde869d8eb5457db11a592006c59a9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934277"
---
# <a name="understand-and-adjust-streaming-units"></a>Entender e ajustar as unidades de streaming

As unidades de streaming (SUs) representam os recursos de computação alocados para executar um trabalho de Stream Analytics. Quanto mais SUs, mais recursos de CPU e de memória são alocados ao trabalho. Essa capacidade permite que você se concentre na lógica de consulta e abstrai a necessidade de gerenciar o hardware para executar seu trabalho de Stream Analytics de maneira oportuna.

Para obter processamento de fluxos com baixa latência, os trabalhos do Azure Stream Analytics fazem todos os processamentos na memória. Ao ficar sem memória, o trabalho de streaming falha. Como resultado, para um trabalho de produção, é importante monitorar o uso de recursos de um trabalho de streaming e verificar se há recursos suficientes alocados para manter os trabalhos em execução 24/7.

A métrica de% utilização de SU, que varia de 0% a 100%, descreve o consumo de memória de sua carga de trabalho. Para um trabalho de streaming com espaço mínimo, essa métrica geralmente está entre 10% e 20%. Se a utilização de SU% for baixa e eventos de entrada forem registrados, sua carga de trabalho provavelmente exigirá mais recursos de computação, o que exige que você aumente o número de SUs. É melhor manter a métrica de SU abaixo de 80% para considerar picos ocasionais. A Microsoft recomenda a configuração de um alerta na métrica de utilização de 80% SU para evitar o esgotamento de recursos. Para obter mais informações, consulte [tutorial: configurar alertas para trabalhos de Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Configurar unidades de streaming de Stream Analytics (SUs)
1. Iniciar sessão no [portal do Azure](https://portal.azure.com/)

2. Na lista de recursos, localize o trabalho Stream Analytics que você deseja dimensionar e, em seguida, abra-o. 

3. Na página trabalho, sob o título **Configurar** , selecione **escala**. 

    ![Configuração de trabalho do portal do Azure Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Use o controle deslizante para definir o SUs para o trabalho. Observe que você está limitado a configurações de SU específicas. 

## <a name="monitor-job-performance"></a>Monitorar o desempenho do trabalho
Usando o portal do Azure, você pode acompanhar a taxa de transferência de um trabalho:

![Azure Stream Analytics monitorar trabalhos][img.stream.analytics.monitor.job]

Calcule a taxa de transferência esperada da carga de trabalho. Se a taxa de transferência for menor que o esperado, ajuste a partição de entrada, ajuste a consulta e adicione o SUs ao seu trabalho.

## <a name="how-many-sus-are-required-for-a-job"></a>Quantos SUs são necessários para um trabalho?

Escolher o número de SUs necessário para um trabalho específico depende da configuração de partição das entradas e da consulta definida no trabalho. A página **escala** permite que você defina o número correto do SUS. É uma prática recomendada alocar mais SUs do que o necessário. O mecanismo de processamento de Stream Analytics otimiza a latência e a taxa de transferência ao custo de alocar memória adicional.

Em geral, a prática recomendada é começar com 6 SUs para consultas que não usam **Partition by**. Em seguida, determine o ponto ideal usando um método de avaliação e erro no qual você modifica o número de SUs depois de passar quantidades representativas de dados e examinar a métrica% de utilização de SU. O número máximo de unidades de streaming que podem ser usadas por um trabalho de Stream Analytics depende do número de etapas na consulta definida para o trabalho e o número de partições em cada etapa. Você pode saber mais sobre os limites [aqui](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Para obter mais informações sobre como escolher o número certo de SUs, consulte esta página: [dimensionar Azure Stream Analytics trabalhos para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)

> [!Note]
> A escolha de quantos SUs são necessários para um trabalho específico depende da configuração de partição das entradas e da consulta definida para o trabalho. Você pode selecionar até sua cota no SUs para um trabalho. Por padrão, cada assinatura do Azure tem uma cota de até 500 SUs para todos os trabalhos de análise em uma região específica. Para aumentar o SUs para suas assinaturas além dessa cota, entre em contato com [suporte da Microsoft](https://support.microsoft.com). Os valores válidos para o SUs por trabalho são 1, 3, 6 e em incrementos de 6.

## <a name="factors-that-increase-su-utilization"></a>Fatores que aumentam a utilização de SU% 

Elementos de consulta temporais (orientados por tempo) são o conjunto principal de operadores com monitoração de estado fornecidos pelo Stream Analytics. O Stream Analytics gerencia o estado dessas operações internamente em nome do usuário, gerenciando o consumo de memória, ponto de verificação para resiliência e recuperação de estado durante atualizações de serviço. Embora Stream Analytics gerencia totalmente os Estados, há uma série de recomendações de práticas recomendadas que os usuários devem considerar.

Observe que um trabalho com lógica de consulta complexa poderia ter uma alta utilização de SU% mesmo quando não estiver recebendo eventos de entrada continuamente. Isso pode ocorrer após um pico repentino em eventos de entrada e saída. O trabalho pode continuar a manter o estado na memória se a consulta for complexa.

A% de utilização de SU pode cair repentinamente em 0 por um curto período antes de voltar para os níveis esperados. Isso ocorre devido a erros transitórios ou atualizações iniciadas pelo sistema. O aumento do número de unidades de streaming para um trabalho pode não reduzir a utilização de SU% se a consulta não for [totalmente paralela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta com estado em elementos temporais
Um dos recursos exclusivos do trabalho Azure Stream Analytics é executar o processamento com estado, como agregações em janelas, junções temporais e funções analíticas temporais. Cada um desses operadores mantém informações de estado. O tamanho máximo da janela para esses elementos de consulta é de sete dias. 

O conceito de janela temporal aparece em vários elementos de consulta Stream Analytics:
1. Agregações em janela: GROUP BY de em cascata, salto e janelas deslizantes

2. Junções temporais: INGRESSAr com a função DATEDIFF

3. Funções analíticas temporais: isprimeiro, último e LATÊNCIA com limite de duração

Os fatores a seguir influenciam a memória usada (parte da métrica de unidades de streaming) por trabalhos de Stream Analytics:

## <a name="windowed-aggregates"></a>Agregações em janela
A memória consumida (tamanho do estado) para uma agregação em janela nem sempre é diretamente proporcional ao tamanho da janela. Em vez disso, a memória consumida é proporcional à cardinalidade dos dados ou ao número de grupos em cada janela de tempo.


Por exemplo, na consulta a seguir, o número associado a `clusterid` é a cardinalidade da consulta. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Para atenuar quaisquer problemas causados pela alta cardinalidade na consulta anterior, você pode enviar eventos para o Hub de eventos particionado por `clusterid`e escalar horizontalmente a consulta, permitindo que o sistema processe cada partição de entrada separadamente usando **Partition** conforme mostrado no exemplo a seguir:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de `clusterid` valores que entram em cada nó é reduzido, reduzindo a cardinalidade do operador Group by. 

As partições do hub de eventos devem ser particionadas pela chave de agrupamento para evitar a necessidade de uma etapa de redução. Para obter mais informações, consulte [visão geral dos hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Junções temporais
A memória consumida (tamanho do estado) de uma junção temporal é proporcional ao número de eventos no espaço de manobra temporal da junção, que é a taxa de entrada do evento multiplicada pelo tamanho da sala de ondulação. Em outras palavras, a memória consumida por junções é proporcional ao intervalo de tempo DateDiff multiplicado pela taxa média de eventos.

O número de eventos sem correspondência na junção afeta a utilização de memória para a consulta. A seguinte consulta está à procura de encontrar as visualizações de anúncios que geram cliques:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Neste exemplo, é possível que muitos anúncios sejam mostrados e algumas pessoas cliquem nele e sejam necessárias para manter todos os eventos na janela de tempo. A memória consumida é proporcional ao tamanho da janela e à velocidade dos eventos. 

Para corrigir isso, envie eventos para o Hub de eventos particionado pelas chaves de junção (ID nesse caso) e escale horizontalmente a consulta, permitindo que o sistema processe cada partição de entrada separadamente usando **Partition,** conforme mostrado:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de eventos que entram em cada nó é reduzido, reduzindo assim o tamanho do estado mantido na janela de junção. 

## <a name="temporal-analytic-functions"></a>Funções analíticas temporais
A memória consumida (tamanho do estado) de uma função analítica temporal é proporcional à taxa de eventos multiplicada pela duração. A memória consumida pelas funções analíticas não é proporcional ao tamanho da janela, mas sim à contagem de partições em cada janela de tempo.

A correção é semelhante à junção temporal. Você pode escalar horizontalmente a consulta usando **Partition by**. 

## <a name="out-of-order-buffer"></a>Buffer fora de ordem 
O usuário pode configurar o tamanho do buffer fora de ordem no painel de configuração de ordenação de eventos. O buffer é usado para armazenar entradas durante a duração da janela e reordená-las. O tamanho do buffer é proporcional à taxa de entrada do evento multiplicado pelo tamanho da janela fora de ordem. O tamanho padrão da janela é 0. 

Para corrigir o estouro do buffer fora de ordem, expanda a consulta usando **Partition by**. Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de eventos que entram em cada nó é reduzido, reduzindo o número de eventos em cada buffer de reordenação. 

## <a name="input-partition-count"></a>Contagem de partições de entrada 
Cada partição de entrada de uma entrada de trabalho tem um buffer. O número maior de partições de entrada, quanto mais recursos o trabalho consome. Para cada unidade de streaming, Azure Stream Analytics pode processar aproximadamente 1 MB/s de entrada. Portanto, você pode otimizar combinando o número de Stream Analytics unidades de streaming com o número de partições no Hub de eventos. 

Normalmente, um trabalho configurado com uma unidade de streaming é suficiente para um hub de eventos com duas partições (que é o mínimo para o Hub de eventos). Se o Hub de eventos tiver mais partições, seu trabalho de Stream Analytics consumirá mais recursos, mas não necessariamente usará a taxa de transferência extra fornecida pelo hub de eventos. 

Para um trabalho com 6 unidades de streaming, você pode precisar de 4 ou 8 partições do hub de eventos. No entanto, evite muitas partições desnecessárias, pois isso causa um uso excessivo de recursos. Por exemplo, um hub de eventos com 16 partições ou maior em um trabalho de Stream Analytics que tem uma unidade de streaming. 

## <a name="reference-data"></a>Dados de referência 
Os dados de referência no ASA são carregados na memória para pesquisa rápida. Com a implementação atual, cada operação de junção com dados de referência mantém uma cópia dos dados de referência na memória, mesmo que você ingresse com os mesmos dados de referência várias vezes. Para consultas com **partição by**, cada partição tem uma cópia dos dados de referência, de modo que as partições são totalmente desacopladas. Com o efeito de multiplicador, o uso de memória pode ficar muito alto rapidamente se você ingressar com dados de referência várias vezes com várias partições.  

### <a name="use-of-udf-functions"></a>Uso de funções UDF
Quando você adiciona uma função UDF, o Azure Stream Analytics carrega o tempo de execução do JavaScript na memória. Isso afetará o% de SU.

## <a name="next-steps"></a>Passos seguintes
* [Criar consultas paralelizáveis no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Dimensionar Azure Stream Analytics trabalhos para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

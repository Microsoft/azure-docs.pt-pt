---
title: Compreender e ajustar as unidades de transmissão em fluxo de mensagens em fila no Azure Stream Analytics
description: Este artigo descreve a definição de unidades de transmissão em fluxo e outros fatores que afetam o desempenho no Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 54296f0b4aed22457a5218154111a42ad01ec262
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329348"
---
# <a name="understand-and-adjust-streaming-units"></a>Compreender e ajustar as unidades transmissão em fluxo

(SUs) de unidades de transmissão em fluxo representa os recursos de computação alocados para executar uma tarefa do Stream Analytics. Quanto mais SUs, mais recursos de CPU e de memória são alocados ao trabalho. Este permite que de capacidade se concentre na lógica da consulta e abstrai a necessidade de gerir o hardware para executar seu do Stream Analytics da tarefa de forma atempada.

Para obter processamento de fluxos com baixa latência, os trabalhos do Azure Stream Analytics fazem todos os processamentos na memória. Se ficar sem memória, a tarefa de transmissão em fluxo falha. Como resultado, para uma tarefa de produção, é importante monitorizar a utilização de recursos de uma tarefa de transmissão em fluxo e certificar-se de que existe suficiente alocado para manter as tarefas em execução 24x7 de recurso.

A métrica de utilização % SU, que vai de 0% a 100%, descreve o consumo de memória da sua carga de trabalho. Para uma tarefa de transmissão em fluxo com requisitos de espaço mínimos, esta métrica é normalmente entre 10 a 20%. Se a % de utilização SU é baixo e de obterem eventos de entrada pendentes, sua carga de trabalho provavelmente requer mais recursos de computação, que requer a aumentar o número de SUs. É melhor manter a métrica SU inferior a 80% para levar em conta picos ocasionais. A Microsoft recomenda a definição de um alerta de métrica de utilização SU de 80% para evitar o esgotamento de recursos. Para obter mais informações, consulte [Tutorial: Configurar alertas para tarefas do Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Configure a análise de Stream (SUs) de unidades de transmissão em fluxo
1. Iniciar sessão no [portal do Azure](https://portal.azure.com/)

2. Na lista de recursos, localize a tarefa de Stream Analytics que pretende dimensionar e, em seguida, abri-lo. 

3. Na página do trabalho, sob o **configurar** cabeçalho, selecione **dimensionamento**. 

    ![Configuração de tarefa Stream Analytics portal do Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Utilize o controlo de deslize para definir os SUs para a tarefa. Tenha em atenção que está limitada às definições específicas do SU. 

## <a name="monitor-job-performance"></a>Monitorizar o desempenho de tarefa
Utilizar o portal do Azure, pode acompanhar a taxa de transferência de uma tarefa:

![Tarefas de monitor do Azure Stream Analytics][img.stream.analytics.monitor.job]

Calcule o débito esperado da carga de trabalho. Se o débito for menor que o esperado, otimizar a partição de entrada, otimizar a consulta e adicionar SUs a seu trabalho.

## <a name="how-many-sus-are-required-for-a-job"></a>Quantidade de SUs é necessárias para uma tarefa?

Escolher o número de SUs necessárias para uma tarefa específica depende da configuração de partição para as entradas e a consulta que é definida dentro da tarefa. O **dimensionamento** página permite-lhe definir o número certo de SUs. É melhor prática alocar mais SUs que o necessário. Otimiza o motor de processamento do Stream Analytics para latência e débito ao custo de alocação de memória adicional.

Em geral, a melhor prática é começar com 6 SUs para consultas que não usam **PARTITION BY**. Em seguida, determine encontramos utilizando um método de tentativa e erro em que modificar o número de SUs depois de transmitir quantidades representativas de dados e examinar a métrica % Utilization de SU. O número máximo de unidades de transmissão em fluxo que pode ser utilizado por uma tarefa do Stream Analytics depende o número de passos da consulta definida para a tarefa e o número de partições em cada etapa. Pode saber mais sobre os limites [aqui](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Para obter mais informações sobre como escolher o número certo de SUs, consulte esta página: [Tarefas de escala do Azure Stream Analytics para aumentar o débito](stream-analytics-scale-jobs.md)

> [!Note]
> Escolher a quantidade de SUs são necessários para uma tarefa específica depende da configuração de partição para as entradas e a consulta definida para a tarefa. Pode selecionar até sua quota em SUs para uma tarefa. Por predefinição, cada subscrição do Azure tem uma quota de até 500 SUs para todas as tarefas de análise numa região específica. Para aumentar o SUs para as suas subscrições para além desta quota, contacte [Support da Microsoft](https://support.microsoft.com). Os valores válidos para SUs por tarefa são 1, 3, 6 e até em incrementos de 6.

## <a name="factors-that-increase-su-utilization"></a>Fatores que aumentam a % de utilização SU 

Elementos de consulta (orientados a tempo) temporal são o conjunto básico de operadores com monitoração de estado fornecido pelo Stream Analytics. Stream Analytics gere o estado destas operações internamente em nome de utilizador, através da gestão do consumo de memória, ponto de verificação para resiliência e recuperação de estado durante as atualizações de serviço. Apesar do Stream Analytics gere totalmente os Estados, há uma série de recomendações de melhores práticas que devem considerar os utilizadores.

Tenha em atenção que uma tarefa com lógica de consulta complexas poderia ter alta utilização de % SU, mesmo quando ele não está continuamente a receber eventos de entrada. Isto pode acontecer após um pico nos eventos de entrada e saídos. A tarefa poderá continuar a manter o estado na memória se a consulta é complexa.

% De utilização SU, de repente, pode remover como 0 durante um curto período antes de entrar novamente para níveis esperados. Isto acontece devido a erros transitórios ou atualizações de sistema iniciada. Aumentar o número de unidades de transmissão em fluxo para uma tarefa não consiga reduzir SU % de utilização, se a sua consulta não é [totalmente paralela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta com monitoração de Estado nos elementos temporais
Um da capacidade única de trabalho do Azure Stream Analytics consiste em efetuar o processamento com monitoração de estado, tais como funções de análise temporais, associações temporais e agregados em janelas. Cada um destes operadores mantém informações de estado. O tamanho máximo da janela para esses elementos de consulta é de sete dias. 

O conceito de janela temporal é apresentado em vários elementos de consulta do Stream Analytics:
1. Agregados em janelas: GRUPO por de em cascata, saltos e deslizante windows

2. Associações temporais: Junte-se com a função DATEDIFF

3. Funções de análise temporais: ISFIRST, LAST e desfasamento com duração de limite

Os seguintes fatores influenciam a memória utilizada (parte da métrica de unidades de transmissão em fluxo) por tarefas do Stream Analytics:

## <a name="windowed-aggregates"></a>Agregados em janelas
A memória utilizada (tamanho de estado) para um agregado em janelas nem sempre é diretamente proporcional ao tamanho de janela. Em vez disso, a memória consumida é proporcional à cardinalidade dos dados ou o número de grupos em cada janela de tempo.


Por exemplo, na seguinte consulta, o número associado `clusterid` é a cardinalidade da consulta. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Para minimizar os problemas causados por elevada cardinalidade da consulta anterior, pode enviar eventos para o Hub de eventos repartido pelas `clusterid`e aumentar horizontalmente a consulta ao permitir que o sistema processar cada partição de entrada separadamente com **PARTIÇÃO POR** conforme mostrado no exemplo abaixo:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de `clusterid` valores chegam a cada nó são reduzidos, reduzindo a cardinalidade do grupo pela operadora de rede. 

Partições do Hub de eventos devem ser particionadas por chave de agrupamento para evitar a necessidade de um passo reduzido. Para obter mais informações, consulte [descrição geral dos Hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Associações temporais
A memória utilizada (tamanho de estado) de uma associação temporal é proporcional ao número de eventos no ambiente de tempo temporal de associação, o que é a taxa de entrada de eventos, multiplicado pelo tamanho do espaço de tempo. Em outras palavras, a memória utilizada pelas associações é proporcional ao intervalo de tempo de DateDiff multiplicado pela taxa de eventos média.

O número de eventos sem correspondência na associação afeta a utilização da memória para a consulta. A seguinte consulta está à procura de encontrar as visualizações de anúncios que geram cliques:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Neste exemplo, é possível que são apresentados vários anúncios e algumas pessoas cliquem nos mesmos e é necessário para manter todos os eventos na janela de tempo. A memória consumida é proporcional ao tamanho da janela e à velocidade dos eventos. 

Para corrigir este problema, enviar eventos para o Hub de eventos particionados pelas chaves de associação (id neste caso) e aumentar horizontalmente a consulta ao permitir que o sistema para processar cada partição de entrada separadamente usando **PARTITION BY** conforme mostrado:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado o número de eventos que chegam a cada nó é reduzido, reduzindo o tamanho do estado mantido na janela de associação. 

## <a name="temporal-analytic-functions"></a>Funções de análise temporais
A memória utilizada (tamanho de estado) de uma função analítica temporal é proporcional à taxa de eventos multiply pela duração. A memória utilizada pelas funções de análise não é proporcional ao tamanho de janela, mas em vez de partição contagem em cada janela de tempo.

A remediação é semelhante à associação temporal. Pode aumentar horizontalmente a consulta utilizando **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Buffer fora de ordem 
Utilizador pode configurar o tamanho do buffer fora de ordem, no caso de ordenação de painel de configuração. A memória intermédia é utilizada para conter entradas para a duração da janela e reordenar. O tamanho da memória intermédia é proporcional à taxa de entrada de eventos multiplicado pelo tamanho da janela fora de ordem. O tamanho da janela padrão é 0. 

Para remediar o estouro do buffer fora de ordem, aumentar horizontalmente a consulta utilizando **PARTITION BY**. Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de eventos que chegam a cada nó é reduzido, reduzindo o número de eventos em cada memória intermédia de reordenação. 

## <a name="input-partition-count"></a>Contagem de partições de entrada 
Cada partição de entrada de uma tarefa de entrada tem um buffer. O maior número de partições de entrada, o recurso mais a tarefa consome. Para cada unidade de transmissão em fluxo, o Azure Stream Analytics pode processar aproximadamente 1 MB/s de entrada. Portanto, pode otimizar pela correspondência entre o número de unidades com o número de partições do seu Hub de eventos de transmissão em fluxo de análise de Stream. 

Normalmente, uma tarefa configurada com uma unidade de transmissão em fluxo é suficiente para um Hub de eventos com duas partições (que é o mínimo para o Hub de eventos). Se o Hub de eventos tem mais partições, a tarefa de Stream Analytics consome mais recursos, mas não necessariamente utiliza o débito adicional fornecido pelo Hub de eventos. 

Para uma tarefa com 6 unidades transmissão em fluxo, terá de 4 ou 8 partições do Hub de eventos. No entanto, evite demasiadas partições desnecessárias, uma vez que o que faz com que o uso excessivo de recursos. Por exemplo, um Hub de eventos com 16 partições ou superior numa tarefa do Stream Analytics que tem 1 unidade de transmissão em fluxo. 

## <a name="reference-data"></a>Dados de referência 
Dados de referência no ASA são carregados na memória para a pesquisa rápida. Com a implementação atual, cada operação de associação com dados de referência mantém uma cópia dos dados de referência na memória, mesmo se ingressar com os mesmos dados de referência várias vezes. Para consultas com **PARTITION BY**, cada partição tem uma cópia dos dados de referência, portanto, as partições são completamente desacopladas. Com efeito multiplicador, utilização da memória pode rapidamente obter muito alta, se associar com dados de referência várias vezes com várias partições.  

### <a name="use-of-udf-functions"></a>Uso de funções UDF
Quando adiciona uma função UDF, o Azure Stream Analytics carrega o tempo de execução do JavaScript na memória. Isto irá afetar a % SU.

## <a name="next-steps"></a>Passos Seguintes
* [Criar consultas ponto pode ser paralelizadas no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Tarefas de escala do Azure Stream Analytics para aumentar o débito](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

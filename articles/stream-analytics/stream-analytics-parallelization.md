---
title: Usar paralelização de consulta e dimensionar em Azure Stream Analytics
description: Este artigo descreve como dimensionar Stream Analytics trabalhos Configurando partições de entrada, ajustando a definição de consulta e definindo unidades de streaming de trabalho.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 985746989af39aa55d5d8af735edf62f4c4b77b7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932293"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Aproveitar a paralelização de consultas no Azure Stream Analytics
Este artigo mostra como aproveitar a paralelização no Azure Stream Analytics. Você aprende a dimensionar Stream Analytics trabalhos Configurando partições de entrada e ajustando a definição de consulta de análise.
Como pré-requisito, talvez você queira estar familiarizado com a noção da unidade de streaming descrita em [entender e ajustar as unidades de streaming](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de um trabalho de Stream Analytics?
Uma definição de trabalho Stream Analytics inclui entradas, uma consulta e uma saída. As entradas são para onde o trabalho lê o fluxo de dados. A consulta é usada para transformar o fluxo de entrada de dados e a saída é onde o trabalho envia os resultados do trabalho.

Um trabalho requer pelo menos uma fonte de entrada para o streaming de dados. A fonte de entrada do fluxo de dados pode ser armazenada em um hub de eventos do Azure ou no armazenamento de BLOBs do Azure. Para obter mais informações, consulte [introdução ao Azure Stream Analytics](stream-analytics-introduction.md) e começar a [usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partições em fontes e coletores
A colocação em escala de um trabalho de Stream Analytics aproveita as partições na entrada ou na saída. O particionamento permite dividir dados em subconjuntos com base em uma chave de partição. Um processo que consome os dados (como um trabalho de streaming Analytics) pode consumir e gravar partições diferentes em paralelo, o que aumenta a taxa de transferência. 

### <a name="inputs"></a>Entradas
Todas as Azure Stream Analytics entrada podem aproveitar o particionamento:
-   EventHub (é necessário definir a chave de partição explicitamente com partição por palavra-chave)
-   Hub IoT (é necessário definir a chave de partição explicitamente com a palavra-chave PARTITION BY)
-   Armazenamento de blobs

### <a name="outputs"></a>Saídas

Ao trabalhar com Stream Analytics, você pode aproveitar o particionamento nas saídas:
-   Armazenamento do Azure Data Lake
-   Funções do Azure
-   Tabela do Azure
-   Armazenamento de BLOBs (pode definir a chave de partição explicitamente)
-   Cosmos DB (é necessário definir a chave de partição explicitamente)
-   Hubs de eventos (é necessário definir a chave de partição explicitamente)
-   Hub IoT (é necessário definir a chave de partição explicitamente)
-   Service Bus
- SQL e SQL Data Warehouse com particionamento opcional: Veja mais informações na [página saída para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI não dá suporte ao particionamento. No entanto, você ainda pode particionar a entrada conforme descrito nesta [seção](#multi-step-query-with-different-partition-by-values) 

Para obter mais informações sobre partições, consulte os seguintes artigos:

* [Descrição geral das funcionalidades dos Hubs de Eventos](../event-hubs/event-hubs-features.md#partitions)
* [Criação de partições de dados](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Trabalhos paralelos do embaraçosamente
Um trabalho *paralelo embaraçosamente* é o cenário mais escalonável que temos em Azure Stream Analytics. Ele conecta uma partição da entrada a uma instância da consulta a uma partição da saída. Esse paralelismo tem os seguintes requisitos:

1. Se a lógica de consulta depender da mesma chave que está sendo processada pela mesma instância de consulta, você deverá certificar-se de que os eventos vão para a mesma partição de sua entrada. Para hubs de eventos ou Hub IoT, isso significa que os dados do evento devem ter o valor **PartitionKey** definido. Como alternativa, você pode usar os remetentes particionados. Para o armazenamento de BLOBs, isso significa que os eventos são enviados para a mesma pasta de partição. Se a lógica de consulta não exigir que a mesma chave seja processada pela mesma instância de consulta, você poderá ignorar esse requisito. Um exemplo dessa lógica seria uma consulta SELECT-Project-Filter simples.  

2. Depois que os dados são dispostos no lado de entrada, você deve verificar se a consulta está particionada. Isso exige que você use **Partition by** em todas as etapas. Várias etapas são permitidas, mas todas devem ser particionadas pela mesma chave. Em nível de compatibilidade 1,0 e 1,1, a chave de particionamento deve ser definida como **PartitionID** para que o trabalho seja totalmente paralelo. Para trabalhos com o nível de compatibilidade 1,2 e superior, a coluna personalizada pode ser especificada como chave de partição nas configurações de entrada e o trabalho será paralellized automaticamente até mesmo sem a cláusula PARTITION BY. Para saída do hub de eventos, a propriedade "coluna de chave de partição" deve ser definida para usar "PartitionID".

3. A maior parte de nossa saída pode aproveitar o particionamento. no entanto, se você usar um tipo de saída que não dê suporte ao particionamento, o trabalho não será totalmente paralelo. Consulte a [seção de saída](#outputs) para obter mais detalhes.

4. O número de partições de entrada deve ser igual ao número de partições de saída. A saída do armazenamento de BLOBs pode dar suporte a partições e herda o esquema de particionamento da consulta upstream. Quando uma chave de partição para o armazenamento de blob é especificada, os dados são particionados por partição de entrada, portanto, o resultado ainda é totalmente paralelo. Aqui estão exemplos de valores de partição que permitem um trabalho totalmente paralelo:

   * 8 partições de entrada do hub de eventos e 8 partições de saída do hub de eventos
   * 8 partições de entrada do hub de eventos e saída do armazenamento de BLOBs
   * 8 partições de entrada do hub de eventos e saída de armazenamento de BLOBs particionadas por um campo personalizado com cardinalidade arbitrária
   * 8 partições de entrada de armazenamento de BLOBs e saída de armazenamento de BLOBs
   * 8 partições de entrada do armazenamento de BLOBs e 8 partições de saída do hub de eventos

As seções a seguir discutem alguns cenários de exemplo que são embaraçosamente paralelos.

### <a name="simple-query"></a>Consulta simples

* Entrada: Hub de eventos com 8 partições
* Saída: o Hub de eventos com 8 partições ("coluna de chave de partição" deve ser definido para usar "PartitionID")

Consultá

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Essa consulta é um filtro simples. Portanto, não precisamos nos preocupar com o particionamento da entrada que está sendo enviada para o Hub de eventos. Observe que os trabalhos com nível de compatibilidade antes de 1,2 devem incluir **a cláusula PARTITION by PartitionID** , portanto, ele atende aos requisitos #2 anteriores. Para a saída, precisamos configurar a saída do hub de eventos no trabalho para ter a chave de partição definida como **PartitionID**. Uma última verificação é certificar-se de que o número de partições de entrada seja igual ao número de partições de saída.

### <a name="query-with-a-grouping-key"></a>Consultar com uma chave de agrupamento

* Entrada: Hub de eventos com 8 partições
* Saída: armazenamento de BLOBs

Consultá

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Esta consulta tem uma chave de agrupamento. Portanto, os eventos agrupados devem ser enviados para a mesma partição do hub de eventos. Como, neste exemplo, agrupamos por TollBoothID, devemos ter certeza de que TollBoothID é usado como a chave de partição quando os eventos são enviados ao Hub de eventos. Em seguida, no ASA, podemos usar **particionar por PartitionID** para herdar deste esquema de partição e habilitar a paralelização completa. Como a saída é o armazenamento de BLOBs, não precisamos nos preocupar com a configuração de um valor de chave de partição, de acordo com o requisito #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exemplo de cenários que *não* são embaraçosamente paralelos

Na seção anterior, mostramos alguns cenários paralelos embaraçosamente. Nesta seção, discutiremos cenários que não atendem a todos os requisitos para serem embaraçosamente paralelos. 

### <a name="mismatched-partition-count"></a>Contagem de partições incompatíveis
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com partições 32

Nesse caso, não importa qual é a consulta. Se a contagem de partição de entrada não corresponder à contagem de partição de saída, a topologia não será embaraçosamente paralela. + no entanto, ainda podemos obter algum nível ou paralelização.

### <a name="query-using-non-partitioned-output"></a>Consulta usando saída não particionada
* Entrada: Hub de eventos com 8 partições
* Saída: Power BI

Power BI a saída atualmente não dá suporte ao particionamento. Portanto, esse cenário não é embaraçosamente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de várias etapas com uma partição diferente por valores
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

Consultá

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Como você pode ver, a segunda etapa usa **TollBoothId** como a chave de particionamento. Essa etapa não é igual à primeira etapa e, portanto, exige que façamos uma ordem aleatória. 

Os exemplos anteriores mostram alguns trabalhos de Stream Analytics que estão em conformidade com (ou não) uma topologia paralela embaraçosamente. Se eles estiverem em conformidade, eles terão o potencial para a escala máxima. Para trabalhos que não se ajustam a um desses perfis, as diretrizes de dimensionamento estarão disponíveis em atualizações futuras. Por enquanto, use as diretrizes gerais nas seções a seguir.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Nível de compatibilidade 1,2-consulta de várias etapas com uma partição diferente por valores 
* Entrada: Hub de eventos com 8 partições
* Saída: o Hub de eventos com 8 partições ("coluna de chave de partição" deve ser definido para usar "TollBoothId")

Consultá

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

O nível de compatibilidade 1,2 permite a execução de consulta paralela por padrão. Por exemplo, a consulta da seção anterior será parttioned, desde que a coluna "TollBoothId" seja definida como chave de partição de entrada. A cláusula PARTITION BY ParttionId não é necessária.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular as unidades de streaming máximas de um trabalho
O número total de unidades de streaming que podem ser usadas por um trabalho de Stream Analytics depende do número de etapas na consulta definida para o trabalho e o número de partições para cada etapa.

### <a name="steps-in-a-query"></a>Etapas em uma consulta
Uma consulta pode ter uma ou várias etapas. Cada etapa é uma subconsulta definida pela palavra-chave **with** . A consulta que está fora da palavra-chave **with** (somente uma consulta) também é contada como uma etapa, como a instrução **Select** na seguinte consulta:

Consultá

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Essa consulta tem duas etapas.

> [!NOTE]
> Essa consulta é discutida em mais detalhes posteriormente neste artigo.
>  

### <a name="partition-a-step"></a>Particionar uma etapa
O particionamento de uma etapa requer as seguintes condições:

* A fonte de entrada deve ser particionada. 
* A instrução **Select** da consulta deve ser lida de uma fonte de entrada particionada.
* A consulta dentro da etapa deve ter a palavra-chave **Partition by** .

Quando uma consulta é particionada, os eventos de entrada são processados e agregados em grupos de partição separados, e os eventos de saída são gerados para cada um dos grupos. Se você quiser uma agregação combinada, deverá criar uma segunda etapa não particionada para agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular as unidades de streaming máximas para um trabalho
Todas as etapas não particionadas juntas podem escalar verticalmente até seis unidades de streaming (SUs) para um trabalho de Stream Analytics. Além disso, você pode adicionar 6 SUs para cada partição em uma etapa particionada.
Você pode ver alguns **exemplos** na tabela a seguir.

| Consulta                                               | SUs máximo para o trabalho |
| --------------------------------------------------- | ------------------- |
| <ul><li>A consulta contém uma etapa.</li><li>A etapa não é particionada.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é particionado por 16.</li><li>A consulta contém uma etapa.</li><li>A etapa é particionada.</li></ul> | 96 (6 * 16 partições) |
| <ul><li>A consulta contém duas etapas.</li><li>Nenhuma das etapas está particionada.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é particionado por 3.</li><li>A consulta contém duas etapas. A etapa de entrada é particionada e a segunda etapa não é.</li><li>A instrução <strong>Select</strong> lê a partir da entrada particionada.</li></ul> | 24 (18 para etapas particionadas + 6 para etapas não particionadas |

### <a name="examples-of-scaling"></a>Exemplos de dimensionamento

A consulta a seguir calcula o número de carros em uma janela de três minutos passando por uma estação de telefone que tem três pedágios. Essa consulta pode ser dimensionada para até seis SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Para usar mais SUs para a consulta, o fluxo de dados de entrada e a consulta devem ser particionados. Como a partição do fluxo de dados está definida como 3, a seguinte consulta modificada pode ser dimensionada para até 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Quando uma consulta é particionada, os eventos de entrada são processados e agregados em grupos de partição separados. Os eventos de saída também são gerados para cada um dos grupos. O particionamento pode causar alguns resultados inesperados quando o campo **Agrupar por** não é a chave de partição no fluxo de dados de entrada. Por exemplo, o campo **TollBoothId** na consulta anterior não é a chave de partição de **entrada1**. O resultado é que os dados do pedágio #1 podem ser distribuídos em várias partições.

Cada uma das partições **entrada1** será processada separadamente por Stream Analytics. Como resultado, vários registros da contagem de carros para o mesmo pedágio na mesma janela em cascata serão criados. Se a chave de partição de entrada não puder ser alterada, esse problema poderá ser corrigido com a adição de uma etapa que não seja de partição para agregar valores entre partições, como no exemplo a seguir:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Essa consulta pode ser dimensionada para 24 SUs.

> [!NOTE]
> Se você estiver unindo dois fluxos, certifique-se de que os fluxos sejam particionados pela chave de partição da coluna que você usa para criar as junções. Verifique também se você tem o mesmo número de partições em ambos os fluxos.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Obtendo mais taxas de transferência em escala

Um trabalho [paralelo embaraçosamente](#embarrassingly-parallel-jobs) é necessário, mas não é suficiente para sustentar uma taxa de transferência mais alta em escala. Cada sistema de armazenamento e sua saída de Stream Analytics correspondente têm variações sobre como obter a melhor taxa de transferência possível de gravação. Assim como acontece com qualquer cenário em escala, há alguns desafios que podem ser resolvidos usando as configurações corretas. Esta seção discute as configurações para algumas saídas comuns e fornece exemplos para manter as taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

As observações a seguir usam um trabalho Stream Analytics com consulta sem estado (passagem), um UDF JavaScript básico que grava no Hub de eventos, banco de BD SQL do Azure ou Cosmos DB.

#### <a name="event-hub"></a>Hub de Eventos

|Taxa de ingestão (eventos por segundo) | Unidades Transmissão em Fluxo | Recursos de saída  |
|--------|---------|---------|
| 1K     |    1    |  2 TU   |
| 5K     |    6    |  6 TU   |
| 10.000    |    12   |  10 TU  |

A solução de [Hub de eventos](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) é dimensionada linearmente em termos de Su (unidades de streaming) e taxa de transferência, tornando-a a maneira mais eficiente e de alto desempenho de analisar e transmitir dados fora de Stream Analytics. Os trabalhos podem ser dimensionados para até 192 SU, o que significa, aproximadamente, o processamento de até 200 MB/s ou 19.000.000.000.000 eventos por dia.

#### <a name="azure-sql"></a>SQL do Azure
|Taxa de ingestão (eventos por segundo) | Unidades Transmissão em Fluxo | Recursos de saída  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10.000  |   36 |  P6   |

O [SQL do Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) dá suporte à gravação em paralelo, chamado de particionamento de herança, mas não é habilitado por padrão. No entanto, habilitar o particionamento de herança, juntamente com uma consulta totalmente paralela, pode não ser suficiente para obter taxas de transferência mais altas. As taxas de transferência de gravação do SQL dependem significativamente da configuração do banco de dados SQL Azure e do esquema de tabela. O artigo [desempenho de saída do SQL](./stream-analytics-sql-output-perf.md) tem mais detalhes sobre os parâmetros que podem maximizar a taxa de transferência de gravação. Conforme observado no artigo [Azure Stream Analytics saída para o banco de dados SQL do Azure](./stream-analytics-sql-output-perf.md#azure-stream-analytics) , essa solução não é dimensionada linearmente como um pipeline totalmente paralelo além de 8 partições e pode precisar de reparticionamento antes da saída SQL (consulte [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Os SKUs Premium são necessários para sustentar taxas de e/s altas juntamente com a sobrecarga dos backups de log ocorrendo a cada poucos minutos.

#### <a name="cosmos-db"></a>BD do Cosmos
|Taxa de ingestão (eventos por segundo) | Unidades Transmissão em Fluxo | Recursos de saída  |
|-------|-------|---------|
|  1K   |  3    | 20 MIL RU  |
|  5K   |  24   | 60K RU  |
|  10.000  |  48   | 120 MIL RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) saída do Stream Analytics foi atualizada para usar a integração nativa no [nível de compatibilidade 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). O nível de compatibilidade 1,2 permite uma taxa de transferência significativamente mais alta e reduz o consumo de RU em comparação com 1,1, que é o nível de compatibilidade padrão para novos trabalhos. A solução usa contêineres CosmosDB particionados em/deviceId e o restante da solução é configurado de forma idêntica.

Todos os [exemplos de streaming em escala do Azure](https://github.com/Azure-Samples/streaming-at-scale) usam um hub de eventos alimentado pelos clientes de teste de simulação de carga como entrada. Cada evento de entrada é um documento JSON 1 KB, que traduz as taxas de ingestão configuradas para taxas de taxa de transferência (1MB/s, 5 MB/s e 10 MB/s) facilmente. Os eventos simulam um dispositivo IoT enviando os seguintes dados JSON (em uma forma reduzida) para dispositivos de até 1K:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> As configurações estão sujeitas a alterações devido a vários componentes usados na solução. Para obter uma estimativa mais precisa, personalize os exemplos para se ajustar ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificando afunilamentos

Use o painel métricas em seu trabalho de Azure Stream Analytics para identificar afunilamentos em seu pipeline. Examine os **eventos de entrada/saída** para obter a taxa de transferência e o ["atraso da marca d' água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **eventos de registro** posterior para ver se o trabalho está acompanhando a taxa de entrada. Para as métricas do hub de eventos, procure **solicitações limitadas** e ajuste as unidades de limite de acordo. Para Cosmos DB métricas, examine o **intervalo máximo consumido de ru/s por chave de partição** em taxa de transferência para garantir que os intervalos de chaves de partição sejam consumidos uniformemente. Para o banco de BD SQL do Azure, monitore e **/s de log** e **CPU**.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [Fórum de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301


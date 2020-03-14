---
title: Utilizar a paralelização de consultas e dimensionamento no Azure Stream Analytics
description: Este artigo descreve como dimensionar tarefas do Stream Analytics ao configurar partições de entrada, ajuste a definição de consulta e definir as unidades de transmissão em fluxo de trabalho.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: d1afb6037b5fc290de93faba405982ebd1fb68ea
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254342"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Tirar partido de paralelização de consultas no Azure Stream Analytics
Este artigo mostra-lhe como tirar partido da paralelização no Azure Stream Analytics. Saiba como dimensionar tarefas do Stream Analytics ao configurar partições de entrada e a definição de consulta de análise de otimização.
Como pré-requisito, pode querer estar familiarizado com a noção de Unidade de Streaming descrita em [Compreender e ajustar unidades](stream-analytics-streaming-unit-consumption.md)de streaming .

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de uma tarefa do Stream Analytics?
Uma definição de tarefa do Stream Analytics inclui entradas, uma consulta e saída. Entradas são em que a tarefa lê o fluxo de dados do. A consulta é utilizada para transformar o fluxo de entrada de dados e a saída é onde a tarefa envia os resultados das tarefas para.

Uma tarefa requer pelo menos uma origem de entrada de dados de transmissão em fluxo. A origem de entrada de fluxo de dados pode ser armazenada num hub de eventos do Azure ou no armazenamento de Blobs do Azure. Para mais informações, consulte [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md) e Get [começou a usar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partições em origens e sinks
Dimensionar uma tarefa do Stream Analytics tira partido das partições na entrada ou saída. Criação de partições permite que dividir os dados em subconjuntos com base numa chave de partição. Um processo que consome os dados (por exemplo, uma tarefa de análise de transmissão em fluxo) pode consumir e escrever diferentes partições em paralelo, o que aumenta o débito. 

### <a name="inputs"></a>Entradas
Todas as entradas do Azure Stream Analytics podem tirar partido da criação de partições:
-   EventHub (é necessário definir a chave de partição explicitamente com a palavra-chave por PARTIÇÃO)
-   IoT Hub (é necessário definir a chave de partição explicitamente com a palavra-chave por PARTIÇÃO)
-   Armazenamento de blobs

### <a name="outputs"></a>Saídas

Quando trabalha com o Stream Analytics, pode aproveitar as saídas de criação de partições:
-   Armazenamento do Azure Data Lake
-   Funções do Azure
-   Tabela do Azure
-   Armazenamento de BLOBs (pode definir a chave de partição explicitamente)
-   Cosmos DB (necessidade de definir explicitamente a chave de partição)
-   Centros de Eventos (necessidade de definir explicitamente a chave de partição)
-   IoT Hub (é necessário definir explicitamente a chave de partição)
-   Service Bus
- Armazém de Dados SQL e SQL com divisória opcional: consulte mais informações sobre a página de Base de [Dados Azure SQL](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

O Power BI não suporta a partilha. No entanto, ainda pode dividir a entrada como descrito [nesta secção](#multi-step-query-with-different-partition-by-values) 

Para obter mais informações sobre as partições, consulte os artigos seguintes:

* [Descrição geral das funcionalidades dos Hubs de Eventos](../event-hubs/event-hubs-features.md#partitions)
* [Criação de partições de dados](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Tarefas constrangedoramente paralelas
Um trabalho *embaraçosamente paralelo* é o cenário mais escalável que temos no Azure Stream Analytics. Ele se conecta uma partição de entrada para uma instância da consulta para uma partição de saída. Esse paralelismo tem os seguintes requisitos:

1. Se a sua lógica de consulta depende da mesma chave a ser processada pela mesma instância de consulta, deve certificar-se de que os eventos de ir para a mesma partição de sua entrada. Para os Hubs de Eventos ou IoT Hub, isto significa que os dados do evento devem ter o conjunto de valor **partitionKey.** Em alternativa, pode utilizar os remetentes particionados. Para armazenamento de BLOBs, isso significa que os eventos são enviados para a mesma pasta de partição. Se a sua lógica de consulta não requer a mesma chave a ser processado pela mesma instância de consulta, pode ignorar este requisito. Um exemplo dessa lógica seria uma simple consulta de filtro de projeto de select.  

2. Depois dos dados são dispostos no lado de entrada, deve certificar-se de que a consulta estiver particionada. Isto requer que utilize a **PARTIÇÃO POR** EM todos os passos. São permitidos vários passos, mas todos eles devem ser particionados pela mesma chave. Nos termos da compatibilidade dos níveis 1.0 e 1.1, a chave de partição deve ser fixada ao **PartitionId** para que o trabalho seja totalmente paralelo. Para trabalhos com nível de compatibilidade 1.2 e superior, a coluna personalizada pode ser especificada como Chave de Partição nas definições de entrada e o trabalho será automaticamente paralelado mesmo sem cláusula DE PARTIÇÃO POR. Para a saída do hub de eventos, a propriedade "Coluna de teclas de partição" deve ser definida para utilizar "PartitionId".

3. A maioria dos nossos saída aproveitar as vantagens do particionamento, no entanto, se usar um tipo de saída que não suporta a criação de partições de seu trabalho não será totalmente paralelo. Consulte a secção de [saída](#outputs) para mais detalhes.

4. O número de partições de entrada deve ser igual ao número de partições de saída. Saída de armazenamento de BLOBs pode oferecer suporte a partições e herda o esquema de particionamento da consulta a montante. Quando uma chave de partição para o Blob storage for especificado, os dados está particionada por partição de entrada, portanto, o resultado é ainda totalmente paralelo. Seguem-se exemplos de valores de partição que permitem que uma tarefa totalmente paralela:

   * 8 partições entrada para o event hub e o hub de eventos de 8 de saída de partições
   * 8 partições entrada para o event hub e a saída de armazenamento de BLOBs
   * 8 de partições de entrada do hub de eventos e saída de armazenamento de BLOBs particionados por um campo personalizado com cardinalidade arbitrária
   * 8 blob entrada as partições de armazenamento e saída de armazenamento de BLOBs
   * 8 8 partições de saída do hub de eventos e partições de entrada do armazenamento de BLOBs

As secções seguintes abordam alguns cenários de exemplo constrangedoramente paralelas.

### <a name="simple-query"></a>Consulta simples

* Entrada: Hub de eventos com 8 partições
* Saída: O centro de eventos com 8 divisórias ("Coluna de teclas de partição" deve ser definido para utilizar "PartitionId")

Consulta:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Esta consulta é um filtro simple. Por conseguinte, não precisamos de se preocupar sobre a criação de partições de entrada que está a ser enviada para o hub de eventos. Note que os trabalhos com nível de compatibilidade antes de 1.2 devem incluir a cláusula **Partition BY PartitionId,** pelo que preenche a exigência #2 anterior. Para a saída, precisamos configurar a saída do centro de eventos no trabalho para ter a chave de partição definida para **PartitionId**. Última verificação de um é certificar-se de que o número de partições de entrada é igual ao número de partições de saída.

### <a name="query-with-a-grouping-key"></a>Consultar com uma chave de agrupamento

* Entrada: Hub de eventos com 8 partições
* Saída: Armazenamento de BLOBs

Consulta:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Esta consulta tem uma chave de agrupamento. Por conseguinte, os eventos agrupados em conjunto têm de ser enviados para a mesma partição do Hub de eventos. Uma vez que neste exemplo, agrupar por TollBoothID, podemos deve ter certeza de que TollBoothID é utilizado como a chave de partição quando os eventos são enviados para o Hub de eventos. Em seguida, na ASA, podemos usar **partition by partitionId** para herdar deste esquema de partição e permitir uma paralelização completa. Uma vez que a saída é o armazenamento de BLOBs, não precisamos de se preocupar sobre como configurar um valor de chave de partição, de acordo com o requisito #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exemplo de cenários que *não* são embaraçosamente paralelos

Na secção anterior, mostramos alguns cenários constrangedoramente paralelos. Nesta secção, vamos abordar os cenários que não correspondam a todos os requisitos para ser constrangedoramente paralelas. 

### <a name="mismatched-partition-count"></a>Contagem de partições sem correspondência
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 32 partições

Neste caso, não importa o que é a consulta. Se o número de partições de entrada não corresponder o número de partições de saída, a topologia não constrangedoramente paralelo. + no entanto, continua a poder aceder alguns nível ou a paralelização.

### <a name="query-using-non-partitioned-output"></a>Consultar com a saída de não-particionada
* Entrada: Hub de eventos com 8 partições
* Saída: Power BI

A saída de POWER BI não suporta atualmente a partilha. Por conseguinte, este cenário não é constrangedoramente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de vários passo com diferentes valores por PARTIÇÃO
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

Consulta:

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

Como pode ver, o segundo passo usa **o TollBoothId** como a chave de partição. Este passo não é o mesmo que o primeiro passo e, portanto, requer-nos fazer um shuffle. 

Os exemplos anteriores mostram algumas tarefas do Stream Analytics que estão em conformidade com (ou não) uma topologia constrangedoramente paralela. Se eles estão em conformidade com, eles têm o potencial para dimensionamento máximo. Atualiza para tarefas que não se ajustam um desses perfis, orientações de dimensionamento estará disponíveis no futuro. Por agora, utilize a documentação de orientação geral nas seções a seguir.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Compatibilidade nível 1.2 - Consulta em várias etapas com diferentes valores DE PARTIÇÃO 
* Entrada: Hub de eventos com 8 partições
* Saída: O centro de eventos com 8 divisórias ("Coluna de teclas de partição" deve ser definido para utilizar "TollBoothId")

Consulta:

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

A compatibilidade do nível 1.2 permite a execução de consulta paralela por defeito. Por exemplo, a consulta da secção anterior será parcialmente parcialmente, desde que a coluna "TollBoothId" seja definida como chave de partição de entrada. A cláusula partição por PartetionId não é necessária.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular o máximo de unidades de uma tarefa de transmissão em fluxo
O número total de unidades de transmissão em fluxo que pode ser utilizado por uma tarefa do Stream Analytics depende o número de passos da consulta definida para a tarefa e o número de partições para cada passo.

### <a name="steps-in-a-query"></a>Passos numa consulta
Uma consulta pode ter um ou vários passos. Cada passo é um subquery definido pela palavra-chave **COM.** A consulta que está fora da palavra-chave **COM** (apenas uma consulta) também é contada como um passo, como a declaração **SELECT** na seguinte consulta:

Consulta:

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

Esta consulta tem duas etapas.

> [!NOTE]
> Esta consulta é abordada em mais detalhes posteriormente neste artigo.
>  

### <a name="partition-a-step"></a>Um passo de partição
Um passo de criação de partições requer as seguintes condições:

* A origem de entrada têm de ser particionada. 
* A declaração **SELECT** da consulta deve ser lida a partir de uma fonte de entrada dividida.
* A consulta dentro do degrau deve ter a **palavra-chave partition by.**

Quando uma consulta estiver particionada, os eventos de entrada são processados e agregados numa partição separada grupos e saídas de eventos são gerados para cada um dos grupos. Se quiser um agregado combinado, tem de criar uma segunda etapa de não-particionada a agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular o máximo de unidades para uma tarefa de transmissão em fluxo
Em conjunto, todos os passos de não-particionada podem Dimensionar até seis unidades transmissão em fluxo (SUs) para uma tarefa do Stream Analytics. Além disso, pode adicionar 6 SUs para cada partição num passo particionado.
Pode ver **alguns exemplos** na tabela abaixo.

| Consulta                                               | Máx. de SUs para a tarefa |
| --------------------------------------------------- | ------------------- |
| <ul><li>A consulta contém uma etapa.</li><li>O passo não está particionado.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é particionado por 16.</li><li>A consulta contém uma etapa.</li><li>O passo está particionado.</li></ul> | 96 (6 * 16 partições) |
| <ul><li>A consulta contém duas etapas.</li><li>Nenhum dos passos é particionado.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é particionado por 3.</li><li>A consulta contém duas etapas. O passo de entrada é particionado e não é o segundo passo.</li><li>A declaração <strong>SELECT</strong> lê-se na entrada dividida.</li></ul> | 24 (18 para obter os passos particionados + 6 para obter os passos de não-particionada |

### <a name="examples-of-scaling"></a>Exemplos de dimensionamento

A seguinte consulta calcula o número de carros durante um período de três minutos através de uma estação de ligação que tem três tollbooths. Esta consulta pode ser dimensionada até seis SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Para utilizar mais de SUs para a consulta, o fluxo de dados de entrada e a consulta devem ser particionados. Uma vez que a partição de fluxo de dados está definida como 3, a seguinte consulta modificada pode ser dimensionada até 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Quando uma consulta estiver particionada, os eventos de entrada são processados e agregados em grupos de partições separado. Eventos de saída também são gerados para cada um dos grupos. A partilha pode causar alguns resultados inesperados quando o campo **GROUP BY** não é a chave de partição no fluxo de dados de entrada. Por exemplo, o campo **TollBoothId** na consulta anterior não é a chave de partição do **Input1**. O resultado é que os dados a partir de TollBooth 1 possam ser distribuídos em várias partições.

Cada uma das divisórias **Input1** será processada separadamente pelo Stream Analytics. Como resultado, serão criados vários registos da contagem de carro para o mesmo tollbooth na mesma janela em cascata. Se a chave de partição de entrada não pode ser alterada, esse problema pode ser corrigido adicionando um passo de não-partition para valores agregados em partições, como no exemplo seguinte:

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

Esta consulta pode ser dimensionada até 24 SUs.

> [!NOTE]
> Se estiver associando dois fluxos, certifique-se de que os fluxos estão particionados pela chave de partição da coluna que utiliza para criar as junções. Além disso, certifique-se de que tem o mesmo número de partições em ambos os fluxos.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Conseguir maiores resultados em escala

Um trabalho [embaraçosamente paralelo](#embarrassingly-parallel-jobs) é necessário, mas não suficiente para sustentar uma maior entrada em escala. Cada sistema de armazenamento e a sua saída correspondente do Stream Analytics têm variações sobre como obter a melhor produção de escrita possível. Como em qualquer cenário em escala, existem alguns desafios que podem ser resolvidos usando as configurações certas. Esta secção discute configurações para algumas saídas comuns e fornece amostras para manter taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

As seguintes observações usam um trabalho de Stream Analytics com consulta apátrida (passthrough), uma UDF javascript básica que escreve para Event Hub, Azure SQL DB ou Cosmos DB.

#### <a name="event-hub"></a>Hub de Eventos

|Taxa de ingestão (eventos por segundo) | Unidades de Streaming | Recursos de Saída  |
|--------|---------|---------|
| 1K     |    1    |  2 TU   |
| 5K     |    6    |  6 TU   |
| 10K    |    12   |  10 TU  |

A solução [Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) escala linearmente em termos de unidades de streaming (SU) e de produção, tornando-a a forma mais eficiente e performativa de analisar e transmitir dados a partir do Stream Analytics. Os postos de trabalho podem ser dimensionados até 192 SU, o que se traduz aproximadamente no processamento de até 200 MB/s, ou 19 triliões de eventos por dia.

#### <a name="azure-sql"></a>SQL do Azure
|Taxa de ingestão (eventos por segundo) | Unidades de Streaming | Recursos de Saída  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[O Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) suporta a escrita em paralelo, chamado Partição Herdada, mas não está ativado por padrão. No entanto, permitir a partilha herdada, juntamente com uma consulta totalmente paralela, pode não ser suficiente para obter maiores resultados. Os sql write as rubricas dependem significativamente da configuração da base de dados SQL Azure e do esquema de mesa. O artigo [sQL Output Performance](./stream-analytics-sql-output-perf.md) tem mais detalhes sobre os parâmetros que podem maximizar a sua produção de escrita. Como se nota na saída do Azure Stream Analytics para o artigo da Base de [Dados Azure SQL,](./stream-analytics-sql-output-perf.md#azure-stream-analytics) esta solução não escala linearmente como um gasoduto totalmente paralelo para além de 8 divisórias e pode precisar de ser repartitentada antes da saída do SQL (ver [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). As SKUs premium são necessárias para manter altas taxas de IO, juntamente com as despesas gerais de backups de registo acontecendo a cada poucos minutos.

#### <a name="cosmos-db"></a>BD do Cosmos
|Taxa de ingestão (eventos por segundo) | Unidades de Streaming | Recursos de Saída  |
|-------|-------|---------|
|  1K   |  3    | 20K RU  |
|  5K   |  24   | 60K RU  |
|  10K  |  48   | 120K RU |

A saída [cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) do Stream Analytics foi atualizada para utilizar a integração nativa sob o nível de [compatibilidade 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). O nível de compatibilidade 1.2 permite uma maior absorção e reduz o consumo de RU em comparação com 1,1, que é o nível de compatibilidade padrão para novos empregos. A solução utiliza recipientes CosmosDB divididos em /dispositivoId e o resto da solução é configurado de forma idêntica.

Todas as [amostras de streaming em scale azure](https://github.com/Azure-Samples/streaming-at-scale) usam um Hub de evento alimentado por simulação de carga clientes de teste como entrada. Cada evento de entrada é um documento JSON de 1KB, que traduz taxas de ingestão configuradas para taxas de produção (1MB/s, 5MB/s e 10MB/s) facilmente. Os eventos simulam um dispositivo IoT enviando os seguintes dados JSON (de forma encurtada) para dispositivos até 1K:

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
> As configurações estão sujeitas a alterações devido aos vários componentes utilizados na solução. Para obter uma estimativa mais precisa, personalize as amostras para se adaptar ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificação de Gargalos

Utilize o painel Metrics no seu trabalho azure Stream Analytics para identificar estrangulamentos no seu pipeline. Reveja os eventos de **entrada/saída** para a produção e ["Watermark Delay" ou "Watermark Delay"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **Eventos atrasados** para ver se o trabalho está a acompanhar a taxa de entrada. Para as métricas do Event Hub, procure **pedidos throttled** e ajuste as Unidades threshold em conformidade. Para as métricas Cosmos DB, reveja **a Max consumiu RU/s por gama** de teclas de partição em Âmbito de Entrada para garantir que as suas gamas-chave de partição são uniformemente consumidas. Para O Azure SQL DB, monitorize **o Log IO** e **o CPU**.

## <a name="get-help"></a>Obter ajuda

Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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


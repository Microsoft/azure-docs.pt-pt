---
title: Utilize a paralelização e escala de consulta em Azure Stream Analytics
description: Este artigo descreve como escalar os trabalhos do Stream Analytics configurando divisórias de entrada, afinando a definição de consulta e definindo unidades de streaming de emprego.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 326af3bc38ce70cc7cb205384bb4302c5ff73d28
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704185"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Paralelização de consulta de alavancagem em Azure Stream Analytics
Este artigo mostra-lhe como aproveitar a paralelização no Azure Stream Analytics. Aprende-se a escalar os trabalhos do Stream Analytics configurando divisórias de entrada e ajustando a definição de consulta analítica.
Como pré-requisito, pode querer estar familiarizado com a noção de Unidade de Streaming descrita em [Compreender e ajustar Unidades de Streaming](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de um trabalho de Stream Analytics?
Uma definição de trabalho stream Analytics inclui pelo menos uma entrada de streaming, uma consulta e uma saída. As entradas são de onde o trabalho lê o fluxo de dados. A consulta é usada para transformar o fluxo de entrada de dados, e a saída é para onde o trabalho envia os resultados do trabalho.

## <a name="partitions-in-inputs-and-outputs"></a>Divisórias em entradas e saídas
A partilha permite dividir os dados em subconjuntos com base numa [chave de partição](../event-hubs/event-hubs-scalability.md#partitions). Se a sua entrada (por exemplo, Centros de Eventos) for dividida por uma chave, é altamente recomendado especificar esta chave de partição ao adicionar a entrada ao seu trabalho stream Analytics. Escalar um trabalho stream Analytics tira partido das divisórias na entrada e na saída. Um trabalho stream Analytics pode consumir e escrever diferentes divisórias em paralelo, o que aumenta a produção. 

### <a name="inputs"></a>Entradas
Toda a entrada Azure Stream Analytics pode tirar partido da partição:
-   EventHub (necessidade de definir a chave de partição explicitamente com palavra-chave PARTITION BY se utilizar o nível de compatibilidade 1.1 ou inferior)
-   IoT Hub (necessidade de definir explicitamente a chave de partição com palavra-chave PARTITION BY se utilizar o nível de compatibilidade 1.1 ou inferior)
-   Armazenamento de blobs

### <a name="outputs"></a>Saídas

Quando trabalha com o Stream Analytics, pode aproveitar a partilha nas saídas:
-   Armazenamento do Azure Data Lake
-   Funções do Azure
-   Tabela do Azure
-   Armazenamento de bolhas (pode definir a chave de partição explicitamente)
-   Cosmos DB (necessidade de definir explicitamente a chave de partição)
-   Centros de Eventos (necessidade de definir explicitamente a chave de partição)
-   IoT Hub (necessidade de definir explicitamente a chave de partição)
-   Service Bus
- SQL e Azure Synapse Analytics com partição opcional: ver mais informações sobre a [página de Base de Dados Azure SQL](./stream-analytics-sql-output-perf.md).

O Power BI não suporta a divisão. No entanto, ainda pode dividir a entrada como descrito [nesta secção](#multi-step-query-with-different-partition-by-values) 

Para obter mais informações sobre divisórias, consulte os seguintes artigos:

* [Descrição geral das funcionalidades dos Hubs de Eventos](../event-hubs/event-hubs-features.md#partitions)
* [Criação de partições de dados](/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Trabalhos embaraçosos paralelos
Um trabalho *embaraçoso paralelo* é o cenário mais escalável em Azure Stream Analytics. Liga uma partição da entrada a uma instância da consulta a uma divisória da saída. Este paralelismo tem os seguintes requisitos:

1. Se a sua lógica de consulta depende da mesma chave ser processada pela mesma instância de consulta, deve certificar-se de que os eventos vão para a mesma divisão da sua entrada. Para Os Centros de Eventos ou IoT Hub, isto significa que os dados do evento devem ter o conjunto de valor **PartitionKey.** Em alternativa, pode utilizar remetentes de divisórias. Para o armazenamento de bolhas, isto significa que os eventos são enviados para a mesma pasta de partição. Um exemplo seria uma instância de consulta que agrega dados por userID onde o hub do evento de entrada é dividido usando o userID como chave de partição. No entanto, se a sua lógica de consulta não necessitar da mesma chave para ser processada pela mesma instância de consulta, pode ignorar este requisito. Um exemplo desta lógica seria uma consulta simples de filtro de projeto selecionado.  

2. O próximo passo é fazer a sua consulta ser dividida. Para trabalhos com compatibilidade nível 1.2 ou superior (recomendado), a coluna personalizada pode ser especificada como Chave de Partição nas definições de entrada e o trabalho será paralellado automaticamente. Trabalhos com compatibilidade nível 1.0 ou 1.1, requer que você use **partition BY PartitionId** em todos os passos da sua consulta. Vários passos são permitidos, mas todos devem ser divididos pela mesma chave. 

3. A maioria das saídas suportadas no Stream Analytics podem tirar partido da partição. Se usar um tipo de saída que não apoie a partilha do seu trabalho não será *embaraçoso.* Para as saídas do Event Hub, certifique-se de que a **coluna-chave de partição** está definida para a mesma tecla de partição utilizada na consulta. Consulte a [secção de saída](#outputs) para mais detalhes.

4. O número de divisórias de entrada deve ser igual ao número de divisórias de saída. A saída de armazenamento de bolhas pode suportar divisórias e herdar o esquema de partição da consulta a montante. Quando uma chave de partição para armazenamento blob é especificada, os dados são divididos por partição de entrada, pelo que o resultado ainda é totalmente paralelo. Aqui estão exemplos de valores de partição que permitem um trabalho totalmente paralelo:

   * 8 divisórias de entrada de hub de evento e 8 divisórias de saída de hub de evento
   * 8 divisórias de entrada de hub de evento e saída de armazenamento de bolhas
   * 8 divisórias de entrada de hub de evento e saída de armazenamento de bolhas divididas por um campo personalizado com cardinalidade arbitrária
   * 8 divisórias de entrada de armazenamento de bolhas e saída de armazenamento de bolhas
   * 8 divisórias de entrada de armazenamento de bolhas e 8 divisórias de saída de centro de evento

As secções seguintes discutem alguns cenários de exemplo que são embaraçosamente paralelos.

### <a name="simple-query"></a>Consulta simples

* Entrada: Centro de eventos com 8 divisórias
* Saída: O centro de eventos com 8 divisórias ("Coluna-chave de partição" deve ser configurado para utilizar "PartitionId")

Consulta:

```SQL
    --Using compatibility level 1.2 or above
    SELECT TollBoothId
    FROM Input1
    WHERE TollBoothId > 100
    
    --Using compatibility level 1.0 or 1.1
    SELECT TollBoothId
    FROM Input1 PARTITION BY PartitionId
    WHERE TollBoothId > 100
```

Esta consulta é um filtro simples. Portanto, não precisamos nos preocupar em dividir a entrada que está sendo enviada para o centro de eventos. Note que os postos de trabalho com nível de compatibilidade antes do 1.2 devem incluir a cláusula **PARTITION BY PartitionId,** pelo que preenche a exigência #2 anterior. Para a saída, precisamos configurar a saída do centro de eventos no trabalho para ter a chave de partição definida para **PartitionId**. Uma última verificação é para garantir que o número de divisórias de entrada seja igual ao número de divisórias de saída.

### <a name="query-with-a-grouping-key"></a>Consulta com uma chave de agrupamento

* Entrada: Centro de eventos com 8 divisórias
* Saída: Armazenamento de bolhas

Consulta:

```SQL
    --Using compatibility level 1.2 or above
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
    --Using compatibility level 1.0 or 1.1
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Esta consulta tem uma chave de agrupamento. Por isso, os eventos agrupados devem ser enviados para a mesma partição do Event Hub. Uma vez que neste exemplo agruparemos a TollBoothID, devemos ter a certeza de que o TollBoothID é usado como chave de partição quando os eventos são enviados para o Event Hub. Em seguida, na ASA, podemos usar **partição by partitionId** para herdar deste esquema de partição e permitir a total paralelização. Uma vez que a saída é armazenamento de bolhas, não precisamos nos preocupar em configurar um valor chave de partição, de acordo com o requisito #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exemplo de cenários que *não* são embaraçosamente paralelos

Na secção anterior, mostrámos alguns cenários embaraçosos e paralelos. Nesta secção, discutimos cenários que não cumprem todos os requisitos para serem embaraçosamente paralelos. 

### <a name="mismatched-partition-count"></a>Contagem de divisórias desajustadas
* Entrada: Centro de eventos com 8 divisórias
* Saída: Centro de eventos com 32 divisórias

Se a contagem de partição de entrada não corresponder à contagem de partição de saída, a topologia não é embaraçosamente paralela, independentemente da consulta. No entanto, ainda podemos obter algum nível ou paralelização.

### <a name="query-using-non-partitioned-output"></a>Consulta utilizando saída não dividida
* Entrada: Centro de eventos com 8 divisórias
* Saída: Power BI

A saída de Power BI não suporta atualmente a partição. Portanto, este cenário não é embaraçoso paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta em várias etapas com diferentes partição por valores
* Entrada: Centro de eventos com 8 divisórias
* Saída: Centro de eventos com 8 divisórias
* Nível de compatibilidade: 1.0 ou 1.1

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

Como pode ver, o segundo passo usa **o TollBoothId** como chave de partição. Este passo não é o mesmo que o primeiro passo, pelo que nos obriga a fazer uma baralhada. 

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta em várias etapas com diferentes partição por valores
* Entrada: Centro de eventos com 8 divisórias
* Saída: O centro de eventos com 8 divisórias ("Coluna-chave de partição" deve ser configurado para utilizar "TollBoothId")
* Nível de compatibilidade - 1.2 ou superior

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

O nível de compatibilidade 1.2 ou superior permite a execução paralela por padrão. Por exemplo, a consulta da secção anterior será dividida enquanto a coluna "TollBoothId" for definida como chave de partição de entrada. Não é necessária a cláusula partitionida.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular as unidades de streaming máximas de um trabalho
O número total de unidades de streaming que podem ser utilizadas por um trabalho stream Analytics depende do número de passos na consulta definida para o trabalho e do número de divisórias para cada passo.

### <a name="steps-in-a-query"></a>Passos em consulta
Uma consulta pode ter um ou muitos passos. Cada passo é um subquery definido pela palavra-chave **COM.** A consulta que está fora da palavra-chave **COM** (apenas uma consulta) é contada como um passo, tal como a declaração **SELECT** na seguinte consulta:

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

Esta consulta tem dois passos.

> [!NOTE]
> Esta consulta é discutida mais detalhadamente mais tarde no artigo.
>  

### <a name="partition-a-step"></a>Partição um passo
A partilha de um passo requer as seguintes condições:

* A fonte de entrada deve ser dividida. 
* A **declaração SELECT** da consulta deve ser lida a partir de uma fonte de entrada dividida.
* A consulta dentro do passo deve ter a **partição por** palavra-chave.

Quando uma consulta é dividida, os eventos de entrada são processados e agregados em grupos de partição separados, e eventos de saídas são gerados para cada um dos grupos. Se quiser um agregado combinado, deve criar um segundo passo não dividido para agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular as unidades de streaming máximas para um trabalho
Todos os passos não divididos em conjunto podem escalar até seis unidades de streaming (SUs) para um trabalho stream Analytics. Além disso, pode adicionar 6 SUs para cada partição num passo dividido.
Pode ver **alguns exemplos** na tabela abaixo.

| Consulta                                               | Max SUs para o trabalho |
| --------------------------------------------------- | ------------------- |
| <ul><li>A consulta contém um passo.</li><li>O passo não é dividido.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é dividido por 16.</li><li>A consulta contém um passo.</li><li>O passo está dividido.</li></ul> | 96 (6 * 16 divisórias) |
| <ul><li>A consulta contém dois passos.</li><li>Nenhum dos degraus está dividido.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é dividido por 3.</li><li>A consulta contém dois passos. O passo de entrada é dividido e o segundo passo não.</li><li>A declaração <strong>SELECT</strong> lê a partir da entrada dividida.</li></ul> | 24 (18 para etapas divididas + 6 para etapas não divididas |

### <a name="examples-of-scaling"></a>Exemplos de escala

A seguinte consulta calcula o número de carros dentro de uma janela de três minutos passando por uma portagem que tem três portagens. Esta consulta pode ser dimensionada até seis SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Para utilizar mais SUs para a consulta, tanto o fluxo de dados de entrada como a consulta devem ser divididos. Uma vez que a partição do fluxo de dados é definida para 3, a seguinte consulta modificada pode ser dimensionada até 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Quando uma consulta é dividida, os eventos de entrada são processados e agregados em grupos de partição separados. Os eventos de saída também são gerados para cada um dos grupos. A partição pode causar alguns resultados inesperados quando o campo **GROUP BY** não é a chave de partição no fluxo de dados de entrada. Por exemplo, o campo **TollBoothId** na consulta anterior não é a chave de partição do **Input1**. O resultado é que os dados da TollBooth #1 podem ser espalhados em várias divisórias.

Cada uma das divisórias **Input1** será processada separadamente pela Stream Analytics. Como resultado, vários registos da contagem de carros para a mesma portagem na mesma janela Tumbling serão criados. Se a chave de partição de entrada não puder ser alterada, este problema pode ser corrigido adicionando um passo de não partição aos valores agregados entre divisórias, como no exemplo seguinte:

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

Esta consulta pode ser dimensionada para 24 SUs.

> [!NOTE]
> Se estiver a unir dois fluxos, certifique-se de que os fluxos são divididos pela chave de partição da coluna que utiliza para criar as juntas. Certifique-se também de que tem o mesmo número de divisórias em ambos os riachos.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Alcançar posições mais elevadas à escala

Um trabalho [embaraçoso paralelo](#embarrassingly-parallel-jobs) é necessário, mas não suficiente para sustentar uma produção mais elevada à escala. Cada sistema de armazenamento e a sua saída stream Analytics correspondente tem variações sobre como alcançar o melhor rendimento de escrita possível. Como em qualquer cenário em escala, existem alguns desafios que podem ser resolvidos utilizando as configurações certas. Esta secção discute configurações para algumas saídas comuns e fornece amostras para manter taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

As seguintes observações usam um trabalho stream Analytics com consulta apátrida (passthrough), um JavaScript UDF básico que escreve para Event Hub, Azure SQL DB, ou Cosmos DB.

#### <a name="event-hub"></a>Hub de Eventos

|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de Saída  |
|--------|---------|---------|
| Mil     |    1    |  2 TU   |
| 5 m.     |    6    |  6 TU   |
| 10 K    |    12   |  10 TU  |

A solução [Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-eventhubs) escala linearmente em termos de unidades de streaming (SU) e produção, tornando-a a forma mais eficiente e performante de analisar e transmitir dados para fora do Stream Analytics. Os postos de trabalho podem ser dimensionado até 192 SU, o que se traduz aproximadamente no processamento de até 200 MB/s, ou 19 triliões de eventos por dia.

#### <a name="azure-sql"></a>SQL do Azure
|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de Saída  |
|---------|------|-------|
|    Mil   |   3  |  S3   |
|    5 m.   |   18 |  P4   |
|    10 K  |   36 |  P6   |

[O Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-azuresql)  suporta a escrita em paralelo, chamada Partição Herdada, mas não é ativada por defeito. No entanto, permitir a partilha herdada, juntamente com uma consulta totalmente paralela, pode não ser suficiente para obter posições mais elevadas. Os produção de escrita SQL dependem significativamente da configuração da sua base de dados e do esquema de tabela. O artigo [SQL Output Performance](./stream-analytics-sql-output-perf.md) tem mais detalhes sobre os parâmetros que podem maximizar o seu rendimento de escrita. Como indicado na saída Azure Stream Analytics para o artigo [base de dados Azure SQL,](./stream-analytics-sql-output-perf.md#azure-stream-analytics) esta solução não escala linearmente como um gasoduto totalmente paralelo para além de 8 divisórias e pode necessitar de repartição antes da saída SQL (ver [INTO).](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) Os SKUs premium são necessários para manter altas taxas de IO, juntamente com a sobrecarga de backups de registos que acontecem a cada poucos minutos.

#### <a name="cosmos-db"></a>BD do Cosmos
|Taxa de ingestão (eventos por segundo) | Unidades de streaming | Recursos de Saída  |
|-------|-------|---------|
|  Mil   |  3    | 20K RU  |
|  5 m.   |  24   | 60K RU  |
|  10 K  |  48   | 120K RU |

A produção [de CosS da](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb) Stream Analytics foi atualizada para utilizar a integração nativa no [nível de compatibilidade 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). O nível de compatibilidade 1.2 permite uma produção significativamente mais elevada e reduz o consumo de RU em comparação com 1.1, que é o nível de compatibilidade padrão para novos postos de trabalho. A solução utiliza recipientes CosmosDB divididos em /deviceId e o resto da solução está configurado de forma idêntica.

Todas as [amostras de streaming na Scale Azure](https://github.com/Azure-Samples/streaming-at-scale) usam um Event Hub como entrada que é alimentada por clientes de teste simuladores de carga. Cada evento de entrada é um documento JSON de 1KB, que traduz taxas de ingestão configuradas para taxas de produção (1MB/s, 5MB/s e 10MB/s) facilmente. Os eventos simulam um dispositivo IoT enviando os seguintes dados JSON (de forma encurtada) para dispositivos até 1K:

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
> As configurações estão sujeitas a alterações devido aos vários componentes utilizados na solução. Para uma estimativa mais precisa, personalize as amostras para se adaptar ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificar estrangulamentos

Utilize o painel métrica no seu trabalho Azure Stream Analytics para identificar estrangulamentos no seu oleoduto. **Reveja os eventos de entrada/saída** para produção e ["Atraso de marca de água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **eventos retrospados** para ver se o trabalho está a acompanhar a taxa de entrada. Para as métricas do Event Hub, procure **pedidos de aceleração** e ajuste as Unidades limiares em conformidade. Para as métricas da Cosmos DB, reveja **max consumiu RU/s por intervalo de chaves de partição** sob a produção para garantir que as suas gamas de chaves de partição são consumidas uniformemente. Para Azure SQL DB, monitorize **Log IO** e **CPU**.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
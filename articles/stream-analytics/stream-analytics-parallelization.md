---
title: Utilize paralelização e escala de consulta no Azure Stream Analytics
description: Este artigo descreve como dimensionar os trabalhos do Stream Analytics configurando divisórias de entrada, afinando a definição de consulta e definindo unidades de streaming de trabalho.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 5bae53c04867233138929867c4895e7f6a2f2149
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838778"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Alavancar a paralelização da consulta no Azure Stream Analytics
Este artigo mostra-lhe como aproveitar a paralelização no Azure Stream Analytics. Você aprende a dimensionar os trabalhos do Stream Analytics configurando divisórias de entrada e afinando a definição de consulta de análise.
Como pré-requisito, pode querer estar familiarizado com a noção de Unidade de Streaming descrita em [Compreender e ajustar unidades](stream-analytics-streaming-unit-consumption.md)de streaming .

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de um trabalho de Stream Analytics?
Uma definição de trabalho stream Analytics inclui pelo menos uma entrada de streaming, uma consulta e saída. As inputs são de onde o trabalho lê o fluxo de dados. A consulta é usada para transformar o fluxo de entrada de dados, e a saída é para onde o trabalho envia os resultados do trabalho.

## <a name="partitions-in-inputs-and-outputs"></a>Divisórias em inputs e saídas
A partilha permite dividir os dados em subconjuntos com base numa [chave de partição](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#partitions). Se a sua entrada (por exemplo, Os Centros de Eventos) for dividida por uma chave, é altamente recomendável especificar esta chave de partição ao adicionar entrada ao seu trabalho de Stream Analytics. Dimensionar um trabalho de Stream Analytics tira partido das divisórias na entrada e saída. Um trabalho de Stream Analytics pode consumir e escrever diferentes divisórias em paralelo, o que aumenta a entrada. 

### <a name="inputs"></a>Entradas
Toda a entrada da Azure Stream Analytics pode tirar partido da partilha:
-   EventHub (necessidade de definir explicitamente a chave de partição com palavra-chave PARTITION BY se utilizar o nível de compatibilidade 1.1 ou abaixo)
-   IoT Hub (necessidade de definir explicitamente a chave de partição com palavra-chave PARTITION BY se utilizar o nível de compatibilidade 1.1 ou abaixo)
-   Armazenamento de blobs

### <a name="outputs"></a>Saídas

Quando trabalha com o Stream Analytics, pode aproveitar a partilha nas saídas:
-   Armazenamento do Azure Data Lake
-   Funções do Azure
-   Tabela do Azure
-   Armazenamento blob (pode definir explicitamente a chave de partição)
-   Cosmos DB (necessidade de definir explicitamente a chave de partição)
-   Centros de Eventos (necessidade de definir explicitamente a chave de partição)
-   IoT Hub (necessidade de definir explicitamente a chave de partição)
-   Service Bus
- Armazém de Dados SQL e SQL com divisória opcional: consulte mais informações sobre a página de Base de [Dados Azure SQL](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

O Power BI não suporta a partilha. No entanto, ainda pode dividir a entrada como descrito [nesta secção](#multi-step-query-with-different-partition-by-values) 

Para obter mais informações sobre divisórias, consulte os seguintes artigos:

* [Descrição geral das funcionalidades dos Hubs de Eventos](../event-hubs/event-hubs-features.md#partitions)
* [Criação de partições de dados](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Empregos embaraçosamente paralelos
Um trabalho *embaraçosamente paralelo* é o cenário mais escalável no Azure Stream Analytics. Liga uma divisória da entrada a uma instância da consulta a uma partição da saída. Este paralelismo tem os seguintes requisitos:

1. Se a sua lógica de consulta depender da mesma chave que está a ser processada pela mesma instância de consulta, deve certificar-se de que os eventos vão para a mesma divisão da sua entrada. Para os Hubs de Eventos ou IoT Hub, isto significa que os dados do evento devem ter o conjunto de valor **partitionKey.** Em alternativa, pode utilizar remetentes divididos. Para armazenamento de bolhas, isto significa que os eventos são enviados para a mesma pasta de partição. Um exemplo seria uma instância de consulta que agrega dados por utilizador ID onde o centro de eventos de entrada é dividido usando o userID como chave de partição. No entanto, se a sua lógica de consulta não exigir que a mesma chave seja processada pela mesma instância de consulta, pode ignorar esta exigência. Um exemplo desta lógica seria uma simples consulta de filtro de projeto selecionado.  

2. O próximo passo é fazer a sua consulta ser dividida. Para trabalhos com o nível de compatibilidade 1.2 ou superior (recomendado), a coluna personalizada pode ser especificada como Chave de Partição nas definições de entrada e o trabalho será automaticamente parapente. Os trabalhos com compatibilidade nível 1.0 ou 1.1, exigem que utilize **partitionid por partição** em todos os passos da sua consulta. São permitidos vários passos, mas todos devem ser divididos pela mesma chave. 

3. A maioria das saídas suportadas no Stream Analytics podem tirar partido da partilha. Se utilizar um tipo de saída que não apoie a divisão do seu trabalho não será *embaraçosamente paralelo*. Para as saídas do Event Hub, certifique-se de que a **coluna de teclas de partição** está definida na mesma tecla de partição utilizada na consulta. Consulte a secção de [saída](#outputs) para mais detalhes.

4. O número de divisórias de entrada deve igualar o número de divisórias de saída. A saída de armazenamento blob pode suportar divisórias e herdar o esquema de partição da consulta a montante. Quando é especificada uma chave de partição para o armazenamento blob, os dados são divididos por divisória de entrada, pelo que o resultado ainda é totalmente paralelo. Aqui estão exemplos de valores de partição que permitem um trabalho totalmente paralelo:

   * 8 divisórias de entrada de centro de eventos e 8 divisórias de saída do hub de eventos
   * 8 divisórias de entrada de centro de eventos e saída de armazenamento de blob
   * 8 divisórias de entrada de centro de eventos e saída de armazenamento de blob dividida por um campo personalizado com cardinalidade arbitrária
   * 8 divisórias de entrada de armazenamento blob e saída de armazenamento de bolhas
   * 8 divisórias de entrada de armazenamento blob e 8 divisórias de saída do hub de eventos

As seguintes secções discutem alguns cenários de exemplo que são embaraçosamente paralelos.

### <a name="simple-query"></a>Consulta simples

* Entrada: Centro de eventos com 8 divisórias
* Saída: O centro de eventos com 8 divisórias ("Coluna de teclas de partição" deve ser definido para utilizar "PartitionId")

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

Esta consulta é um filtro simples. Portanto, não precisamos de nos preocupar em dividir a entrada que está sendo enviada para o centro do evento. Note que os trabalhos com nível de compatibilidade antes de 1.2 devem incluir a cláusula **Partition BY PartitionId,** pelo que preenche a exigência #2 anterior. Para a saída, precisamos configurar a saída do centro de eventos no trabalho para ter a chave de partição definida para **PartitionId**. Uma última verificação é para se certificar de que o número de divisórias de entrada é igual ao número de divisórias de saída.

### <a name="query-with-a-grouping-key"></a>Consulta com uma chave de agrupamento

* Entrada: Centro de eventos com 8 divisórias
* Saída: Armazenamento blob

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

Esta consulta tem uma chave de agrupamento. Por conseguinte, os eventos agrupados devem ser enviados para a mesma divisão do Event Hub. Uma vez que neste exemplo agrupamo-nos pela TollBoothID, devemos ter a certeza de que o TollBoothID é usado como chave de partição quando os eventos são enviados para o Event Hub. Em seguida, na ASA, podemos usar **partition by partitionId** para herdar deste esquema de partição e permitir uma paralelização completa. Uma vez que a saída é armazenamento de bolhas, não precisamos de nos preocupar em configurar um valor chave de divisória, de acordo com o requisito #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exemplo de cenários que *não* são embaraçosamente paralelos

Na secção anterior, mostrámos alguns cenários embaraçosamente paralelos. Nesta secção, discutimos cenários que não cumprem todos os requisitos para serem embaraçosamente paralelos. 

### <a name="mismatched-partition-count"></a>Contagem de divisórias desajustada
* Entrada: Centro de eventos com 8 divisórias
* Saída: Centro de eventos com 32 divisórias

Se a contagem de divisórias de entrada não corresponder à contagem de divisórias de saída, a topologia não é embaraçosamente paralela independentemente da consulta. No entanto, ainda podemos obter algum nível ou paralelização.

### <a name="query-using-non-partitioned-output"></a>Consulta utilizando saída não dividida
* Entrada: Centro de eventos com 8 divisórias
* Saída: Power BI

A saída de POWER BI não suporta atualmente a partilha. Portanto, este cenário não é embaraçosamente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta em várias etapas com diferentes valores de partição
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

Como pode ver, o segundo passo usa **o TollBoothId** como a chave de partição. Este passo não é o mesmo que o primeiro passo, pelo que nos obriga a fazer uma confusão. 

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta em várias etapas com diferentes valores de partição
* Entrada: Centro de eventos com 8 divisórias
* Saída: O centro de eventos com 8 divisórias ("Coluna de teclas de partição" deve ser definido para utilizar "TollBoothId")
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

A compatibilidade do nível 1.2 ou superior permite a execução de consulta paralela por defeito. Por exemplo, a consulta da secção anterior será dividida enquanto a coluna "TollBoothId" for definida como chave de partição de entrada. PartIÇÃO POR Partição A cláusula Id não é necessária.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular as unidades de streaming máximas de um trabalho
O número total de unidades de streaming que podem ser usadas por um trabalho de Stream Analytics depende do número de passos na consulta definida para o trabalho e do número de divisórias para cada passo.

### <a name="steps-in-a-query"></a>Passos em uma consulta
Uma consulta pode ter um ou muitos passos. Cada passo é um subquery definido pela palavra-chave **COM.** A consulta que está fora da palavra-chave **COM** (apenas uma consulta) também é contada como um passo, como a declaração **SELECT** na seguinte consulta:

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
A partição de um passo requer as seguintes condições:

* A fonte de entrada deve ser dividida. 
* A declaração **SELECT** da consulta deve ser lida a partir de uma fonte de entrada dividida.
* A consulta dentro do degrau deve ter a **palavra-chave partition by.**

Quando uma consulta é dividida, os eventos de entrada são processados e agregados em grupos separados de partição, e os eventos de saída são gerados para cada um dos grupos. Se quiser um agregado combinado, deve criar um segundo passo não dividido para agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular as unidades de streaming máximas para um trabalho
Todos os passos não divididos em conjunto podem escalar até seis unidades de streaming (UsS) para um trabalho de Stream Analytics. Além disso, pode adicionar 6 SUs para cada partição num passo dividido.
Pode ver **alguns exemplos** na tabela abaixo.

| Consulta                                               | Max SUs para o trabalho |
| --------------------------------------------------- | ------------------- |
| <ul><li>A consulta contém um passo.</li><li>O passo não é dividido.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é dividido por 16.</li><li>A consulta contém um passo.</li><li>O passo está dividido.</li></ul> | 96 (6 * 16 divisórias) |
| <ul><li>A consulta contém dois passos.</li><li>Nenhum dos degraus está dividido.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada é dividido por 3.</li><li>A consulta contém dois passos. O passo de entrada é dividido e o segundo passo não.</li><li>A declaração <strong>SELECT</strong> lê-se na entrada dividida.</li></ul> | 24 (18 para passos divididos + 6 para passos não divididos |

### <a name="examples-of-scaling"></a>Exemplos de escala

A seguinte consulta calcula o número de carros dentro de uma janela de três minutos passando por uma estação de portagens que tem três portagens. Esta consulta pode ser dimensionada até seis US.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Para utilizar mais SUs para a consulta, tanto o fluxo de dados de entrada como a consulta devem ser divididos. Uma vez que a partilha do fluxo de dados está definida para 3, a seguinte consulta modificada pode ser dimensionada até 18 US:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Quando uma consulta é dividida, os eventos de entrada são processados e agregados em grupos separados de partição. Os eventos de saída também são gerados para cada um dos grupos. A partilha pode causar alguns resultados inesperados quando o campo **GROUP BY** não é a chave de partição no fluxo de dados de entrada. Por exemplo, o campo **TollBoothId** na consulta anterior não é a chave de partição do **Input1**. O resultado é que os dados da #1 TollBooth podem ser espalhados em várias divisórias.

Cada uma das divisórias **Input1** será processada separadamente pelo Stream Analytics. Como resultado, serão criados vários registos da contagem de carros para a mesma portagem na mesma janela Tumbling. Se a chave de partição de entrada não puder ser alterada, este problema pode ser corrigido adicionando um passo não-divisória para valores agregados entre divisórias, como no exemplo seguinte:

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
> Se estiver a juntar dois fluxos, certifique-se de que os fluxos são divididos pela chave de partição da coluna que utiliza para criar as juntas. Certifique-se também de que tem o mesmo número de divisórias em ambos os fluxos.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Conseguir maiores resultados em escala

Um trabalho [embaraçosamente paralelo](#embarrassingly-parallel-jobs) é necessário, mas não suficiente para sustentar uma maior entrada em escala. Cada sistema de armazenamento e a sua saída correspondente do Stream Analytics têm variações sobre como obter a melhor produção de escrita possível. Como em qualquer cenário em escala, existem alguns desafios que podem ser resolvidos usando as configurações certas. Esta secção discute configurações para algumas saídas comuns e fornece amostras para manter taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

As seguintes observações usam um trabalho de Stream Analytics com consulta apátrida (passthrough), uma UDF javascript básica que escreve para Event Hub, Azure SQL DB ou Cosmos DB.

#### <a name="event-hub"></a>Hub de Eventos

|Taxa de ingestão (eventos por segundo) | Unidades de Streaming | Recursos de Saída  |
|--------|---------|---------|
| Mil     |    1    |  2 TU   |
| 5 m.     |    6    |  6 TU   |
| 10 K    |    12   |  10 TU  |

A solução [Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) escala linearmente em termos de unidades de streaming (SU) e de produção, tornando-a a forma mais eficiente e performativa de analisar e transmitir dados a partir do Stream Analytics. Os postos de trabalho podem ser dimensionados até 192 SU, o que se traduz aproximadamente no processamento de até 200 MB/s, ou 19 triliões de eventos por dia.

#### <a name="azure-sql"></a>SQL do Azure
|Taxa de ingestão (eventos por segundo) | Unidades de Streaming | Recursos de Saída  |
|---------|------|-------|
|    Mil   |   3  |  S3   |
|    5 m.   |   18 |  P4   |
|    10 K  |   36 |  P6   |

[O Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) suporta a escrita em paralelo, chamado Partição Herdada, mas não está ativado por padrão. No entanto, permitir a partilha herdada, juntamente com uma consulta totalmente paralela, pode não ser suficiente para obter maiores resultados. Os sql write as rubricas dependem significativamente da configuração da base de dados SQL Azure e do esquema de mesa. O artigo [sQL Output Performance](./stream-analytics-sql-output-perf.md) tem mais detalhes sobre os parâmetros que podem maximizar a sua produção de escrita. Como se nota na saída do Azure Stream Analytics para o artigo da Base de [Dados Azure SQL,](./stream-analytics-sql-output-perf.md#azure-stream-analytics) esta solução não escala linearmente como um gasoduto totalmente paralelo para além de 8 divisórias e pode precisar de ser repartitentada antes da saída do SQL (ver [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). As SKUs premium são necessárias para manter altas taxas de IO, juntamente com as despesas gerais de backups de registo acontecendo a cada poucos minutos.

#### <a name="cosmos-db"></a>BD do Cosmos
|Taxa de ingestão (eventos por segundo) | Unidades de Streaming | Recursos de Saída  |
|-------|-------|---------|
|  Mil   |  3    | 20K RU  |
|  5 m.   |  24   | 60K RU  |
|  10 K  |  48   | 120K RU |

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


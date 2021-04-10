---
title: Execução do indexante de horário
titleSuffix: Azure Cognitive Search
description: Agende os indexadores de Pesquisa Cognitiva Azure para indexar o conteúdo periodicamente ou em momentos específicos.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097981"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Como agendar indexadores na Pesquisa Cognitiva Azure

Um indexante normalmente funciona uma vez, imediatamente após a sua criação. Em seguida, pode executá-lo novamente a pedido usando o portal Azure, [Run Indexer (REST)](/rest/api/searchservice/run-indexer)ou um Azure SDK. Em alternativa, também pode configurar um indexer para executar num horário. Algumas situações em que o agendamento do indexante é útil incluem:

* Os dados de origem mudarão ao longo do tempo, e pretende que o indexante de pesquisa processe automaticamente a diferença.

* Os dados de origem são muito grandes e pretende-se difundir o processamento do indexante ao longo do tempo. Os trabalhos indexantes estão sujeitos a um tempo máximo de funcionamento de 24 horas para fontes regulares de dados e 2 horas para indexantes com skillsets. Se a indexação não puder ser completada dentro do intervalo máximo, pode configurar um horário que funciona a cada 2 horas. Os indexantes podem automaticamente retomar onde pararam, como evidenciado por uma marca interna de alta água que marca onde a indexação terminou pela última vez. Executar um indexer num horário recorrente de 2 horas permite-lhe processar um conjunto de dados muito grande (muitos milhões de documentos) para além do intervalo permitido para um único trabalho. Para obter mais informações sobre a indexação de grandes volumes de dados, consulte [Como indexar grandes conjuntos de dados na Pesquisa Cognitiva Azure](search-howto-large-index.md).

* Um índice de pesquisa será povoado de várias fontes de dados, e você quer que os indexantes sejam executados em diferentes momentos para reduzir conflitos.

Visualmente, um horário pode parecer o seguinte: a partir de 1 de janeiro e a correr a cada 50 minutos.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> O agendador é uma característica incorporada da Azure Cognitive Search. Não há apoio para agendadores externos.

## <a name="schedule-property"></a>Agendar propriedade

Um horário faz parte da definição do indexante. Se a propriedade do **horário** for omitida, o indexante só será executado uma vez imediatamente após a sua criação. Se adicionar uma propriedade **de agenda,** especificará duas partes.

| Propriedade | Descrição |
|----------|-------------|
|**Intervalo** | (obrigatório) O tempo entre o início de duas execuções indexantes consecutivas. O intervalo mais pequeno permitido é de 5 minutos, e o mais longo é de 1440 minutos (24 horas). Deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) O padrão para isto é: `P(nD)(T(nH)(nM))` . <br/><br/>Exemplos: `PT15M` por cada 15 minutos, `PT2H` por cada 2 horas.|
| **Hora de Início (UTC)** | (opcional) Indica quando devem começar as execuções programadas. Se omitido, o tempo UTC atual é utilizado. Desta vez pode ser no passado, caso em que a primeira execução está programada como se o indexante estivesse a funcionar continuamente desde o início original **.**<br/><br/>Exemplos: `2021-01-01T00:00:00Z` a partir da meia-noite de 1 de janeiro, a partir das `2021-01-05T22:28:00Z` 22h28.m. no dia 5 de janeiro.|

## <a name="scheduling-behavior"></a>Comportamento de agendamento

Apenas uma execução de um indexante pode ser executada de cada vez. Se um indexante já estiver em execução quando a sua próxima execução estiver programada, essa execução será adiada para a próxima hora programada.

Vamos considerar um exemplo para tornar isto mais concreto. Suponha que configuramos um calendário indexante com um **intervalo** de hora e uma hora de **início** de 1 de junho de 2019 às 8:00:00 AM UTC. Eis o que pode acontecer quando uma corrida de indexante leva mais de uma hora:

* A primeira execução indexante começa em ou por volta de 1 de junho de 2019 às 8:00 AM UTC. Assuma que esta execução demora 20 minutos (ou em qualquer hora menos de 1 hora).
* A segunda execução começa em 1 de junho de 2019 9:00 UTC. Suponha que esta execução leva 70 minutos - mais de uma hora - e só estará concluída às 10:10 UTC.
* A terceira execução tem início marcado para as 10:00 UTC, mas nessa altura a execução anterior ainda está em execução. Esta execução programada é então ignorada. A próxima execução do indexante só começará às 11:00 UTC.

> [!NOTE]
> Se um indexante for definido para um determinado horário, mas falhar repetidamente no mesmo documento de cada vez, o indexante começará a funcionar num intervalo menos frequente (até ao intervalo máximo de pelo menos uma vez a cada 24 horas) até que progrida com sucesso novamente. Se acredita que corrigiu qualquer que fosse o problema subjacente, pode executar o indexante manualmente, e se a indexação for bem sucedida, o indexante voltará ao seu horário regular.

## <a name="schedule-using-rest"></a>Agendar usando REST

Especifique a propriedade do **horário** ao criar ou atualizar o indexante.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Horário usando .NET

O exemplo C# a seguir cria um indexante de base de dados Azure SQL, utilizando uma fonte de dados e índice predefinidos, e define a sua programação para ser executado uma vez por dia, a partir de agora:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

O calendário é definido utilizando a classe [IndexingSchedule,](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) quando cria ou atualiza um indexante utilizando o [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). O **construtor IndexingSchedule** requer um parâmetro **de intervalo** especificado com um objeto **TimeSpan.** Como já foi notado, o menor valor de intervalo permitido é de 5 minutos, e o maior é de 24 horas. O segundo parâmetro **StartTime,** especificado como um objeto **DateTimeOffset,** é opcional.

## <a name="next-steps"></a>Passos seguintes

Para os indexantes que executam um horário, pode monitorizar as operações recuperando o estado do serviço de pesquisa ou obter informações detalhadas, permitindo a verificação de registos de diagnóstico.

* [Monitorizar o estado do indexante de pesquisa](search-howto-monitor-indexers.md)
* [Recolher e analisar dados de registo](search-monitor-logs.md)
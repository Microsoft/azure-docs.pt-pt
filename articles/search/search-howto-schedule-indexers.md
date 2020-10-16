---
title: Execução do indexante de horário
titleSuffix: Azure Cognitive Search
description: Agende os indexadores de Pesquisa Cognitiva Azure para indexar o conteúdo periodicamente ou em momentos específicos.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: dffa8393dcfebf1cb73e3ab72890999cfa633b80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532572"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Como agendar indexadores na Pesquisa Cognitiva Azure

Um indexante normalmente funciona uma vez, imediatamente após a sua criação. Pode executá-lo novamente a pedido usando o portal, a API REST ou o .NET SDK. Também pode configurar um indexer para funcionar periodicamente num horário.

Algumas situações em que o agendamento do indexante é útil:

* Os dados de origem mudarão ao longo do tempo e pretende que os indexantes de Pesquisa Cognitiva do Azure processem automaticamente os dados alterados.
* O índice será preenchido a partir de múltiplas fontes de dados e pretende certificar-se de que os indexantes funcionam em diferentes momentos para reduzir conflitos.
* Os dados de origem são muito grandes e pretende-se difundir o processamento do indexante ao longo do tempo. Para obter mais informações sobre a indexação de grandes volumes de dados, consulte [Como indexar grandes conjuntos de dados na Pesquisa Cognitiva Azure.](search-howto-large-index.md)

O agendador é uma característica incorporada da Azure Cognitive Search. Não pode usar um programador externo para controlar os indexadores de pesquisa.

## <a name="define-schedule-properties"></a>Definir propriedades de agenda

Um calendário indexante tem duas propriedades:
* **Intervalo**, que define a quantidade de tempo entre execuções indexantes programadas. O menor intervalo permitido é de 5 minutos, e o maior é de 24 horas.
* **Hora de Início (UTC),** que indica a primeira vez em que o indexante deve ser executado.

Pode especificar um calendário quando criar o indexante pela primeira vez, ou atualizando as propriedades do indexante mais tarde. Os horários dos indexantes podem ser definidos através do [portal,](#portal)da [API REST,](#restApi)ou do [.NET SDK](#dotNetSdk).

Apenas uma execução de um indexante pode ser executada de cada vez. Se um indexante já estiver em execução quando a sua próxima execução estiver programada, essa execução será adiada para a próxima hora programada.

Vamos considerar um exemplo para tornar isto mais concreto. Suponha que configuramos um calendário indexante com um **intervalo** de hora e uma hora de **início** de 1 de junho de 2019 às 8:00:00 AM UTC. Eis o que pode acontecer quando uma corrida de indexante leva mais de uma hora:

* A primeira execução indexante começa em ou por volta de 1 de junho de 2019 às 8:00 AM UTC. Assuma que esta execução demora 20 minutos (ou em qualquer hora menos de 1 hora).
* A segunda execução começa em 1 de junho de 2019 9:00 UTC. Suponha que esta execução leva 70 minutos - mais de uma hora - e só estará concluída às 10:10 UTC.
* A terceira execução tem início marcado para as 10:00 UTC, mas nessa altura a execução anterior ainda está em execução. Esta execução programada é então ignorada. A próxima execução do indexante só começará às 11:00 UTC.

> [!NOTE]
> Se um indexante for definido para um determinado horário, mas falhar repetidamente no mesmo documento repetidamente cada vez mais de cada vez que for executado, o indexante começará a funcionar num intervalo menos frequente (até ao máximo de pelo menos uma vez a cada 24 horas) até que progrida com sucesso novamente.  Se acredita que corrigiu qualquer que fosse o problema que estava a causar a fixação do indexante num determinado ponto, pode realizar uma execução a pedido do indexante, e se isso progredir com sucesso, o indexante voltará a ter o seu intervalo de horário definido.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Agendar no portal

O assistente de Dados de Importação no portal permite definir o calendário para um indexante no momento da criação. A definição de horário predefinido é **hora,** o que significa que o indexante funciona uma vez depois de ser criado, e volta a funcionar a cada hora depois.

Pode alterar a definição de Agenda para **Uma vez** que não quer que o indexante volte a funcionar automaticamente, ou ao **Daily** para executar uma vez por dia. Desa estade para **Personalizado** se pretender especificar um intervalo diferente ou uma hora de início futura específica.

Quando define o horário para **Personalizado,** os campos parecem permitir especificar o **Intervalo** e a **Hora de Início (UTC)**. O intervalo de tempo mais curto permitido é de 5 minutos, e o mais longo é de 1440 minutos (24 horas).

   ![Definição do calendário do indexante no assistente de dados de importação](media/search-howto-schedule-indexers/schedule-import-data.png "Definição do calendário do indexante no assistente de dados de importação")

Depois de ter sido criado um indexante, pode alterar as definições de agenda utilizando o painel de Edição do indexante. Os campos Agendar são os mesmos que no assistente de dados de importação.

   ![Definir o calendário no painel de edição indexante](media/search-howto-schedule-indexers/schedule-edit.png "Definir o calendário no painel de edição indexante")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Agendar usando APIs REST

Pode definir o calendário para um indexante utilizando a API REST. Para isso, inclua a propriedade de **agenda ao** criar ou atualizar o indexante. O exemplo abaixo mostra um pedido DEP para atualizar um indexante existente:

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

O parâmetro **de intervalo** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções indexantes consecutivas. O menor intervalo permitido é de 5 minutos; o mais longo é um dia. Deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) O padrão para isto é: `P(nD)(T(nH)(nM))` . Exemplos: `PT15M` por cada 15 minutos, `PT2H` por cada 2 horas.

O **início** opcional Hora de início indica quando devem começar as execuções programadas. Se for omitido, utiliza-se o tempo utc atual. Desta vez pode ser no passado, caso em que a primeira execução está programada como se o indexante estivesse a funcionar continuamente desde o início original **.**

Também pode executar um indexante a pedido a qualquer momento usando a chamada 'Índice de Execução'. Para obter mais informações sobre indexantes em execução e definir horários de indexante, consulte [Run Indexer,](/rest/api/searchservice/run-indexer) [Get Indexer](/rest/api/searchservice/get-indexer)e [Update Indexer](/rest/api/searchservice/update-indexer) na Referência DE API REST.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Agendar utilizando o .NET SDK

Pode definir o calendário de um indexante utilizando o Azure Cognitive Search .NET SDK. Para isso, inclua a propriedade de **agenda ao** criar ou atualizar um Indexer.

O exemplo C# a seguir cria um indexante, utilizando uma fonte de dados predefinida e índice, e define o seu horário para ser executado uma vez por dia a partir de 30 minutos a partir de agora:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Se o parâmetro do **calendário** for omitido, o indexante só funcionará uma vez imediatamente após a sua criação.

O parâmetro **startTime** pode ser definido para uma hora no passado. Nesse caso, a primeira execução está programada como se o indexante estivesse a funcionar continuamente desde o início de **tempo.**

O horário é definido usando a classe [IndexingSchedule.](/dotnet/api/microsoft.azure.search.models.indexingschedule) O **construtor IndexingSchedule** requer um parâmetro **de intervalo** especificado com um objeto **TimeSpan.** O menor valor de intervalo permitido é de 5 minutos, e o maior é de 24 horas. O segundo parâmetro **startTime,** especificado como um objeto **DateTimeOffset,** é opcional.

O .NET SDK permite controlar as operações indexantes utilizando a classe [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) e a sua propriedade [Indexers,](/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) que implementa métodos a partir da interface **IIndexersOperations.** 

Pode executar um indexante a pedido a qualquer momento utilizando um dos métodos [Run,](/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run) [RunAsync](/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)ou [RunWithHttpMessagesAsync.](/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)

Para obter mais informações sobre a criação, atualização e execução de [indexantes, consulte IIindexersOperations](/dotnet/api/microsoft.azure.search.iindexersoperations).
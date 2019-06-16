---
title: Como agendar indexadores - Azure Search
description: Agende indexadores do Azure Search para indexar conteúdo periodicamente ou em horários específicos.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755384"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Como agendar indexadores para o Azure Search
Um indexador normalmente é executado uma vez, imediatamente depois de criado. Pode executá-lo novamente a pedido com o portal, a API REST ou o SDK do .NET. Também pode configurar um indexador para executar periodicamente com base numa agenda.

Algumas situações em que o agendamento do indexador é útil:

* Origem de dados será alterado ao longo do tempo e pretender indexadores do Azure Search para processar automaticamente os dados alterados.
* O índice será preenchido de várias origens de dados e pretender certificar-se de que os indexadores executam em alturas diferentes para reduzir conflitos.
* A origem de dados é muito grande e que pretende distribuir o indexador de processamento ao longo do tempo. Para obter mais informações sobre a indexação de grandes volumes de dados, consulte [como indexar grandes conjuntos de dados no Azure Search](search-howto-large-index.md).

O scheduler é um recurso interno do Azure Search. Não é possível utilizar um agendador externo para controlar os indexadores de pesquisa.

## <a name="define-schedule-properties"></a>Definir propriedades da agenda

Um cronograma de indexador tem duas propriedades:
* **Intervalo**, que define a quantidade de tempo entre agendada execuções de indexador. O intervalo menor permitido é de 5 minutos e o maior é de 24 horas.
* **Hora de início (UTC)** , que indica a primeira vez em que deve ser executado o indexador.

Pode especificar uma agenda ao criar primeiro o indexador ou ao atualizar as propriedades do indexador mais tarde. Agendas de indexador podem ser definidas utilizando o [portal](#portal), o [REST API](#restApi), ou o [SDK de .NET](#dotNetSdk).

Pode executar apenas uma execução de um indexador de cada vez. Se um indexador já está em execução quando a sua próxima execução está agendada, dessa execução será adiada até que a próxima hora de agendada.

Vamos considerar um exemplo para tornar isso mais concreto. Suponha que podemos configurar um agendamento do indexador com uma **intervalo** de hora a hora e um **Start Time** de 1 de Junho de 2019 00: 8:00, UTC. Eis o que poderia acontecer quando a execução de um indexador demora mais de uma hora:

* A primeira execução do indexador começa em ou em qualquer parte 1 de Junho de 2019 8:00 UTC. Assumir que esta execução demora 20 minutos (ou em qualquer altura inferior a 1 hora).
* A segunda execução começa em ou em qualquer parte 1 de Junho de 2019 9:00 UTC. Suponhamos que esta execução demora 70 minutos - mais de uma hora – e não será concluída até 10:10 AM UTC.
* A execução de terceiro está agendada para começar às 10:00 UTC, mas nessa altura execução anterior ainda está em execução. Isso agendada execução, em seguida, é ignorada. A próxima execução do indexador não será iniciada até 11:00 UTC.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Defina uma agenda no portal

O Assistente para importar dados no portal do permite que defina o agendamento de um indexador no momento da criação. A definição de agenda predefinida é **por hora**, que significa que o indexador é executado uma vez depois de criado e é executado novamente a cada hora, posteriormente.

Pode alterar a definição de agenda para **uma vez** se não pretender que o indexador para ser executada novamente automaticamente, ou a **diária** para executar uma vez por dia. Defina-o como **personalizado** se pretender especificar um intervalo diferente ou uma específica futura Start Time.

Ao definir a agenda **personalizada**, campos são apresentados permitir que especifique a **intervalo** e o **Iniciar hora (UTC)** . O tempo mais curto intervalo permitido é 5 minutos e há mais tempo é 1440 minutos (24 horas).

   ![Agendamento do indexador de definição no Assistente para importar dados](media/search-howto-schedule-indexers/schedule-import-data.png "agendamento do indexador de definição no Assistente para importar dados")

Depois de um indexador tiver sido criado, pode alterar as definições do agendamento utilizando o painel de editar o indexador. Os campos de agenda são os mesmos que no Assistente de importação de dados.

   ![Definir a agenda no painel de edição do indexador](media/search-howto-schedule-indexers/schedule-edit.png "definir a agenda no painel de edição do indexador")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Defina uma agenda através da API REST

Pode definir a agenda para um indexador através da API REST. Para tal, inclua o **agenda** propriedade quando criar ou atualizar o indexador. O exemplo abaixo mostra um pedido PUT para atualizar um indexador existente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O **intervalo** é necessário o parâmetro. O intervalo de refere-se para o tempo entre o início de duas execuções de indexador consecutivos. O intervalo menor permitido é de 5 minutos; há mais tempo é um dia. Tem de ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um [duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) valor). O padrão para isto é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

O opcional **startTime** indica quando devem começar execuções agendadas. Se for omitido, é utilizada a hora UTC atual. Desta vez pode ser no passado, nesse caso a primeira execução está agendado como se estiver a ser executado o indexador continuamente desde o original **startTime**.

Também pode executar um indexador a pedido em qualquer altura através da chamada de executar o indexador. Para obter mais informações sobre a execução de indexadores e definir agendas de indexador, consulte [executar indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [obter indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer), e [atualização indexador](https://docs.microsoft.com/rest/api/searchservice/update-indexer) na referência de API do REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Defina uma agenda com o SDK .NET

Pode definir a agenda para um indexador através do SDK de .NET de pesquisa do Azure. Para tal, inclua o **agenda** propriedade quando criar ou atualizar um indexador.

O seguinte C# exemplo cria um indexador, usando uma origem de dados predefinidos e um índice e define a agenda para ser executada uma vez por dia a partir de 30 minutos a partir de agora:

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
Se o **agenda** parâmetro for omitido, o indexador será executado apenas uma vez imediatamente depois de criado.

O **startTime** parâmetro pode ser definido numa hora no passado. Nesse caso, a primeira execução está agendada como se estiver a ser executado o indexador continuamente desde a determinado **startTime**.

A agenda é definida usando o [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) classe. O **IndexingSchedule** construtor exige uma **intervalo** parâmetro especificado com um **TimeSpan** objeto. O valor mais baixo do intervalo permitido é de 5 minutos e o maior é de 24 horas. A segunda **startTime** parâmetro, especificado como um **DateTimeOffset** de objeto, é opcional.

O SDK de .NET permite operações do indexador de controle com o [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) classe e a respetiva [indexadores](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) propriedade, que implementa métodos do **IIndexersOperations**interface. 

Pode executar um indexador a pedido em qualquer altura utilizando um da [execute](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), ou [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) métodos.

Para obter mais informações sobre como criar, atualizar e executar os indexadores, consulte [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).

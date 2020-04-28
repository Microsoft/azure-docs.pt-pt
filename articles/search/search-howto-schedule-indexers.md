---
title: Execução do indexador de agenda
titleSuffix: Azure Cognitive Search
description: Agende os indexantes de Pesquisa Cognitiva Azure para indexar o conteúdo periodicamente ou em horários específicos.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112943"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Como agendar indexadores em Pesquisa Cognitiva Azure

Um indexante normalmente funciona uma vez, imediatamente após a sua criação. Pode executá-lo novamente a pedido através do portal, da API REST ou do .NET SDK. Também pode configurar um indexante para funcionar periodicamente num horário.

Algumas situações em que o agendamento do indexante é útil:

* Os dados de origem mudarão ao longo do tempo, e você quer que os indexadores de Pesquisa Cognitiva Azure processem automaticamente os dados alterados.
* O índice será povoado a partir de várias fontes de dados e você quer garantir que os indexers funcionam em diferentes momentos para reduzir conflitos.
* Os dados de origem são muito grandes e você quer espalhar o processamento do indexante ao longo do tempo. Para obter mais informações sobre a indexação de grandes volumes de dados, consulte [como indexar grandes conjuntos](search-howto-large-index.md)de dados na Pesquisa Cognitiva azure .

O programador é uma característica incorporada da Pesquisa Cognitiva Azure. Não pode usar um programador externo para controlar os indexadores de pesquisa.

## <a name="define-schedule-properties"></a>Definir propriedades de horário

Um calendário indexante tem duas propriedades:
* **Intervalo**, que define a quantidade de tempo entre execuções indexadas programadas. O menor intervalo permitido é de 5 minutos, e o maior é de 24 horas.
* **Início do tempo (UTC)**, que indica a primeira vez em que o indexante deve ser executado.

Pode especificar um horário quando criar o indexador, ou atualizando as propriedades do indexante mais tarde. Os horários do indexante podem ser definidos através do [portal,](#portal)do [REST API,](#restApi)ou do [.NET SDK](#dotNetSdk).

Só uma execução de um indexante pode ser executada de cada vez. Se um indexante já estiver em execução quando a sua próxima execução estiver agendada, essa execução é adiada para a próxima hora programada.

Vamos considerar um exemplo para tornar isto mais concreto. Suponha que configuramos um calendário de indexantes com um **intervalo** de hora e um horário de **início** de 1 de junho de 2019 às 8:00:00 UTC. Eis o que pode acontecer quando uma corrida de indexantes demorar mais de uma hora:

* A primeira execução do indexante começa em 1 de junho de 2019 às 8:00 UTC. Assuma que esta execução leva 20 minutos (ou qualquer momento inferior a 1 hora).
* A segunda execução começa por volta de 1 de junho de 2019 9:00 AM UTC. Suponha que esta execução demore 70 minutos - mais de uma hora - e só estará concluída às 10:10 UTC.
* A terceira execução está marcada para começar às 10:00 UTC, mas nessa altura a execução anterior ainda está em execução. Esta execução programada é então ignorada. A próxima execução do indexante só começará às 11:00 UTC.

> [!NOTE]
> Se um indexante estiver definido para um determinado horário, mas falha repetidamente no mesmo documento cada vez mais, cada vez que funciona, o indexante começará a funcionar num intervalo menos frequente (até ao máximo de pelo menos uma vez a cada 24 horas) até que volte a progredir com sucesso.  Se acredita ter corrigido qualquer que seja o problema que estava a causar o facto de o indexante estar preso a um determinado ponto, pode realizar uma execução a pedido do indexante, e se isso fizer progressos com sucesso, o indexante voltará ao intervalo de horário definido novamente.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Agenda no portal

O assistente de dados de importação no portal permite definir o calendário para um indexante no momento da criação. A definição de horário padrão é hora, o que significa que o indexante funciona uma vez após a sua criação, e corre **novamente**a cada hora depois.

Pode alterar a definição de Horário para **Once** se não quiser que o indexante volte a funcionar automaticamente, ou ao **Daily** para funcionar uma vez por dia. Desloque-o para **Custom** se quiser especificar um intervalo diferente ou um futuro horário específico de início.

Quando define o horário para **Personalizado,** os campos parecem permitir especificar o **Intervalo** e o Tempo de **Início (UTC)**. O intervalo de tempo mais curto permitido é de 5 minutos, e o mais longo é de 1440 minutos (24 horas).

   ![Definição da programação do indexante no assistente de dados de importação](media/search-howto-schedule-indexers/schedule-import-data.png "Definição da programação do indexante no assistente de dados de importação")

Depois de criado um indexante, pode alterar as definições de horário utilizando o painel de Edição do indexante. Os campos de Agenda são os mesmos que no assistente de dados de importação.

   ![Definição da programação no painel de edição do indexador](media/search-howto-schedule-indexers/schedule-edit.png "Definição da programação no painel de edição do indexador")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Agendar usando APIs REST

Pode definir o horário para um indexante utilizando a API REST. Para isso, inclua a propriedade de **horário** ao criar ou atualizar o indexante. O exemplo abaixo mostra um pedido de PUT para atualizar um indexante existente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

É necessário o parâmetro de **intervalo.** O intervalo refere-se ao tempo entre o início de duas execuções consecutivas do indexante. O menor intervalo permitido é de 5 minutos; o mais longo é um dia. Deve ser formatado como um valor XSD "daytimeduration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) O padrão para `P(nD)(T(nH)(nM))`isto é: . Exemplos: `PT15M` por cada 15 minutos, `PT2H` por cada 2 horas.

O **início** opcional indica quando as execuções programadas devem começar. Se for omitida, o tempo atual utc é utilizado. Este tempo pode ser no passado, caso em que a primeira execução está agendada como se o indexante estivesse a funcionar continuamente desde o **início**original.

Também pode executar um indexante a pedido a qualquer momento usando a chamada do Índice de Execução. Para obter mais informações sobre os indexantes de execução e definindo os horários dos indexantes, consulte o [Indexer de Execução,](https://docs.microsoft.com/rest/api/searchservice/run-indexer)o [Indexante de Execução](https://docs.microsoft.com/rest/api/searchservice/get-indexer)e o Indexante de [Atualização](https://docs.microsoft.com/rest/api/searchservice/update-indexer) na referência rest API.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Agendar usando o .NET SDK

Pode definir o calendário para um indexante utilizando o Azure Cognitive Search .NET SDK. Para isso, inclua a propriedade de **horário** ao criar ou atualizar um Indexer.

O exemplo C# seguinte cria um indexante, utilizando uma fonte de dados e índice predefinidos, e define o seu horário para funcionar uma vez por dia a partir de 30 minutos a partir de agora:

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
Se o parâmetro de **programação** for omitido, o indexante só funcionará uma vez imediatamente após a sua criação.

O parâmetro **startTime** pode ser definido para um tempo no passado. Nesse caso, a primeira execução está programada como se o indexante estivesse a funcionar continuamente desde o tempo de **início**dado.

O calendário é definido utilizando a classe [IndexingSchedule.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) O construtor **IndexingSchedule** requer um parâmetro de **intervalo** especificado utilizando um objeto **TimeSpan.** O menor valor de intervalo permitido é de 5 minutos, e o maior é de 24 horas. O segundo parâmetro **startTime,** especificado como um objeto **DateTimeOffset,** é opcional.

O .NET SDK permite controlar as operações de indexantes utilizando a classe [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) e a sua propriedade [Indexers,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) que implementa métodos a partir da interface **IIndexersOperations.** 

Pode executar um indexador a qualquer momento utilizando um dos métodos [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)ou [RunWithHttpMessagesAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)

Para obter mais informações sobre a criação, atualização e a execução de indexadores, consulte [iIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).

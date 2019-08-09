---
title: Como agendar indexadores-Azure Search
description: Agende Azure Search indexadores para indexar conteúdo periodicamente ou em horários específicos.
ms.date: 05/31/2019
author: HeidiSteen
manager: HeidiSteen
ms.author: heidist
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.openlocfilehash: 245a2139aae0910ea1415811234667f2c06500ec
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855800"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Como agendar indexadores para Azure Search
Um indexador normalmente é executado uma vez, imediatamente após sua criação. Você pode executá-lo novamente sob demanda usando o portal, a API REST ou o SDK do .NET. Você também pode configurar um indexador para ser executado periodicamente em um agendamento.

Algumas situações em que o agendamento do indexador é útil:

* Os dados de origem serão alterados ao longo do tempo e você deseja que os indexadores de Azure Search processem automaticamente os dados alterados.
* O índice será preenchido de várias fontes de dados e você deseja garantir que os indexadores sejam executados em momentos diferentes para reduzir os conflitos.
* Os dados de origem são muito grandes e você deseja espalhar o processamento do indexador ao longo do tempo. Para obter mais informações sobre como indexar grandes volumes de dados, consulte [como indexar grandes conjuntos de dados no Azure Search](search-howto-large-index.md).

O Agendador é um recurso interno do Azure Search. Você não pode usar um Agendador externo para controlar indexadores de pesquisa.

## <a name="define-schedule-properties"></a>Definir propriedades da agenda

Uma agenda do indexador tem duas propriedades:
* **Intervalo**, que define a quantidade de tempo entre as execuções do indexador agendado. O menor intervalo permitido é de 5 minutos e o maior é de 24 horas.
* **Hora de início (UTC)** , que indica a primeira vez em que o indexador deve ser executado.

Você pode especificar um agendamento ao criar o indexador pela primeira vez ou atualizando as propriedades do indexador posteriormente. Os agendamentos do indexador podem ser definidos usando o [portal](#portal), a [API REST](#restApi)ou o [SDK do .net](#dotNetSdk).

Somente uma execução de um indexador pode ser executada por vez. Se um indexador já estiver em execução quando a próxima execução for agendada, essa execução será adiada até a próxima hora agendada.

Vamos considerar um exemplo para tornar isso mais concreto. Suponha que configuramos uma agenda do indexador com um **intervalo** de hora em horário e uma **hora de início** de 1º de junho de 2019 às 8:00:00 am UTC. Veja o que poderia acontecer quando uma execução de indexador demorar mais do que uma hora:

* A primeira execução do indexador começa em 1º de junho de 2019 às 8:00 AM UTC. Suponha que essa execução demore 20 minutos (ou a qualquer momento menos de 1 hora).
* A segunda execução começa em 1º de junho de 2019 9:00 AM UTC. Suponha que essa execução leva 70 minutos – mais de uma hora – e não será concluída até 10:10 AM UTC.
* A terceira execução está agendada para iniciar às 10:00 AM UTC, mas, nesse momento, a execução anterior ainda está em execução. Essa execução agendada é ignorada. A próxima execução do indexador não será iniciada até 11:00 AM UTC.

> [!NOTE]
> Se um indexador for definido como um determinado agendamento, mas falhar repetidamente no mesmo documento repetidamente cada vez que for executado, o indexador começará a ser executado em um intervalo menos frequente (até o máximo de pelo menos uma vez a cada 24 horas) até que ele faça com que o progresso seja agado com êxito no.  Se acreditar que você corrigiu qualquer problema que estava fazendo com que o indexador fosse paralisado em um determinado ponto, você poderá executar uma execução sob demanda do indexador e, se isso fizer o progresso com êxito, o indexador retornará ao seu intervalo de agendamento definido novamente.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definir uma agenda no portal

O assistente de importação de dados no portal permite que você defina a agenda para um indexador no momento da criação. A configuração de agendamento padrão é por **hora**, o que significa que o indexador é executado uma vez após sua criação e é executado novamente a cada hora depois.

Você pode alterar a configuração de agenda para **uma vez** se não quiser que o indexador seja executado novamente automaticamente ou **diariamente** para ser executado uma vez por dia. Defina-o como **personalizado** se você quiser especificar um intervalo diferente ou uma hora de início futura específica.

Quando você define o agendamento como **personalizado**, os campos são exibidos para permitir que você especifique o **intervalo** e a **hora de início (UTC)** . O intervalo de tempo mais curto permitido é de 5 minutos e o mais longo é de 1440 minutos (24 horas).

   ![Configuração do agendamento do indexador no assistente de importação de dados](media/search-howto-schedule-indexers/schedule-import-data.png "Configuração do agendamento do indexador no assistente de importação de dados")

Depois que um indexador tiver sido criado, você poderá alterar as configurações de agendamento usando o painel de edição do indexador. Os campos de agendamento são os mesmos do assistente de importação de dados.

   ![Definindo a agenda no painel de edição] do indexador (media/search-howto-schedule-indexers/schedule-edit.png "Definindo a agenda no painel de edição") do indexador

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definir uma agenda usando a API REST

Você pode definir o agendamento de um indexador usando a API REST. Para fazer isso, inclua a propriedade **Schedule** ao criar ou atualizar o indexador. O exemplo a seguir mostra uma solicitação PUT para atualizar um indexador existente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O parâmetro **Interval** é obrigatório. O intervalo refere-se ao tempo entre o início de duas execuções de indexador consecutivas. O menor intervalo permitido é de 5 minutos; o mais longo é um dia. Ele deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

O **StartTime** opcional indica quando as execuções agendadas devem começar. Se for omitido, a hora UTC atual será usada. Esse tempo pode estar no passado, caso em que a primeira execução é agendada como se o indexador fosse executado continuamente desde o iníciotimeoriginal.

Você também pode executar um indexador sob demanda a qualquer momento usando a chamada executar indexador. Para obter mais informações sobre a execução de indexadores e a configuração de agendas do indexador, consulte [executar indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [obter indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer)e [Atualizar INDEXADOR](https://docs.microsoft.com/rest/api/searchservice/update-indexer) na referência da API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definir uma agenda usando o SDK do .NET

Você pode definir a agenda para um indexador usando o SDK do .NET Azure Search. Para fazer isso, inclua a propriedade **Schedule** ao criar ou atualizar um indexador.

O exemplo C# a seguir cria um indexador, usando uma fonte de dados predefinida e um índice, e define sua agenda para ser executada uma vez a cada dia, começando em 30 minutos a partir de agora:

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
Se o parâmetro de **agendamento** for omitido, o indexador será executado apenas uma vez logo após sua criação.

O parâmetro StartTime pode ser definido como uma hora no passado. Nesse caso, a primeira execução é agendada como se o indexador fosse executado continuamente desde o início de **StartTime**.

O agendamento é definido usando a classe [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) . O construtor **IndexingSchedule** requer um parâmetro de **intervalo** especificado usando um objeto **TimeSpan** . O menor valor de intervalo permitido é de 5 minutos e o maior é de 24 horas. O segundo parâmetro StartTime, especificado como um objeto **DateTimeOffset** , é opcional.

O SDK do .NET permite controlar as operações do indexador usando a classe [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) e sua propriedade [Indexers](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) , que implementa métodos da interface **IIndexersOperations** . 

Você pode executar um indexador sob demanda a qualquer momento usando um dos métodos [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)ou [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) .

Para obter mais informações sobre como criar, atualizar e executar indexadores, consulte [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).

---
title: Monitorizar o estado do indexador e os resultados
titleSuffix: Azure Cognitive Search
description: Monitorize o estado, o progresso e os resultados dos indexadores de Pesquisa Cognitiva Azure no portal Azure, utilizando a API REST, ou o .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112990"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Como monitorizar o estado do indexador de pesquisa cognitiva Azure e os resultados

A Pesquisa Cognitiva Azure fornece estado e monitorização de informações sobre as corridas atuais e históricas de cada indexante.

A monitorização do indexante é útil quando deseja:

* Acompanhe o progresso de um indexante durante uma corrida em curso.
* Reveja os resultados da execução do indexador em curso ou anterior.
* Identifique erros indexadores de alto nível e erros ou avisos sobre a indexação de documentos individuais.

## <a name="get-status-and-history"></a>Obter estatuto e história

Pode aceder a informações de monitorização de indexantes de várias formas, incluindo:

* No [portal do Azure](#portal)
* Usando a [API REST](#restapi)
* Usando o [.NET SDK](#dotnetsdk)

As informações de monitorização do indexante disponíveis incluem todos os seguintes (embora os formatos de dados diferam com base no método de acesso utilizado):

* Informação de estado sobre o próprio indexante
* Informações sobre a mais recente execução do indexante, incluindo o seu estado, tempos de início e fim, e erros e avisos detalhados.
* Uma lista de indexantes históricos corre, e os seus estatutos, resultados, erros e avisos.

Os indexadores que processam grandes volumes de dados podem demorar muito tempo a ser executados. Por exemplo, os indexadores que lidam com milhões de documentos de origem podem funcionar durante 24 horas e, em seguida, reiniciar quase imediatamente. O estatuto dos indexadores de alto volume pode sempre dizer **in Progress** no portal. Mesmo quando um indexante está em execução, existem detalhes sobre o progresso em curso e as execuções anteriores.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitor usando o portal

Pode ver o estado atual de todos os seus indexers na lista **de Indexers** na página de visão geral do seu serviço de pesquisa.

   ![Lista de indexadores](media/search-monitor-indexers/indexers-list.png "Lista de indexadores")

Quando um indexante está a executar, o estado na lista mostra **in Progress**, e o valor bem sucedido do **Docs** mostra o número de documentos processados até agora. Pode levar alguns minutos para o portal atualizar os valores de estado do indexador e contagens de documentos.

Um indexante cuja mais recente execução foi bem sucedida mostra **sucesso.** Uma execução de indexante pode ser bem sucedida mesmo que os documentos individuais tenham erros, se o número de erros for inferior à definição de **itens de itens falhados** do indexante Max.

Se a execução mais recente terminou com um erro, o estado mostra **falhado**. Um estado de **Reset** significa que o estado de rastreio de mudança do indexante foi reposto.

Clique num indexador na lista para ver mais detalhes sobre as correntes e recentes corridas do indexante.

   ![Resumo indexante e história de execução](media/search-monitor-indexers/indexer-summary.png "Resumo indexante e história de execução")

O gráfico **resumo do Indexer** apresenta um gráfico do número de documentos processados nas suas mais recentes execuções.

A lista de detalhes da **Execução** mostra 50 dos resultados mais recentes da execução.

Clique num resultado de execução na lista para ver detalhes sobre essa execução. Isto inclui os seus tempos de início e fim, e quaisquer erros e avisos que ocorreram.

   ![Detalhes de execução do indexante](media/search-monitor-indexers/indexer-execution.png "Detalhes de execução do indexante")

Se houver problemas específicos do documento durante a execução, serão listados nos campos De erros e avisos.

   ![Detalhes do indexante com erros](media/search-monitor-indexers/indexer-execution-error.png "Detalhes do indexante com erros")

Os avisos são comuns a alguns tipos de indexantes, e nem sempre indicam um problema. Por exemplo, os indexadores que utilizam serviços cognitivos podem relatar avisos quando os ficheiros de imagem ou PDF não contêm qualquer texto para processar.

Para obter mais informações sobre a investigação de erros e avisos de [indexantes, consulte problemas comuns de resolução de problemas na Pesquisa Cognitiva de Azure](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitor utilizando APIs REST

Pode recuperar o estado e o histórico de execução de um indexante utilizando o [comando Get Indexer Status:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o estatuto global do indexante, a última (ou em curso) invocação do indexante, e a história das recentes invocações indexadas.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

O histórico de execução contém até 50 runs mais recentes, que são classificados por ordem cronológica inversa (o primeiro mais recente).

Note que existem dois valores de estado diferentes. O estatuto de nível superior é para o próprio indexante. Um estado indexador de **funcionamento** significa que o indexante está configurado corretamente e disponível para funcionar, mas não que esteja atualmente em execução.

Cada execução do indexante também tem o seu próprio estatuto que indica se essa execução específica está em curso (**em execução)** ou já completada com um **sucesso**, **transientFailure**ou **persistenteEstado de Falha.** 

Quando um indexante é reposto para refrescar o seu estado de rastreio de mudança, uma entrada separada do histórico de execução é adicionada com um estado de **reset.**

Para obter mais detalhes sobre códigos de estado e dados de monitorização do indexante, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitor utilizando o .NET SDK

Pode definir o calendário para um indexante utilizando o Azure Cognitive Search .NET SDK. Para isso, inclua a propriedade de **horário** ao criar ou atualizar um Indexer.

O exemplo C# seguinte escreve informações sobre o estado de um indexante e os resultados da sua mais recente (ou em curso) corrida para a consola.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

A saída na consola será parecida com esta:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Note que existem dois valores de estado diferentes. O estatuto de nível superior é o estatuto do próprio indexante. Um estado indexador de **Execução** significa que o indexante está corretamente configurado e disponível para execução, mas não que esteja atualmente a executar.

Cada execução do indexante também tem o seu próprio estatuto para saber se essa execução específica está em curso (**Running),** ou se já foi concluída com um estado de **Sucesso** ou **TransientError.** 

Quando um indexante é reposto para refrescar o seu estado de rastreio de mudança, uma entrada de histórico separada é adicionada com um estado de **reset.**

Para obter mais detalhes sobre códigos de estado e informações de monitorização do indexante, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) na API REST.

Os detalhes sobre erros ou advertências específicos do `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings`documento podem ser recuperados enumerando as listas e .

Para obter mais informações sobre as classes .NET SDK utilizadas para monitorizar os indexadores, consulte [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).

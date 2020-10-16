---
title: Monitorizar o estado do indexante e os resultados
titleSuffix: Azure Cognitive Search
description: Monitorize o estado, o progresso e os resultados dos indexadores de Pesquisa Cognitiva Azure no portal Azure, utilizando a API REST ou o .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 06a31e61583ac28218b34195dd6e5b7f92776dce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541242"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Como monitorizar o estado e os resultados do indexante de pesquisa cognitiva do Azure

A Azure Cognitive Search fornece informações de estado e monitorização sobre as execuções atuais e históricas de cada indexante.

A monitorização do indexante é útil quando pretende:

* Acompanhe o progresso de um indexante durante uma corrida em curso.
* Reveja os resultados da execução do indexante em curso ou anterior.
* Identifique erros indexantes de nível superior e erros ou avisos sobre a indexação de documentos individuais.

## <a name="get-status-and-history"></a>Obtenha estatuto e história

Pode aceder à informação de monitorização do indexante de várias formas, incluindo:

* No [portal Azure](#portal)
* Utilização da [API REST](#restapi)
* Utilizando o [.NET SDK](#dotnetsdk)

As informações de monitorização do indexante disponível incluem todos os seguintes (embora os formatos de dados diferem com base no método de acesso utilizado):

* Informação sobre o próprio indexante
* Informações sobre a mais recente execução do indexante, incluindo o seu estado, tempos de início e fim, e erros e avisos detalhados.
* Uma lista de indexantes históricos corre, e seus estados, resultados, erros e avisos.

Os indexantes que processam grandes volumes de dados podem demorar muito tempo a ser executados. Por exemplo, os indexantes que lidam com milhões de documentos de origem podem funcionar durante 24 horas e, em seguida, reiniciar quase imediatamente. O estado dos indexantes de alto volume pode sempre dizer **Em Progresso** no portal. Mesmo quando um indexante está em execução, estão disponíveis detalhes sobre o progresso em curso e execuções anteriores.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitor usando o portal

Pode ver o estado atual de todos os seus indexantes na lista **de Indexadores** na página de Visão Geral do seu serviço de pesquisa.

   ![Lista de indexantes](media/search-monitor-indexers/indexers-list.png "Lista de indexantes")

Quando um indexante está a executar, o estado da lista mostra **Em Progresso**, e o valor **Docs Succeeded** mostra o número de documentos processados até agora. Pode levar alguns minutos para o portal atualizar os valores de estado do indexante e a contagem de documentos.

Um indexante cuja execução mais recente foi bem sucedida mostra **sucesso.** Uma execução de indexante pode ser bem sucedida mesmo que os documentos individuais tenham erros, se o número de erros for inferior à definição de **itens falhados** max do indexante.

Se a execução mais recente terminou com um erro, o estado mostra **falha**. Um estado de **reset** significa que o estado de rastreio de mudança do indexante foi reiniciado.

Clique num indexante da lista para ver mais detalhes sobre as correntes e as recentes execuções do indexante.

   ![Resumo do indexante e histórico de execução](media/search-monitor-indexers/indexer-summary.png "Resumo do indexante e histórico de execução")

O gráfico **de resumo indexante** apresenta um gráfico do número de documentos processados nas suas últimas execuções.

A lista **de detalhes da execução** mostra até 50 dos resultados mais recentes da execução.

Clique num resultado de execução na lista para ver detalhes sobre essa execução. Isto inclui os seus tempos de início e fim, e quaisquer erros e avisos que ocorreram.

   ![Detalhes da execução do indexante](media/search-monitor-indexers/indexer-execution.png "Detalhes da execução do indexante")

Se houver problemas específicos do documento durante a execução, serão listados nos campos de Erros e Avisos.

   ![Detalhes do indexante com erros](media/search-monitor-indexers/indexer-execution-error.png "Detalhes do indexante com erros")

Os avisos são comuns com alguns tipos de indexantes, e nem sempre indicam um problema. Por exemplo, os indexantes que usam serviços cognitivos podem reportar avisos quando ficheiros de imagem ou PDF não contêm qualquer texto para processar.

Para obter mais informações sobre a investigação de erros e avisos indexantes, consulte [problemas de indexante comum na Pesquisa Cognitiva do Azure](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitorize usando APIs de REST

Pode recuperar o estado e o histórico de execução de um indexante utilizando o [comando 'Obter Indexer Status':](/rest/api/searchservice/get-indexer-status)

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

A resposta contém o estado global do indexante, a última invocação indexante (ou em curso) e a história das recentes invocações indexantes.

```output
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
```

O histórico de execução contém até as 50 execuções mais recentes, que são ordenadas por ordem cronológica inversa (a primeira mais recente).

Note que existem dois valores de estado diferentes. O estado de nível superior é para o próprio indexante. Um estado indexante de **execução** significa que o indexante está configurado corretamente e disponível para ser executado, mas não que esteja atualmente em execução.

Cada execução do indexante também tem o seu próprio estatuto que indica se essa execução específica está em curso (**em execução),** ou já concluída com um **sucesso**, **transientarfailure,** ou o persistente estado **de Produção.** 

Quando um indexante é reiniciado para atualizar o seu estado de rastreio de alterações, uma entrada separada do histórico de execução é adicionada com um estado **de Reset.**

Para obter mais detalhes sobre códigos de estado e dados de monitorização do indexante, consulte [GetIndexerStatus](/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitor utilizando o .NET SDK

Pode definir o calendário de um indexante utilizando o Azure Cognitive Search .NET SDK. Para isso, inclua a propriedade de **agenda ao** criar ou atualizar um Indexer.

O exemplo C# a seguir escreve informações sobre o estado de um indexante e os resultados da sua mais recente (ou em curso) corrida para a consola.

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

A saída na consola será mais ou menos assim:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Note que existem dois valores de estado diferentes. O estado de alto nível é o estado do próprio indexante. Um estado indexante de **Running** significa que o indexante está configurado corretamente e disponível para execução, mas não que esteja atualmente a executar.

Cada execução do indexante também tem o seu próprio estatuto para saber se essa execução específica está em curso **(Running),** ou já foi concluída com um estado **de Sucesso** ou **TransientError.** 

Quando um indexante é reiniciado para atualizar o seu estado de rastreio de alterações, uma entrada de histórico separada é adicionada com um estado **de Reset.**

Para obter mais detalhes sobre códigos de estado e informações de monitorização do indexante, consulte [GetIndexerStatus](/rest/api/searchservice/get-indexer-status) na API REST.

Os detalhes sobre erros ou avisos específicos do documento podem ser recuperados enumerando as listas `IndexerExecutionResult.Errors` e `IndexerExecutionResult.Warnings` . .

Para obter mais informações sobre as classes .NET SDK utilizadas para monitorizar os indexantes, consulte [IndexerExecutionInfo](/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo) e [IndexerExecutionResult](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult).
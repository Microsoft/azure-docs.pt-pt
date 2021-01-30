---
title: Monitorizar o estado do indexante e os resultados
titleSuffix: Azure Cognitive Search
description: Monitorize o estado, o progresso e os resultados dos indexadores de Pesquisa Cognitiva Azure no portal Azure, utilizando a API REST ou o .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063264"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Como monitorizar o estado e os resultados do indexante de pesquisa cognitiva do Azure

Pode monitorizar o processamento do indexante no portal Azure, ou programáticamente através de chamadas REST ou de um Azure SDK. Além do estado sobre o próprio indexante, pode rever os tempos de início e fim, e erros detalhados e avisos de uma determinada execução.

## <a name="monitor-using-azure-portal"></a>Monitor usando portal Azure

Pode ver o estado atual de todos os seus indexantes na página de visão geral do seu serviço de pesquisa. As páginas do portal atualizam-se a cada poucos minutos, para que não vejam provas de um novo indexante a funcionar imediatamente.

   ![Lista de indexantes](media/search-monitor-indexers/indexers-list.png "Lista de indexantes")

| Estado | Descrição |
|--------|-------------|
| **Em Curso** | Indica execução ativa. O portal informará sobre informação parcial. À medida que a indexação progride, pode ver o valor conseguido dos **Docs** crescer em resposta. Os indexantes que processam grandes volumes de dados podem demorar muito tempo a ser executados. Por exemplo, os indexantes que lidam com milhões de documentos de origem podem funcionar durante 24 horas e, em seguida, reiniciar quase imediatamente. O estado dos indexantes de alto volume pode sempre dizer **Em Progresso** no portal. Mesmo quando um indexante está em execução, estão disponíveis detalhes sobre o progresso em curso e execuções anteriores. |
| **Com êxito** | Indica que a corrida foi um sucesso. Uma execução de indexante pode ser bem sucedida mesmo que os documentos individuais tenham erros, se o número de erros for inferior à definição de **itens falhados** max do indexante. |
| **Com falhas** | O número de erros excedeu **os itens falhados** do Max e a indexação parou. |
| **Repor** | O estado de rastreio de alterações internas do indexante foi reiniciado. O indexante funcionará na íntegra, refrescando todos os documentos, e não apenas aqueles com novos cartões de tempo. |

Pode clicar num indexante da lista para ver mais detalhes sobre as correntes e as recentes execuções do indexante.

   ![Resumo do indexante e histórico de execução](media/search-monitor-indexers/indexer-summary.png "Resumo do indexante e histórico de execução")

O gráfico **de resumo indexante** apresenta um gráfico do número de documentos processados nas suas últimas execuções.

A lista **de detalhes da execução** mostra até 50 dos resultados mais recentes da execução. Clique num resultado de execução na lista para ver detalhes sobre essa execução. Isto inclui os seus tempos de início e fim, e quaisquer erros e avisos que ocorreram.

   ![Detalhes da execução do indexante](media/search-monitor-indexers/indexer-execution.png "Detalhes da execução do indexante")

Se houver problemas específicos do documento durante a execução, serão listados nos campos de Erros e Avisos.

   ![Detalhes do indexante com erros](media/search-monitor-indexers/indexer-execution-error.png "Detalhes do indexante com erros")

Os avisos são comuns com alguns tipos de indexantes, e nem sempre indicam um problema. Por exemplo, os indexantes que usam serviços cognitivos podem reportar avisos quando ficheiros de imagem ou PDF não contêm qualquer texto para processar. 

Para obter mais informações sobre a investigação de erros e avisos indexantes, consulte [problemas de indexante comum na Pesquisa Cognitiva do Azure](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Monitor utilizando o Estado do Indexante (REST API)

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

Para obter mais detalhes sobre códigos de estado e dados de monitorização do indexante, consulte [o Estado do Indexante](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Monitor usando .NET

Utilizando o Azure Cognitive Search .NET SDK, o exemplo C# seguinte escreve informações sobre o estado de um indexante e os resultados da sua mais recente (ou em curso) corrida para a consola.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
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
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Note que existem dois valores de estado diferentes. O estado de alto nível é o estado do próprio indexante. Um estado indexante de **Running** significa que o indexante está configurado corretamente e disponível para execução, mas não que esteja atualmente a executar.

Cada execução do indexante também tem o seu próprio estatuto para saber se essa execução específica está em curso **(Running),** ou já foi concluída com um estado **de Sucesso** ou **TransientError.** 

Quando um indexante é reiniciado para atualizar o seu estado de rastreio de alterações, uma entrada de histórico separada é adicionada com um estado **de Reset.**

## <a name="next-steps"></a>Passos seguintes

Para obter mais detalhes sobre os códigos de estado e informações de monitorização do indexante, consulte a seguinte referência API:

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)
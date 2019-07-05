---
title: Como monitorizar o estado de indexador e resultados - Azure Search
description: Monitorize o estado, progresso e os resultados de indexadores do Azure Search no portal do Azure, com a API REST ou o SDK do .NET.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486288"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Como monitorizar o estado do indexador de Azure Search e os resultados

O Azure Search fornece o estado e informações sobre execuções de atuais e históricas de cada indexador de monitorização.

Monitorização de indexador é útil quando pretender:

* Execute a controlar o progresso de um indexador durante uma em curso.
* Reveja os resultados da execução de indexador em curso ou anterior.
* Indexador de nível superior de identificar erros e erros ou avisos sobre os documentos individuais que estão a ser indexados.

## <a name="find-indexer-status-and-history-details"></a>Encontrar detalhes de estado e histórico de indexador

Pode aceder a informações de monitorização do indexador de várias formas, incluindo:

* No [portal do Azure](#portal)
* Usando o [REST API](#restapi)
* Usando o [.NET SDK](#dotnetsdk)

Indexador disponível informações de monitorização inclui todas as seguintes (embora os dados em formatos são diferentes com base no método de acesso utilizado):

* Informações de estado sobre o indexador em si
* Informações sobre as mais recentes de execução do indexador, incluindo o respetivo estado, início e horas de fim e erros detalhados e avisos.
* Uma lista de execuções de indexador históricos e seus Estados, resultados, erros e avisos.

Indexadores que processar grandes volumes de dados podem demorar muito tempo para ser executado. Por exemplo, indexadores que lidar com milhões de documentos de origem podem executar durante 24 horas e, em seguida, reinicie quase imediatamente. O estado de indexadores de grande volume sempre poderá dizer **em curso** no portal. Mesmo quando um indexador está em execução, os detalhes estão disponíveis sobre o progresso em curso e execuções anteriores.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Monitor de indexadores no portal

Pode ver o estado atual de todos os indexadores na **indexadores** lista na sua página de descrição geral do serviço de pesquisa.

   ![Lista de indexadores](media/search-monitor-indexers/indexers-list.png "lista de indexadores")

Quando um indexador está em execução, o estado da mostra a lista **em curso**e o **Docs foi concluída com êxito** valor apresenta o número de documentos processados até agora. Pode demorar alguns minutos para o portal atualizar os valores de estado de indexador e contagem de documentos.

Um indexador cuja execução mais recente foi concluída com êxito mostra **êxito**. Execução de um indexador pode ser concluída com êxito, mesmo que os documentos individuais têm erros, se o número de erros é menor que o indexador **máximo de itens falhados** definição.

Se a última execução terminada com um erro, o estado é apresentado **falhada**. Estado **repor** significa que o estado de controlo de alterações do indexador foi reposta.

Clique num indexador na lista para ver mais detalhes sobre o indexador atuais e recentes é executado.

   ![Histórico de resumo e execução do indexador](media/search-monitor-indexers/indexer-summary.png "histórico de resumo e execução do indexador")

O **resumo de indexador** gráfico mostra um gráfico do número de documentos processados nas suas execuções de mais recentes.

O **detalhes da execução** lista mostra até 50 dos resultados da execução mais recentes.

Clique num resultado da execução da lista para ver os detalhes sobre o que são executados. Isto inclui o início e de horas de fim e de quaisquer erros e avisos que ocorreram.

   ![Detalhes da execução do indexador](media/search-monitor-indexers/indexer-execution.png "detalhes da execução do indexador")

Se ocorreram problemas específicos do documento durante a execução, estes serão apresentados os campos de erros e avisos.

   ![Detalhes de indexador com erros](media/search-monitor-indexers/indexer-execution-error.png "detalhes de indexador com erros")

Avisos são comuns com alguns tipos de indexadores e sempre não indicam um problema. Por exemplo indexadores que utilizam os serviços cognitivos podem reportar avisos quando imagem ou ficheiros PDF não contém qualquer texto para processar.

Para obter mais informações sobre como investigar avisos e erros de indexador, consulte [resolução de problemas comuns do indexador no Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Monitorizar indexadores com a API REST

Pode recuperar o histórico de estado e a execução de um indexador através do [comando do obter estado do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o estado geral do indexador, a invocação de indexador último (ou em curso) e o histórico de invocações de indexador recentes.

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

Histórico de execução contém até 50 execuções mais recentes, que são classificadas em ordem cronológica reversa (mais recente primeiro).

Tenha em atenção de que existem dois valores de estado diferente. O estado de nível superior é para o indexador em si. Estado indexador **em execução** significa que o indexador está corretamente definido e disponível para ser executada, mas que não do atualmente em execução.

Cada execução do indexador também tem seu próprio Estado que indica se essa execução específica está em curso (**em execução**), ou já foi concluída com um **êxito**, **transientFailure**, ou **persistentFailure** estado. 

Quando um indexador é reposto para atualizar o respetiva estado de controlo de alterações, uma entrada de histórico de execução separado é adicionada com um **repor** estado.

Para obter mais detalhes sobre códigos de estado e o indexador de dados de monitorização, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Monitorizar os indexadores utilizando o SDK .NET

Pode definir a agenda para um indexador através do SDK de .NET de pesquisa do Azure. Para tal, inclua o **agenda** propriedade quando criar ou atualizar um indexador.

O seguinte C# exemplo escreve informações sobre o estado de um indexador e os resultados da sua mais recente (ou em curso) executam na consola.

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

O resultado na consola será algo parecido com isto:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Tenha em atenção de que existem dois valores de estado diferente. O estado de nível superior é o estado de que o indexador em si. Estado indexador **em execução** significa que o indexador está corretamente definido e estão disponíveis para execução, mas que não está atualmente em execução.

Cada execução do indexador também tem seu próprio Estado para se essa execução específica está em curso (**em execução**), ou já foi concluída com um **êxito** ou **TransientError** Estado. 

Quando um indexador é reposto para atualizar o respetiva estado de controlo de alterações, uma entrada de histórico separado é adicionada com um **repor** estado.

Para obter mais detalhes sobre códigos de estado e informações de monitorização de indexador, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) na REST API.

Detalhes sobre erros específicos do documento ou avisos podem ser obtidos por enumerar as listas `IndexerExecutionResult.Errors` e `IndexerExecutionResult.Warnings`.

Para obter mais informações sobre as classes do SDK do .NET usado para monitorar os indexadores, consulte [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).

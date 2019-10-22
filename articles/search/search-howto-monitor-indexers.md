---
title: Como monitorar o status e os resultados do indexador-Azure Search
description: Monitore o status, o progresso e os resultados de Azure Search indexadores no portal do Azure, usando a API REST ou o SDK do .NET.
ms.date: 06/28/2019
author: RobDixon22
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 0096334e06051b9ff474543384febb37bdf1c8e2
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72533713"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Como monitorar Azure Search o status e os resultados do indexador

Azure Search fornece informações de status e monitoramento sobre as execuções atuais e históricas de todos os indexadores.

O monitoramento do indexador é útil quando você deseja:

* Acompanhe o progresso de um indexador durante uma execução em andamento.
* Examine os resultados da execução do indexador em andamento ou anterior.
* Identificar erros de indexador de nível superior e erros ou avisos sobre documentos individuais que estão sendo indexados.

## <a name="get-status-and-history"></a>Obter status e histórico

Você pode acessar as informações de monitoramento do indexador de várias maneiras, incluindo:

* No [portal do Azure](#portal)
* Usando a [API REST](#restapi)
* Usando o [SDK do .net](#dotnetsdk)

As informações de monitoramento do indexador disponíveis incluem todos os itens a seguir (embora os formatos de dados sejam diferentes com base no método de acesso usado):

* Informações de status sobre o próprio indexador
* Informações sobre a execução mais recente do indexador, incluindo seu status, horários de início e de término e erros e avisos detalhados.
* Uma lista de execuções do indexador histórico e seus status, resultados, erros e avisos.

Os indexadores que processam grandes volumes de dados podem levar muito tempo para serem executados. Por exemplo, indexadores que manipulam milhões de documentos de origem podem ser executados por 24 horas e, em seguida, reiniciam quase imediatamente. O status de indexadores de alto volume pode sempre dizer **em andamento** no Portal. Mesmo quando um indexador estiver em execução, os detalhes estarão disponíveis sobre o progresso em andamento e as execuções anteriores.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitorar usando o portal

Você pode ver o status atual de todos os seus indexadores na lista de **indexadores** na página de visão geral do serviço de pesquisa.

   ![Lista de indexadores](media/search-monitor-indexers/indexers-list.png "Lista de indexadores")

Quando um indexador está em execução, o status na lista mostra **em andamento**e o valor **documentos com êxito** mostra o número de documentos processados até o momento. Pode levar alguns minutos para que o portal atualize os valores de status e contagens de documentos do indexador.

Um indexador cuja execução mais recente foi bem-sucedida mostra **êxito**. Uma execução de indexador pode ser bem-sucedida mesmo que documentos individuais tenham erros, se o número de erros for menor que a configuração de **itens de falha máximo** do indexador.

Se a execução mais recente terminar com um erro, o status mostrará **falha**. Um status de **Redefinir** significa que o estado de controle de alterações do indexador foi redefinido.

Clique em um indexador na lista para ver mais detalhes sobre as execuções atuais e recentes do indexador.

   ![Resumo do indexador e histórico de execução](media/search-monitor-indexers/indexer-summary.png "Resumo do indexador e histórico de execução")

O gráfico de **Resumo do indexador** exibe um grafo do número de documentos processados em suas execuções mais recentes.

A lista **detalhes da execução** mostra até 50 dos resultados de execução mais recentes.

Clique em um resultado de execução na lista para ver as informações específicas sobre essa execução. Isso inclui os horários de início e término e quaisquer erros e avisos ocorridos.

   ![Detalhes de execução do indexador](media/search-monitor-indexers/indexer-execution.png "Detalhes de execução do indexador")

Se houver problemas específicos ao documento durante a execução, eles serão listados nos campos erros e avisos.

   ![Detalhes do indexador com erros](media/search-monitor-indexers/indexer-execution-error.png "Detalhes do indexador com erros")

Os avisos são comuns com alguns tipos de indexadores e nem sempre indicam um problema. Por exemplo indexadores que usam serviços cognitivas podem relatar avisos quando arquivos de imagem ou PDF não contêm nenhum texto para processar.

Para obter mais informações sobre como investigar erros e avisos do indexador, consulte [Solucionando problemas comuns do indexador no Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitorar usando APIs REST

Você pode recuperar o status e o histórico de execução de um indexador usando o [comando obter status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

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

O histórico de execução contém até as 50 execuções mais recentes, que são classificadas em ordem cronológica inversa (mais recente primeiro).

Observe que há dois valores de status diferentes. O status de nível superior é para o indexador em si. Um status de indexador de **em execução** significa que o indexador está configurado corretamente e disponível para execução, mas não está em execução no momento.

Cada execução do indexador também tem seu próprio status que indica se a execução específica está em andamento (**em execução**) ou já concluída com o status **êxito**, **transientFailure**ou **persistentFailure** . 

Quando um indexador é redefinido para atualizar seu estado de controle de alterações, uma entrada de histórico de execução separada é adicionada com um status de **redefinição** .

Para obter mais detalhes sobre códigos de status e dados de monitoramento do indexador, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitorar usando o SDK do .NET

Você pode definir a agenda para um indexador usando o SDK do .NET Azure Search. Para fazer isso, inclua a propriedade **Schedule** ao criar ou atualizar um indexador.

O exemplo C# a seguir grava informações sobre o status de um indexador e os resultados de sua execução mais recente (ou contínua) no console.

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

A saída no console terá uma aparência semelhante a esta:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Observe que há dois valores de status diferentes. O status de nível superior é o status do próprio indexador. Um status de indexador de **em execução** significa que o indexador está configurado corretamente e disponível para execução, mas não está em execução no momento.

Cada execução do indexador também tem seu próprio status para se a execução específica está em andamento (**em execução**) ou já foi concluída com um status de **êxito** ou **TransientError** . 

Quando um indexador é redefinido para atualizar seu estado de controle de alterações, uma entrada de histórico separada é adicionada com um status de **redefinição** .

Para obter mais detalhes sobre códigos de status e informações de monitoramento do indexador, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) na API REST.

Detalhes sobre erros ou avisos específicos do documento podem ser recuperados enumerando as listas `IndexerExecutionResult.Errors` e `IndexerExecutionResult.Warnings`.

Para obter mais informações sobre as classes do SDK do .NET usadas para monitorar indexadores, consulte [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).

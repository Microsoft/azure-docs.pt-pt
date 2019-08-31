---
title: Atualizar para o SDK do .NET Azure Search versão 1,1-Azure Search
description: Migre o código para o Azure Search .NET SDK versão 1,1 das versões mais antigas da API. Saiba o que há de novo e quais alterações de código são necessárias.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 8227e1b372af1eee43db59da2cfad165d67be9ae
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183264"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Atualizando para o SDK do .NET Azure Search versão 1,1

Se você estiver usando a versão 1.0.2-Preview ou anterior do [SDK Azure Search .net](https://aka.ms/search-sdk), este artigo ajudará você a atualizar seu aplicativo para usar a versão 1,1.

Para obter uma explicação mais geral do SDK, incluindo exemplos, consulte [como usar Azure Search de um aplicativo .net](search-howto-dotnet-sdk.md).

> [!NOTE]
> Depois de atualizar para a versão 1,1, ou se você já estiver usando uma versão entre 1,1 e 2,0-Preview, inclusive, deverá atualizar para a versão 3. Consulte [Atualizando para o SDK do .net Azure Search versão 3](search-dotnet-sdk-migration.md) para obter instruções.
>

Primeiro, atualize a referência do NuGet `Microsoft.Azure.Search` para usar o console do Gerenciador de pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto.

Se você estava usando anteriormente a versão 1.0.0-Preview, o 1.0.1-Preview ou o 1.0.2-Preview, a compilação deve ter sucesso e você está pronto para começar!

Se você estava usando anteriormente a versão 0.13.0-Preview ou anterior, você deverá ver erros de compilação como o seguinte:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A próxima etapa é corrigir os erros de compilação um a um. A maioria exigirá a alteração de alguns nomes de classe e método que foram renomeados no SDK. A [lista de alterações significativas na versão 1,1](#ListOfChangesV1) contém uma lista dessas alterações de nome.

Se você estiver usando classes personalizadas para modelar seus documentos e essas classes tiverem Propriedades de tipos primitivos não anuláveis (por `int` exemplo `bool` , C#ou em), haverá uma correção de bug na versão 1,1 do SDK do qual você deve estar ciente. Consulte [correções de bugs na versão 1,1](#BugFixesV1) para obter mais detalhes.

Por fim, depois de corrigir os erros de compilação, você poderá fazer alterações em seu aplicativo para tirar proveito da nova funcionalidade, se desejar.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista de alterações significativas na versão 1,1
A lista a seguir é ordenada pela probabilidade de que a alteração afete o código do aplicativo.

### <a name="indexbatch-and-indexaction-changes"></a>Alterações de IndexBatch e indexAction
`IndexBatch.Create`foi renomeado para `IndexBatch.New` e não tem mais um `params` argumento. Você pode usar `IndexBatch.New` para lotes que misturam diferentes tipos de ações (mesclagens, exclusões, etc.). Além disso, há novos métodos estáticos para criar lotes onde todas as ações são as mesmas `Delete`:, `Merge`, `MergeOrUpload`e `Upload`.

`IndexAction`Não tem mais construtores públicos e suas propriedades agora são imutáveis. Você deve usar os novos métodos estáticos para criar ações para diferentes finalidades `Merge`: `MergeOrUpload` `Delete`,, `Upload`e. `IndexAction.Create`foi removido. Se você usou a sobrecarga que usa apenas um documento, certifique-se de `Upload` usar em vez disso.

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Você pode alterá-lo para corrigir quaisquer erros de compilação:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se desejar, você pode simplificar ainda mais a ti:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException alterações
A `IndexBatchException.IndexResponse` Propriedade foi renomeada para `IndexingResults`e seu tipo é agora `IList<IndexingResult>`.

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Você pode alterá-lo para corrigir quaisquer erros de compilação:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Alterações do método de operação
Cada operação no SDK do .NET Azure Search é exposta como um conjunto de sobrecargas de método para chamadores síncronos e assíncronos. As assinaturas e a fatoração dessas sobrecargas de método foram alteradas na versão 1,1.

Por exemplo, a operação "obter estatísticas de índice" em versões mais antigas do SDK expôs estas assinaturas:

Em `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

Em `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

As assinaturas de método para a mesma operação na versão 1,1 têm a seguinte aparência:

Em `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

Em `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

A partir da versão 1,1, o SDK do .NET Azure Search organiza os métodos de operação de maneira diferente:

* Os parâmetros opcionais agora são modelados como parâmetros padrão em vez de sobrecargas de método adicionais. Isso reduz o número de sobrecargas de método, às vezes drasticamente.
* Os métodos de extensão agora ocultam muitos dos detalhes incorretos do HTTP do chamador. Por exemplo, versões mais antigas do SDK retornavam um objeto de resposta com um código de status HTTP, que você geralmente não precisa verificar porque os `CloudException` métodos de operação geram qualquer código de status que indique um erro. Os novos métodos de extensão simplesmente retornam objetos de modelo, poupando-lhe o problema de ter que desenvolva-los em seu código.
* Por outro lado, as interfaces principais agora expõem métodos que oferecem mais controle no nível de HTTP, se necessário. Agora você pode passar os cabeçalhos HTTP personalizados para serem incluídos nas solicitações e o novo `AzureOperationResponse<T>` tipo de retorno fornece acesso direto `HttpRequestMessage` ao e `HttpResponseMessage` à operação. `AzureOperationResponse`é definido no `Microsoft.Rest.Azure` namespace e substitui `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>Alterações de pontuaçãoparameters
Uma nova classe chamada `ScoringParameter` foi adicionada ao SDK mais recente para facilitar o fornecimento de parâmetros para perfis de pontuação em uma consulta de pesquisa. Anteriormente, `ScoringProfiles` a propriedade `SearchParameters` da classe foi digitada `IList<string>`como; Agora ele é digitado `IList<ScoringParameter>`como.

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Você pode alterá-lo para corrigir quaisquer erros de compilação: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Alterações de classe de modelo
Devido às alterações de assinatura descritas nas [alterações do método de operação](#OperationMethodChanges), muitas `Microsoft.Azure.Search.Models` classes no namespace foram renomeadas ou removidas. Por exemplo:

* `IndexDefinitionResponse`foi substituído por`AzureOperationResponse<Index>`
* `DocumentSearchResponse` mudou de nome para `DocumentSearchResult`
* `IndexResult` mudou de nome para `IndexingResult`
* `Documents.Count()`agora retorna um `long` com a contagem de documentos em vez de um`DocumentCountResponse`
* `IndexGetStatisticsResponse` mudou de nome para `IndexGetStatisticsResult`
* `IndexListResponse` mudou de nome para `IndexListResult`

Para resumir `OperationResponse`, as classes derivadas que existiam apenas para encapsular um objeto de modelo foram removidas. As classes restantes tiveram seu sufixo alterado de `Response` para. `Result`

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Você pode alterá-lo para corrigir quaisquer erros de compilação:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### <a name="response-classes-and-ienumerable"></a>Classes de resposta e IEnumerable
Uma alteração adicional que pode afetar seu código é que as classes de resposta que mantêm as coleções `IEnumerable<T>`não implementam mais. Em vez disso, você pode acessar a propriedade de coleção diretamente. Por exemplo, se o seu código tiver esta aparência:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Você pode alterá-lo para corrigir quaisquer erros de compilação:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Caso especial para aplicativos Web
Se você tiver um aplicativo Web que se serializa `DocumentSearchResponse` diretamente para enviar os resultados da pesquisa para o navegador, será necessário alterar seu código ou os resultados não serão serializados corretamente. Por exemplo, se o seu código tiver esta aparência:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Você pode alterá-la obtendo `.Results` a propriedade da resposta de pesquisa para corrigir o processamento do resultado da pesquisa:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Você terá que procurar tais casos em seu código por conta própria; **O compilador não avisará você** porque `JsonResult.Data` é do tipo `object`.

### <a name="cloudexception-changes"></a>Alterações de cloudexception
A `CloudException` classe foi movida `Hyak.Common` do namespace para o `Microsoft.Rest.Azure` namespace. Além disso, `Error` sua propriedade foi renomeada para `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Alterações de SearchServiceClient e SearchIndexClient
O tipo da `Credentials` Propriedade foi alterado de `SearchCredentials` para sua classe base, `ServiceClientCredentials`. Se você `SearchCredentials` precisar acessar o de um `SearchIndexClient` ou `SearchServiceClient`, use a nova `SearchCredentials` propriedade.

Em versões mais antigas do SDK `SearchServiceClient` e `SearchIndexClient` tinham construtores que levaram um `HttpClient` parâmetro. Eles foram substituídos por construtores que usam um `HttpClientHandler` e uma matriz de `DelegatingHandler` objetos. Isso facilita a instalação de manipuladores personalizados para o pré-processamento de solicitações HTTP, se necessário.

Por fim, os construtores que levaram um `Uri` e `SearchCredentials` foram alterados. Por exemplo, se você tiver um código parecido com este:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Você pode alterá-lo para corrigir quaisquer erros de compilação:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Observe também que o tipo do parâmetro de credenciais foi alterado para `ServiceClientCredentials`. É improvável que isso afete seu código `SearchCredentials` desde que seja `ServiceClientCredentials`derivado de.

### <a name="passing-a-request-id"></a>Passando uma ID de solicitação
Em versões mais antigas do SDK, você pode definir uma ID de solicitação no `SearchServiceClient` ou `SearchIndexClient` e ela seria incluída em cada solicitação para a API REST. Isso é útil para solucionar problemas com o serviço de pesquisa se você precisar entrar em contato com o suporte. No entanto, é mais útil definir uma ID de solicitação exclusiva para cada operação em vez de usar a mesma ID para todas as operações. Por esse motivo, os `SetClientRequestId` métodos de `SearchServiceClient` e `SearchIndexClient` foram removidos. Em vez disso, você pode passar uma ID de solicitação para cada método de `SearchRequestOptions` operação por meio do parâmetro opcional.

> [!NOTE]
> Em uma versão futura do SDK, adicionaremos um novo mecanismo para definir uma ID de solicitação globalmente nos objetos de cliente que é consistente com a abordagem usada por outros SDKs do Azure.
> 
> 

### <a name="example"></a>Exemplo
Se você tiver um código parecido com este:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Você pode alterá-lo para corrigir quaisquer erros de compilação:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Alterações de nome de interface
Os nomes da interface do grupo de operações foram alterados para serem consistentes com seus nomes de propriedade correspondentes:

* O tipo de `ISearchServiceClient.Indexes` foi renomeado de `IIndexOperations` para `IIndexesOperations`.
* O tipo de `ISearchServiceClient.Indexers` foi renomeado de `IIndexerOperations` para `IIndexersOperations`.
* O tipo de `ISearchServiceClient.DataSources` foi renomeado de `IDataSourceOperations` para `IDataSourcesOperations`.
* O tipo de `ISearchIndexClient.Documents` foi renomeado de `IDocumentOperations` para `IDocumentsOperations`.

Essa alteração é improvável de afetar seu código, a menos que você tenha criado simulações dessas interfaces para fins de teste.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Correções de bugs na versão 1,1
Houve um bug em versões mais antigas do SDK do .NET Azure Search relacionado à serialização de classes de modelo personalizado. O bug poderá ocorrer se você tiver criado uma classe de modelo Personalizada com uma propriedade de um tipo de valor não anulável.

### <a name="steps-to-reproduce"></a>Etapas para reproduzir
Crie uma classe de modelo Personalizada com uma propriedade de tipo de valor não anulável. Por exemplo, adicione uma propriedade `UnitCount` pública do tipo `int` em vez `int?`de.

Se você indexar um documento com o valor padrão desse tipo (por exemplo, 0 para `int`), o campo será nulo em Azure Search. Se você Pesquisar posteriormente esse documento, a chamada `Search` `JsonSerializationException` emitirá uma reclamação de que não pode converter `null` para `int`.

Além disso, os filtros podem não funcionar conforme o esperado, pois NULL foi gravado no índice em vez do valor pretendido.

### <a name="fix-details"></a>Corrigir detalhes
Corrigimos esse problema na versão 1,1 do SDK. Agora, se você tiver uma classe de modelo como esta:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e você define `IntValue` como 0, esse valor agora é serializado corretamente como 0 na transmissão e armazenado como 0 no índice. A viagem de ida e volta também funciona conforme o esperado.

Há um problema potencial a ser considerado com essa abordagem: Se você usar um tipo de modelo com uma propriedade não anulável, precisará **garantir** que nenhum documento no índice contenha um valor nulo para o campo correspondente. Nem o SDK nem a API REST do Azure Search ajudarão você a impor isso.

Isso não é apenas uma preocupação hipotética: Imagine um cenário em que você adiciona um novo campo a um índice existente que é do `Edm.Int32`tipo. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, ainda recomendamos que você use tipos anuláveis em suas classes de modelo como uma prática recomendada.

Para obter mais detalhes sobre esse bug e a correção, consulte [este problema no GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).


---
title: Atualizar para o SDK .NET da Azure Search versão 1.1 - Azure Search
description: Migre o código para o SDK .NET da Azure Search versão 1.1 de versões de API mais antigas. Saiba quais são as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 3f47656bb13d08ea56cf25a2a29897722abb1cdb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024154"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Atualizar para o SDK .NET da Azure Search versão 1.1

> [!Important]
> Este conteúdo está ainda estão em construção. Versão 9.0 do SDK de .NET de pesquisa do Azure está disponível em NuGet. Estamos a trabalhar neste artigo para explicar como atualizar para 9.0. 
> 

Se estiver a utilizar 1.0.2-preview de versão ou mais antigas do [SDK .NET da Azure Search](https://aka.ms/search-sdk), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 1.1.

Para obter instruções mais geral do SDK, incluindo exemplos, consulte [como utilizar o Azure Search a partir de um aplicativo .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Depois de atualizar para versão 1.1, ou se já estiver a utilizar uma versão entre 1.1 e 2.0-pré-visualização, inclusive, deve atualizar para a versão 3. Ver [atualizar para o SDK .NET da Azure Search versão 3](search-dotnet-sdk-migration.md) para obter instruções.
>

Em primeiro lugar, atualizar a sua referência de NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou ao clicar com o botão direito em suas referências de projeto e selecionando "Gerir NuGet pacotes..." no Visual Studio.

Assim que o NuGet tiver baixado os novos pacotes e suas dependências, recrie seu projeto.

Se anteriormente estava a utilizar a versão 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview a compilação deve ter êxito e está pronto para começar!

Se estava a utilizar anteriormente 0.13.0-preview de versão ou mais, deverá ver compilação erros semelhantes ao seguinte:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A próxima etapa é corrigir os erros de compilação individualmente. A maioria será necessário alterar alguns nomes de classe e método foram renomeadas no SDK. [Lista de alterações significativas na versão 1.1](#ListOfChangesV1) contém uma lista dessas alterações de nome.

Se estiver a utilizar classes personalizadas para modelar os seus documentos, e essas classes têm propriedades de tipos primitivos não anulável (por exemplo, `int` ou `bool` em C#), há uma correção de bug na versão 1.1 do SDK dos quais deve estar ciente. Ver [correções de erros na versão 1.1](#BugFixesV1) para obter mais detalhes.

Por fim, depois de ter de corrigir os erros de compilação, pode efetuar alterações à sua aplicação para tirar partido das novas funcionalidades, se desejar.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista de alterações significativas na versão 1.1
A lista seguinte é ordenada pela probabilidade de que a alteração afetará o código da aplicação.

### <a name="indexbatch-and-indexaction-changes"></a>Alterações IndexBatch e IndexAction
`IndexBatch.Create` o nome foi mudado para `IndexBatch.New` e já não tem um `params` argumento. Pode usar `IndexBatch.New` para lotes misturam tipos diferentes de ações (mesclagens, eliminações, etc.). Além disso, existem novos métodos estáticos para a criação de batches em que todas as ações são os mesmos: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`.

`IndexAction` já não tem construtores públicos e as respetivas propriedades agora são imutáveis. Deve usar os novos métodos estáticos para a criação de ações para diferentes fins: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`. `IndexAction.Create` foi removido. Se utilizou a sobrecarga que usa apenas um documento, certifique-se de usar `Upload` em vez disso.

#### <a name="example"></a>Exemplo
Se o seu código é semelhante a isto:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se quiser, pode simplificar ainda mais-lo a este:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>Alterações de IndexBatchException
O `IndexBatchException.IndexResponse` propriedade foi renomeada para `IndexingResults`, e seu tipo é agora `IList<IndexingResult>`.

#### <a name="example"></a>Exemplo
Se o seu código é semelhante a isto:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Alterações do método de operação
Cada operação no SDK de .NET da Azure Search é exposta como um conjunto de sobrecargas do método para os autores de chamadas síncronas e assíncronas. As assinaturas e a decomposição dessas sobrecargas do método foi alterado na versão 1.1.

Por exemplo, a operação "Obter estatísticas de índice" em versões mais antigas do SDK exposta estas assinaturas:

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

As assinaturas de método para a mesma operação na versão 1.1 tem esta aparência:

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

A partir da versão 1.1, o SDK .NET da Azure Search organiza os métodos de operação diferente:

* Parâmetros opcionais agora são modelados como predefinido em vez disso, os parâmetros que sobrecargas do método adicional. Isso reduz o número de sobrecargas do método, às vezes drasticamente.
* Os métodos de extensão agora ocultam muitos dos detalhes estranhos de HTTP do chamador. Por exemplo, as versões mais antigas do SDK devolveu um objeto de resposta com um código de estado HTTP, que, muitas vezes, não tem de verificar uma vez que os métodos de operação lançar `CloudException` para qualquer código de estado que indica um erro. Os novos métodos de extensão retornam apenas objetos de modelo, poupando o trabalho de ter de anular a moldagem-los em seu código.
* Por outro lado, as principais interfaces expõem agora métodos que oferecem mais controle no nível HTTP, se necessário. Agora pode passar nos cabeçalhos HTTP personalizados a serem incluídos em solicitações e a nova `AzureOperationResponse<T>` tipo de retorno proporciona acesso direto para o `HttpRequestMessage` e `HttpResponseMessage` para a operação. `AzureOperationResponse` está definido no `Microsoft.Rest.Azure` espaço de nomes e substitui `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>Alterações de ScoringParameters
Uma nova classe denominada `ScoringParameter` foi adicionado no SDK mais recente para facilitar fornecer os parâmetros para perfis de classificação numa consulta de pesquisa. Anteriormente a `ScoringProfiles` propriedade o `SearchParameters` classe foi digitada como `IList<string>`; Agora ele é digitado como `IList<ScoringParameter>`.

#### <a name="example"></a>Exemplo
Se o seu código é semelhante a isto:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Pode alterá-lo a este para corrigir quaisquer erros de compilação: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Alterações no modelo de classe
Devido às alterações de assinatura descritas em [alterações de método de operação](#OperationMethodChanges), muitas classes no `Microsoft.Azure.Search.Models` espaço de nomes foram renomeado ou removido. Por exemplo:

* `IndexDefinitionResponse` foi substituído por `AzureOperationResponse<Index>`
* `DocumentSearchResponse` mudou de nome para `DocumentSearchResult`
* `IndexResult` mudou de nome para `IndexingResult`
* `Documents.Count()` agora retorna um `long` com a contagem de documentos em vez de um `DocumentCountResponse`
* `IndexGetStatisticsResponse` mudou de nome para `IndexGetStatisticsResult`
* `IndexListResponse` mudou de nome para `IndexListResult`

Para resumir, `OperationResponse`-derivadas de classes que existiam apenas moldar um objeto de modelo foram removidos. As classes restantes tiveram suas sufixo mudou de `Response` para `Result`.

#### <a name="example"></a>Exemplo
Se o seu código é semelhante a isto:

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

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

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
Uma alteração adicional que pode afetar seu código é que as classes de resposta que contêm coleções já não está a implementar `IEnumerable<T>`. Em vez disso, pode acessar diretamente a propriedade de coleção. Por exemplo, se seu código é semelhante a esta:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Caso especial para aplicativos web
Se tiver uma aplicação web que serializa `DocumentSearchResponse` diretamente para enviar os resultados da pesquisa para o navegador, precisará alterar seu código ou os resultados nebude serializovat corretamente. Por exemplo, se seu código é semelhante a esta:

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

Pode alterá-lo, obtendo o `.Results` propriedade da resposta de pesquisa para corrigir o processamento do resultado de pesquisa:

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

Terá de procurar nestes casos no seu código por conta própria; **o compilador não o irá avisar** porque `JsonResult.Data` é do tipo `object`.

### <a name="cloudexception-changes"></a>Alterações de CloudException
O `CloudException` classe foi movido do `Hyak.Common` espaço de nomes para o `Microsoft.Rest.Azure` espaço de nomes. Além disso, seu `Error` propriedade foi alterada para `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Alterações SearchServiceClient e SearchIndexClient
O tipo do `Credentials` propriedade foi alterado de `SearchCredentials` à sua classe base, `ServiceClientCredentials`. Se precisar de aceder a `SearchCredentials` de um `SearchIndexClient` ou `SearchServiceClient`, utilize o novo `SearchCredentials` propriedade.

Em versões mais antigas do SDK, `SearchServiceClient` e `SearchIndexClient` tinha construtores que demoraram um `HttpClient` parâmetro. Eles foram substituídos com construtores que efetuar uma `HttpClientHandler` e uma matriz de `DelegatingHandler` objetos. Isto torna mais fácil de instalar manipuladores personalizados para processar previamente os pedidos HTTP, se necessário.

Por fim, os construtores que demorou um `Uri` e `SearchCredentials` foram alterados. Por exemplo, se tiver código parecido com este:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Tenha também em atenção que o tipo do parâmetro de credenciais foi alterado para `ServiceClientCredentials`. Isso é pouco provável que afetam o seu código desde `SearchCredentials` é derivado de `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Passando um ID de pedido
Em versões mais antigas do SDK, poderia definir um ID de pedido no `SearchServiceClient` ou `SearchIndexClient` e deve ser incluído em cada solicitação para a API REST. Isto é útil para resolução de problemas com o serviço de pesquisa se precisar de contactar o suporte. No entanto, é mais útil para definir um ID de pedido exclusivo para cada operação em vez de utilizar o mesmo ID para todas as operações. Por esse motivo, o `SetClientRequestId` métodos `SearchServiceClient` e `SearchIndexClient` foram removidos. Em vez disso, pode passar um ID de pedido para cada método de operação via o opcional `SearchRequestOptions` parâmetro.

> [!NOTE]
> Numa versão futura do SDK, vamos adicionar um novo mecanismo para a definição de um ID de pedido globalmente no cliente objetos, consistente com a abordagem utilizada por outros SDKs do Azure.
> 
> 

### <a name="example"></a>Exemplo
Se tiver código parecido com este:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Pode alterá-lo a este para corrigir quaisquer erros de compilação:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Alterações de nome de interface
Os nomes de interface de operação do grupo foram alterados para ser consistente com seus nomes de propriedade correspondente:

* O tipo de `ISearchServiceClient.Indexes` foi alterado de `IIndexOperations` para `IIndexesOperations`.
* O tipo de `ISearchServiceClient.Indexers` foi alterado de `IIndexerOperations` para `IIndexersOperations`.
* O tipo de `ISearchServiceClient.DataSources` foi alterado de `IDataSourceOperations` para `IDataSourcesOperations`.
* O tipo de `ISearchIndexClient.Documents` foi alterado de `IDocumentOperations` para `IDocumentsOperations`.

Esta alteração é pouco provável que afetam o seu código, a menos que criou as simulações dessas interfaces para fins de teste.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Correções de erros na versão 1.1
Ocorreu um erro em versões anteriores do SDK .NET da Azure Search relacionados com a serialização de classes de modelo personalizado. O erro pode ocorrer se tiver criado uma classe de modelo personalizado com uma propriedade de um tipo de valor não nulo.

### <a name="steps-to-reproduce"></a>Passos para reproduzir
Crie uma classe de modelo personalizado com uma propriedade do tipo de valor não nulo. Por exemplo, adicione uma pública `UnitCount` propriedade do tipo `int` em vez de `int?`.

Se indexar um documento com o valor padrão desse tipo (por exemplo, 0 para `int`), o campo será nulo na Azure Search. Se pesquisar, em seguida, para esse documento, o `Search` chamada lançará `JsonSerializationException` reclamando que não é possível converter `null` para `int`.

Além disso, os filtros podem não funcionar conforme esperado, uma vez que null foi escrito para o índice em vez do valor pretendido.

### <a name="fix-details"></a>Corrigir detalhes
Podemos corrigir este problema na versão 1.1 do SDK. Agora, se tiver uma classe de modelo como este:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e definir `IntValue` como 0, esse valor é agora corretamente serializado como 0 na conexão e armazenado como 0 no índice. Também tripping redondo funciona conforme esperado.

Existe um problema potencial estar atento com essa abordagem: Se usar um tipo de modelo com uma propriedade não anulável, terá **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem a API de REST do Azure Search irão ajudá-lo a impor tais pedidos.

Não é apenas uma preocupação hipotética: Imagine um cenário em que adicionar um novo campo para um índice existente que é do tipo `Edm.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelo como melhor prática.

Para obter mais detalhes sobre esse bug e a correção, consulte [este problema no GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).


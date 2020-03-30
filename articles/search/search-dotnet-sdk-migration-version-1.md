---
title: Upgrade para Azure Search .NET SDK versão 1.1
titleSuffix: Azure Cognitive Search
description: Migrar código para o Azure Search .NET SDK versão 1.1 a partir de versões API mais antigas. Saiba o que é novo e quais as mudanças de código que são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792388"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Upgrade para Azure Search .NET SDK versão 1.1

Se estiver a utilizar a versão 1.0.2-preview ou mais antiga do [Azure Search .NET SDK,](https://aka.ms/search-sdk)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 1.1.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Pesquisa Azure a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Uma vez atualizado para a versão 1.1, ou se já estiver a utilizar uma versão entre 1.1 e 2.0-preview inclusive, deve fazer o upgrade para a versão 3. Consulte o Upgrade para a versão 3 do [Azure Search .NET SDK](search-dotnet-sdk-migration.md) 3 para obter instruções.
>

Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a Consola nuGet Package Manager ou clicando corretamente nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." no Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto.

Se já estava a utilizar a versão 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview, a construção deve ter sucesso e está pronto para ir!

Se já estava a utilizar a versão 0.13.0-preview ou mais antiga, deve ver erros de construção como os seguintes:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

O próximo passo é corrigir os erros de construção um a um. A maioria exigirá a alteração de alguns nomes de classe e métodos que foram renomeados no SDK. [A lista de alterações de rutura na versão 1.1](#ListOfChangesV1) contém uma lista destas alterações de nome.

Se estiver a usar aulas personalizadas para modelar os seus documentos, e `int` essas `bool` classes tiverem propriedades de tipos primitivos não nuníveis (por exemplo, ou em C#), existe uma correção de bugs na versão 1.1 do SDK da qual deve estar atento. Consulte as correções do [Bug na versão 1.1](#BugFixesV1) para obter mais detalhes.

Finalmente, uma vez corrigido quaisquer erros de construção, pode fazer alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista de alterações de rutura na versão 1.1
A seguinte lista é ordenada pela probabilidade de a alteração afetar o seu código de aplicação.

### <a name="indexbatch-and-indexaction-changes"></a>Alterações indexbatch e IndexAction
`IndexBatch.Create`foi renomeado `IndexBatch.New` para e já `params` não tem uma discussão. Pode utilizar `IndexBatch.New` para lotes que misturam diferentes tipos de ações (fusões, exclusões, etc.). Além disso, existem novos métodos estáticos para `Delete`a `Merge` `MergeOrUpload`criação `Upload`de lotes onde todas as ações são as mesmas: , , e .

`IndexAction`já não tem construtores públicos e as suas propriedades são agora imutáveis. Deve utilizar os novos métodos estáticos `Delete`para `Merge` `MergeOrUpload`criar `Upload`ações para diferentes fins: , e . `IndexAction.Create`foi removido. Se usou a sobrecarga que leva apenas `Upload` um documento, certifique-se de que utiliza.

#### <a name="example"></a>Exemplo
Se o seu código se parecer com este:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Pode mudá-lo para isto para corrigir eventuais erros de construção:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se quiser, pode simplificar ainda mais:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>Alterações de exceção do IndexBatch
A `IndexBatchException.IndexResponse` propriedade foi renomeada para, `IndexingResults`e `IList<IndexingResult>`o seu tipo é agora .

#### <a name="example"></a>Exemplo
Se o seu código se parecer com este:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Pode mudá-lo para isto para corrigir eventuais erros de construção:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Alterações no método de operação
Cada operação no Azure Search .NET SDK é exposta como um conjunto de sobrecargas de método para chamadas sincronizadas e assíncronas. As assinaturas e factoring destas sobrecargas de método mudaram na versão 1.1.

Por exemplo, a operação "Obter Estatísticas de Índices" em versões mais antigas do SDK expôs estas assinaturas:

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

As assinaturas do método para a mesma operação na versão 1.1 são assim:

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

Começando pela versão 1.1, o Azure Search .NET SDK organiza métodos de operação de forma diferente:

* Os parâmetros opcionais são agora modelados como parâmetros padrão em vez de sobrecargas adicionais do método. Isto reduz o número de sobrecargas de método, às vezes dramaticamente.
* Os métodos de extensão escondem agora muitos dos detalhes estranhos do HTTP do chamador. Por exemplo, versões mais antigas do SDK devolveram um objeto de resposta com um `CloudException` código de estado HTTP, que muitas vezes não precisava de verificar porque os métodos de operação lançam para qualquer código de estado que indique um erro. Os novos métodos de extensão apenas devolvem objetos de modelo, poupando-lhe o trabalho de ter que desembrulhá-los no seu código.
* Inversamente, as interfaces centrais agora expõem métodos que lhe dão mais controlo ao nível HTTP, se precisar. Agora pode passar em cabeçalhos HTTP personalizados para serem `AzureOperationResponse<T>` incluídos em pedidos, `HttpRequestMessage` `HttpResponseMessage` e o novo tipo de devolução dá-lhe acesso direto ao e para a operação. `AzureOperationResponse`é definido `Microsoft.Rest.Azure` no espaço de `Hyak.Common.OperationResponse`nome e substitui .

### <a name="scoringparameters-changes"></a>Alterações de Pontos Parâmetros
Uma nova `ScoringParameter` classe nomeada foi adicionada no mais recente SDK para facilitar a disponibilização de parâmetros para marcar perfis numa consulta de pesquisa. Anteriormente, `ScoringProfiles` a `SearchParameters` propriedade da classe `IList<string>`era dactilografada como; Agora é dactilografado como `IList<ScoringParameter>`.

#### <a name="example"></a>Exemplo
Se o seu código se parecer com este:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Pode mudá-lo para isto para corrigir eventuais erros de construção: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Mudanças de classe modelo
Devido às alterações de assinatura descritas nas `Microsoft.Azure.Search.Models` alterações do método de [operação,](#OperationMethodChanges)muitas classes no espaço de nome foram renomeadas ou removidas. Por exemplo:

* `IndexDefinitionResponse`foi substituído por`AzureOperationResponse<Index>`
* `DocumentSearchResponse` mudou de nome para `DocumentSearchResult`
* `IndexResult` mudou de nome para `IndexingResult`
* `Documents.Count()`agora devolve `long` um com a contagem de documentos em vez de um`DocumentCountResponse`
* `IndexGetStatisticsResponse` mudou de nome para `IndexGetStatisticsResult`
* `IndexListResponse` mudou de nome para `IndexListResult`

Resumindo, `OperationResponse`as classes derivadas que existiam apenas para embrulhar um objeto modelo foram removidas. As restantes aulas tiveram o `Response` seu `Result`sufixo alterado de .

#### <a name="example"></a>Exemplo
Se o seu código se parecer com este:

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

Pode mudá-lo para isto para corrigir eventuais erros de construção:

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

#### <a name="response-classes-and-ienumerable"></a>Aulas de resposta e IEnumerable
Uma alteração adicional que pode afetar o seu código é `IEnumerable<T>`que as classes de resposta que detêm coleções já não implementam . Em vez disso, pode aceder diretamente à propriedade de recolha. Por exemplo, se o seu código se parecer com este:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Pode mudá-lo para isto para corrigir eventuais erros de construção:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Caso especial para aplicações web
Se tiver uma aplicação web `DocumentSearchResponse` que se serialize diretamente para enviar resultados de pesquisa para o navegador, terá de alterar o seu código ou os resultados não se serializarão corretamente. Por exemplo, se o seu código se parecer com este:

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

Pode mudá-lo obtendo a `.Results` propriedade da resposta de pesquisa para corrigir a renderização do resultado da pesquisa:

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

Terá de procurar tais casos no seu código; **O compilador não** o `JsonResult.Data` avisa `object`porque é de tipo.

### <a name="cloudexception-changes"></a>Alterações de CloudException
A `CloudException` aula mudou-se do espaço do `Hyak.Common` nome para o espaço do `Microsoft.Rest.Azure` nome. Além `Error` disso, a sua `Body`propriedade foi renomeada para .

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Mudanças no SearchServiceClient e SearchIndexClient
O tipo `Credentials` de propriedade `SearchCredentials` mudou de `ServiceClientCredentials`para a sua classe base, . Se precisar de `SearchCredentials` aceder `SearchIndexClient` ao `SearchServiceClient`de a `SearchCredentials` ou , por favor, use a nova propriedade.

Em versões mais antigas `SearchServiceClient` do `SearchIndexClient` SDK, e `HttpClient` tinha construtores que levavam um parâmetro. Estes foram substituídos por construtores `HttpClientHandler` que levam `DelegatingHandler` uma e uma variedade de objetos. Isto facilita a instalação de manipuladores personalizados para pré-processar pedidos HTTP, se necessário.

Finalmente, os construtores que `Uri` `SearchCredentials` tomaram um e mudaram. Por exemplo, se tiver um código que se pareça com este:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Pode mudá-lo para isto para corrigir eventuais erros de construção:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Note também que o tipo de parâmetro `ServiceClientCredentials`de credenciais mudou para . Isto é improvável que afete o seu código uma vez `SearchCredentials` que é derivado de `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Passar uma identificação de pedido
Em versões mais antigas do SDK, você `SearchServiceClient` `SearchIndexClient` poderia definir um ID de pedido no ou e seria incluído em todos os pedidos para a API REST. Isto é útil para problemas de resolução de problemas com o seu serviço de pesquisa se precisar de contactar o suporte. No entanto, é mais útil definir um ID de pedido único para cada operação em vez de usar o mesmo ID para todas as operações. Por esta razão, os `SetClientRequestId` métodos de `SearchServiceClient` e `SearchIndexClient` foram removidos. Em vez disso, pode passar uma identificação `SearchRequestOptions` de pedido para cada método de operação através do parâmetro opcional.

> [!NOTE]
> Num futuro lançamento do SDK, adicionaremos um novo mecanismo para definir um PEDIDO de ID globalmente sobre os objetos de cliente que é consistente com a abordagem usada por outros SDKs Azure.
> 
> 

### <a name="example"></a>Exemplo
Se tem um código que se parece com este:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Pode mudá-lo para isto para corrigir eventuais erros de construção:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Alterações no nome da interface
Os nomes de interface de grupo de operação mudaram todos para serem consistentes com os respetivos nomes de propriedade:

* O tipo `ISearchServiceClient.Indexes` de foi `IIndexOperations` renomeado de `IIndexesOperations`.
* O tipo `ISearchServiceClient.Indexers` de foi `IIndexerOperations` renomeado de `IIndexersOperations`.
* O tipo `ISearchServiceClient.DataSources` de foi `IDataSourceOperations` renomeado de `IDataSourcesOperations`.
* O tipo `ISearchIndexClient.Documents` de foi `IDocumentOperations` renomeado de `IDocumentsOperations`.

É pouco provável que esta alteração afete o seu código a menos que tenha criado simulações destas interfaces para efeitos de teste.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Correções de bugs na versão 1.1
Havia um bug em versões mais antigas do Azure Search .NET SDK relativo à serialização de classes de modelos personalizados. O bug pode ocorrer se criar uma classe modelo personalizada com uma propriedade de um tipo de valor não nula.

### <a name="steps-to-reproduce"></a>Passos para se reproduzir
Crie uma classe de modelo personalizada com uma propriedade de tipo de valor não nudível. Por exemplo, adicione `UnitCount` uma `int` propriedade `int?`pública de tipo em vez de .

Se indexar um documento com o valor predefinido `int`desse tipo (por exemplo, 0 para), o campo será nulo na Pesquisa Azure. Se posteriormente pesquisar esse documento, `Search` a `JsonSerializationException` chamada lançará queixando-se de que não pode converter-se `null` a `int`.

Além disso, os filtros podem não funcionar como esperado, uma vez que o nulo foi escrito ao índice em vez do valor pretendido.

### <a name="fix-details"></a>Corrigir detalhes
Corrigimos esta questão na versão 1.1 do SDK. Agora, se tiver uma classe modelo como esta:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e definiu `IntValue` para 0, esse valor é agora corretamente serializado como 0 no fio e armazenado como 0 no índice. Tropeçar redondos também funciona como esperado.

Há um problema potencial a ter em conta com esta abordagem: Se utilizar um tipo de modelo com uma propriedade não nulidade, tem de **garantir** que nenhum documento no seu índice contenha um valor nulo para o campo correspondente. Nem o SDK nem a API de Pesquisa Azure o ajudarão a impor isto.

Esta não é apenas uma preocupação hipotética: imagine um cenário onde adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esta razão, recomendamos ainda que utilize tipos nuníveis nas suas aulas de modelo como uma melhor prática.

Para mais detalhes sobre este bug e a correção, consulte [este problema no GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).


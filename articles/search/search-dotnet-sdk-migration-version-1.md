---
title: Upgrade para Azure Search .NET SDK versão 1.1
titleSuffix: Azure Cognitive Search
description: Migrar o código para a versão 1.1 da API de Pesquisa Azure .NET. Saiba o que há de novo e que mudanças de código são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4860918fc4f995ad267fc35b142d3fcb460ce177
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002832"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Upgrade para Azure Search .NET SDK versão 1.1

Se estiver a utilizar a versão 1.0.2 pré-visualização ou mais antiga do [Azure Search .NET SDK,](/dotnet/api/overview/azure/search)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 1.1.

Para uma passagem mais geral do SDK, incluindo exemplos, consulte [Como utilizar a Azure Search a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Uma vez atualizado para a versão 1.1, ou se já estiver a utilizar uma versão entre 1.1 e 2.0 inclusive, deverá atualizar para a versão 3. Consulte [a atualização para a versão 3 da Azure Search .NET SDK](search-dotnet-sdk-migration.md) para obter instruções.
>

Em primeiro lugar, atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a consola Do Gestor de Pacotes NuGet ou clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes nuget..." em Estúdio Visual.

Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto.

Se já usou a versão 1.0.0 pré-visualização, 1.0.1 pré-visualização ou 1.0.2 pré-visualização, a construção deve ter sucesso e está pronto para ir!

Se já usou a versão 0.13.0 ou mais antiga, deverá ver erros de construção como o seguinte:

```output
Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)
```

O próximo passo é corrigir os erros de construção um a um. A maioria requer alterações de nomes de classe e métodos que foram renomeados no SDK. [A lista de alterações de rutura na versão 1.1](#ListOfChangesV1) contém uma lista destas alterações de nome.

Se estiver a utilizar aulas personalizadas para modelar os seus documentos, e essas classes tiverem propriedades de tipos primitivos não nulos (por exemplo, `int` ou `bool` em C#), existe uma correção de bugs na versão 1.1 do SDK de que deve estar ciente. Consulte [as correções do Bug na versão 1.1](#BugFixesV1) para obter mais detalhes.

Finalmente, uma vez corrigido quaisquer erros de construção, pode escoar alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista de alterações de rutura na versão 1.1
A lista a seguir é ordenada pela probabilidade de a alteração afetar o seu código de aplicação.

### <a name="indexbatch-and-indexaction-changes"></a>Alterações de IndexBatch e IndexAction
`IndexBatch.Create` foi renomeado `IndexBatch.New` e já não tem `params` argumentos. Pode utilizar `IndexBatch.New` para lotes que misturam diferentes tipos de ações (fusões, eliminações, etc.). Além disso, existem novos métodos estáticos para a criação de lotes onde todas as ações são as mesmas: `Delete` `Merge` , , e `MergeOrUpload` `Upload` .

`IndexAction` já não tem construtores públicos e as suas propriedades são agora imutáveis. Deve utilizar os novos métodos estáticos para criar ações para diferentes finalidades: `Delete` `Merge` , e `MergeOrUpload` `Upload` . `IndexAction.Create` foi removido. Se utilizar a sobrecarga que leva apenas um documento, certifique-se de que `Upload` utiliza.

#### <a name="example"></a>Exemplo
Se o seu código se parece com isto:

```csharp
var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
indexClient.Documents.Index(batch);
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
indexClient.Documents.Index(batch);
```

Se quiser, pode simplificar ainda mais isto:

```csharp
var batch = IndexBatch.Upload(documents);
indexClient.Documents.Index(batch);
```

### <a name="indexbatchexception-changes"></a>Alterações no IndexBatchException
A `IndexBatchException.IndexResponse` propriedade foi renomeada `IndexingResults` para, e o seu tipo é agora `IList<IndexingResult>` .

#### <a name="example"></a>Exemplo
Se o seu código se parece com isto:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Alterações do método de operação
Cada operação no Azure Search .NET SDK é exposta como um conjunto de sobrecargas de método para chamadas sincronizadas e assíncronas. As assinaturas e o factoring destas sobrecargas de métodos mudaram na versão 1.1.

Por exemplo, a operação "Obter Estatísticas de Índice" em versões mais antigas do SDK expôs estas assinaturas:

Em `IIndexOperations`:

```csharp
// Asynchronous operation with all parameters
Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    string indexName,
    CancellationToken cancellationToken);
```

Em `IndexOperationsExtensions`:

```csharp
// Asynchronous operation with only required parameters
public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    this IIndexOperations operations,
    string indexName);

// Synchronous operation with only required parameters
public static IndexGetStatisticsResponse GetStatistics(
    this IIndexOperations operations,
    string indexName);
```

As assinaturas-método para a mesma operação na versão 1.1 são assim:

Em `IIndexesOperations`:

```csharp
// Asynchronous operation with lower-level HTTP features exposed
Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
    Dictionary<string, List<string>> customHeaders = null,
    CancellationToken cancellationToken = default(CancellationToken));
```

Em `IndexesOperationsExtensions`:

```csharp
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
```

Começando pela versão 1.1, o Azure Search .NET SDK organiza métodos de funcionamento de forma diferente:

* Os parâmetros opcionais são agora modelados como parâmetros padrão em vez de sobrecargas adicionais do método. Isto reduz o número de sobrecargas de métodos, às vezes dramaticamente.
* Os métodos de extensão agora escondem muitos dos detalhes extra-hediondos de HTTP do chamador. Por exemplo, versões mais antigas do SDK devolveram um objeto de resposta com um código de estado HTTP, que muitas vezes não precisava de verificar porque os métodos de funcionamento lançam `CloudException` para qualquer código de estado que indique um erro. Os novos métodos de extensão apenas devolvem objetos do modelo, poupando-lhe o trabalho de ter que desembrulhá-los no seu código.
* Inversamente, as interfaces centrais agora expõem métodos que lhe dão mais controlo ao nível HTTP se precisar. Agora pode passar em cabeçalhos HTTP personalizados para serem incluídos em pedidos, e o novo `AzureOperationResponse<T>` tipo de devolução dá-lhe acesso direto ao `HttpRequestMessage` e para a `HttpResponseMessage` operação. `AzureOperationResponse` é definido no `Microsoft.Rest.Azure` espaço de nome e substitui `Hyak.Common.OperationResponse` .

### <a name="scoringparameters-changes"></a>Alterações de Pontuação Parametros
Uma nova classe nomeada `ScoringParameter` foi adicionada no mais recente SDK para facilitar a disponibilização de parâmetros para marcar perfis numa consulta de pesquisa. Anteriormente, a `ScoringProfiles` propriedade da classe era `SearchParameters` dactilografada `IList<string>` como; Agora é dactilografado como `IList<ScoringParameter>` .

#### <a name="example"></a>Exemplo
Se o seu código se parece com isto:

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção: 

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters =
    new[]
    {
        new ScoringParameter("featuredParam", new[] { "featured" }),
        new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
    };
```

### <a name="model-class-changes"></a>Mudanças de classe de modelo
Devido às alterações de assinatura descritas nas [alterações do método de Operação,](#OperationMethodChanges)muitas classes no espaço de `Microsoft.Azure.Search.Models` nome foram renomeadas ou removidas. Por exemplo:

* `IndexDefinitionResponse` foi substituído por `AzureOperationResponse<Index>`
* `DocumentSearchResponse` mudou de nome para `DocumentSearchResult`
* `IndexResult` mudou de nome para `IndexingResult`
* `Documents.Count()` agora devolve um `long` com a contagem de documentos em vez de um `DocumentCountResponse`
* `IndexGetStatisticsResponse` mudou de nome para `IndexGetStatisticsResult`
* `IndexListResponse` mudou de nome para `IndexListResult`

Para resumir, `OperationResponse` foram removidas as classes derivadas que existiam apenas para embrulhar um objeto modelo. As restantes classes mudaram de sufixo `Response` de `Result` .

#### <a name="example"></a>Exemplo
Se o seu código se parece com isto:

```csharp
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
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
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
```

#### <a name="response-classes-and-ienumerable"></a>Aulas de resposta e IEnumerable
Uma alteração adicional que pode afetar o seu código é que as classes de resposta que detêm coleções já não implementam `IEnumerable<T>` . Em vez disso, pode aceder diretamente à propriedade da coleção. Por exemplo, se o seu código se parece com isto:

```csharp
DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response)
{
    Console.WriteLine(result.Document);
}
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response.Results)
{
    Console.WriteLine(result.Document);
}
```

#### <a name="special-case-for-web-applications"></a>Caso especial para aplicações web
Se tiver uma aplicação web que serialize `DocumentSearchResponse` diretamente para enviar resultados de pesquisa para o navegador, terá de alterar o seu código ou os resultados não serão serializáveis corretamente. Por exemplo, se o seu código se parece com isto:

```csharp
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
```

Pode alterá-la obtenda pela `.Results` obtenção da propriedade da resposta de pesquisa para corrigir a renderização do resultado da pesquisa:

```csharp
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
```

Você mesmo terá de procurar tais casos no seu código; **O compilador não o avisa** porque `JsonResult.Data` é do tipo `object` .

### <a name="cloudexception-changes"></a>Alterações cloudException
A `CloudException` classe passou do espaço para o espaço de `Hyak.Common` `Microsoft.Rest.Azure` nomes. Além disso, a sua `Error` propriedade foi renomeada `Body` para.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Alterações do SearchServiceClient e SearchIndexClient
O tipo de `Credentials` propriedade mudou de `SearchCredentials` classe base, `ServiceClientCredentials` . Se precisar de aceder ao `SearchCredentials` `SearchIndexClient` `SearchServiceClient` ou, por favor, use o novo `SearchCredentials` imóvel.

Em versões mais antigas do SDK, `SearchServiceClient` e `SearchIndexClient` tinha construtores que tomavam um `HttpClient` parâmetro. Estes foram substituídos por construtores que tomam `HttpClientHandler` um e um conjunto de `DelegatingHandler` objetos. Isto facilita a instalação de manipuladores personalizados para pré-processar pedidos HTTP, se necessário.

Finalmente, os construtores que tomaram um `Uri` e `SearchCredentials` mudaram. Por exemplo, se tiver um código que se pareça com este:

```csharp
var client =
    new SearchServiceClient(
        new SearchCredentials("abc123"),
        new Uri("http://myservice.search.windows.net"));
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
var client =
    new SearchServiceClient(
        new Uri("http://myservice.search.windows.net"),
        new SearchCredentials("abc123"));
```

Note também que o tipo de parâmetro de credenciais mudou para `ServiceClientCredentials` . É pouco provável que isto afete o seu código, uma vez `SearchCredentials` que é derivado de `ServiceClientCredentials` .

### <a name="passing-a-request-id"></a>Passando uma ID de pedido
Em versões mais antigas do SDK, pode definir um ID de pedido no `SearchServiceClient` ou e seria incluído em todos os `SearchIndexClient` pedidos à API REST. Isto é útil para resolver problemas com o seu serviço de pesquisa se precisar de contactar suporte. No entanto, é mais útil definir um ID de pedido único para cada operação do que usar o mesmo ID para todas as operações. Por esta razão, os `SetClientRequestId` métodos de `SearchServiceClient` e foram `SearchIndexClient` removidos. Em vez disso, pode passar um ID de pedido a cada método de operação através do `SearchRequestOptions` parâmetro opcional.

> [!NOTE]
> Numa futura versão do SDK, adicionaremos um novo mecanismo para definir um ID de pedido globalmente nos objetos do cliente que é consistente com a abordagem usada por outros SDKs Azure.
> 
> 

### <a name="example"></a>Exemplo
Se tiver um código que se pareça com este:

```csharp
client.SetClientRequestId(Guid.NewGuid());
...
long count = client.Documents.Count();
```

Pode alterá-lo para isto para corrigir quaisquer erros de construção:

```csharp
long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));
```

### <a name="interface-name-changes"></a>Alterações no nome da interface
Os nomes da interface do grupo de operação foram alterados para serem consistentes com os respetivos nomes de propriedade:

* O tipo de `ISearchServiceClient.Indexes` foi renomeado `IIndexOperations` `IIndexesOperations` de.
* O tipo de `ISearchServiceClient.Indexers` foi renomeado `IIndexerOperations` `IIndexersOperations` de.
* O tipo de `ISearchServiceClient.DataSources` foi renomeado `IDataSourceOperations` `IDataSourcesOperations` de.
* O tipo de `ISearchIndexClient.Documents` foi renomeado `IDocumentOperations` `IDocumentsOperations` de.

Esta alteração é improvável que afete o seu código a menos que tenha criado simulações destas interfaces para fins de teste.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Correções de bugs na versão 1.1
Houve um bug em versões mais antigas do Azure Search .NET SDK relativo à serialização de classes de modelos personalizados. O bug pode ocorrer se criar uma classe modelo personalizada com uma propriedade de um tipo de valor não anulado.

### <a name="steps-to-reproduce"></a>Passos para reproduzir
Crie uma classe modelo personalizada com uma propriedade de valor não anulado. Por exemplo, adicione uma propriedade pública `UnitCount` do tipo em vez de `int` `int?` .

Se indexar um documento com o valor predefinido desse tipo (por exemplo, 0 `int` para), o campo será nulo na Pesquisa de Azure. Se posteriormente pesquisar esse documento, a `Search` chamada irá lançar `JsonSerializationException` reclamando que não pode converter para `null` `int` .

Além disso, os filtros podem não funcionar como esperado, uma vez que o nulo foi escrito ao índice em vez do valor pretendido.

### <a name="fix-details"></a>Corrigir detalhes
Corrigimos esta questão na versão 1.1 do SDK. Agora, se tiver uma classe modelo como esta:

```csharp
public class Model
{
    public string Key { get; set; }

    public int IntValue { get; set; }
}
```

e definiu `IntValue` para 0, esse valor é agora corretamente serializado como 0 no fio e armazenado como 0 no índice. Tropeçar em volta também funciona como esperado.

Há um problema potencial a ter em conta com esta abordagem: Se utilizar um tipo de modelo com uma propriedade não a nula, tem de **garantir** que nenhum documento no seu índice contenha um valor nulo para o campo correspondente. Nem o SDK nem a Azure Search REST API o ajudarão a impor isto.

Esta não é apenas uma preocupação hipotética: imagine um cenário onde adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Por esta razão, recomendamos ainda que utilize tipos nulos nas suas aulas de modelo como uma boa prática.

Para mais detalhes sobre este bug e a correção, consulte [este problema no GitHub.](https://github.com/Azure/azure-sdk-for-net/issues/1063)
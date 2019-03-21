---
title: Consultar dados a um índice da Azure Search no C# (.NET SDK) - Azure Search
description: C#exemplo de código para a criação de uma consulta de pesquisa no Azure Search. Adicione parâmetros de pesquisa para filtrar e ordenar os resultados da pesquisa.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 6bb170a5f3353288ab9c393e01b7a0902361913b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287014"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Início rápido: 3 - consultar um índice da Azure Search noC#

Este artigo mostra-lhe como consultar [um índice da Azure Search](search-what-is-an-index.md) com C# e o [SDK de .NET](https://aka.ms/search-sdk). Pesquisar documentos no seu índice é conseguido através da execução dessas tarefas:

> [!div class="checklist"]
> * Criar uma [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objeto para ligar a um índice de pesquisa com direitos somente leitura.
> * Criar uma [ `SearchParameters` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) objeto que contém a definição do filtro ou pesquisa.
> * Chamar o `Documents.Search` método no `SearchIndexClient` para enviar consultas para um índice.

## <a name="prerequisites"></a>Pré-requisitos

[Carregar um índice da Azure Search](search-import-data-dotnet.md) com os dados de exemplo hotéis.

Obter uma chave de consulta utilizada para acesso só de leitura aos documentos. Até agora, utilizou uma chave de API de administração para que possa criar objetos e conteúdo num serviço de pesquisa. Mas, para obter suporte de consulta em aplicações, recomendamos vivamente utilizar uma chave de consulta. Para obter instruções, consulte [criar uma chave de consulta](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Criar um cliente
Criar uma instância do `SearchIndexClient` classe, para que pode fornecer uma chave de consulta para acesso só de leitura (em vez dos direitos de acesso de escrita conferidos após o `SearchServiceClient` utilizado na lição anterior).

Esta classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa, o nome do índice e um [ `SearchCredentials` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) objeto como parâmetros. `SearchCredentials` molda a sua chave de API.

O código abaixo cria uma nova `SearchIndexClient` para o índice "Hotéis" com valores para o nome de serviço de pesquisa e a chave de api que são armazenadas no ficheiro de configuração da aplicação (`appsettings.json` no caso da [aplicação de exemplo](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` tem um [ `Documents` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) propriedade. Esta propriedade fornece todos os métodos de que necessita para consultar os índices de Pesquisa do Azure.

## <a name="construct-searchparameters"></a>Construa SearchParameters
A pesquisa com o SDK .NET é tão simples como chamar o método `Documents.Search` no seu `SearchIndexClient`. Este método aceita alguns parâmetros, incluindo o texto de pesquisa, juntamente com um objeto `SearchParameters` que podem ser utilizados para aperfeiçoar ainda mais a consulta.

### <a name="types-of-queries"></a>Tipos de consultas
Os dois principais [tipos de consulta](search-query-overview.md#types-of-queries) que irá utilizar são `search` e `filter`. Uma consulta `search` pesquisa um ou mais termos em todos os campos *pesquisáveis* no seu índice. Uma consulta `filter` avalia uma expressão booleana através de todos os campos *filtráveis* num índice. Pode utilizar as pesquisas e os filtros em conjunto ou separadamente.

As pesquisas e os filtros são efetuados utilizando o método `Documents.Search`. Uma consulta de pesquisa pode ser transmitida no parâmetro `searchText`, enquanto uma expressão de filtro pode ser transmitida na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText` . Para pesquisar sem filtrar, basta deixar a propriedade `Filter` por definir ou não transmitir numa instância `SearchParameters` de todo.

### <a name="example-queries"></a>Consultas de exemplo
O código de exemplo seguinte mostra algumas formas diferentes de consultar o índice "Hotéis" definido no [criar um índice da Azure Search no C# ](search-create-index-dotnet.md#DefineIndex). Tenha em atenção que os documentos devolvidos com os resultados da pesquisa são instâncias do `Hotel` classe, que foi definida em [importar dados para um índice da Azure Search no C# ](search-import-data-dotnet.md#construct-indexbatch). O código de exemplo utiliza um método `WriteDocuments` para enviar os resultados da pesquisa para a consola. Este método é descrito na secção seguinte.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Processar os resultados da pesquisa
O método `Documents.Search` devolve um objeto `DocumentSearchResult` que contém os resultados da consulta. O exemplo na secção anterior utilizou um método denominado `WriteDocuments` para enviar os resultados da pesquisa para a consola:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Eis o que o aspeto dos resultados das consultas na secção anterior, partindo do princípio de que o índice "Hotéis" é preenchido com os dados de exemplo:

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

O código de exemplo acima utiliza a consola para produzir os resultados da pesquisa. Da mesma forma, terá de apresentar os resultados da pesquisa na sua própria aplicação. Para obter um exemplo de como apresentar resultados de pesquisa numa aplicação web com base no ASP.NET MVC, consulte a [DotNetSample projeto](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) no GitHub.

## <a name="next-steps"></a>Passos Seguintes

Se ainda não fez isso, reveja o código de exemplo [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) no GitHub, juntamente com [como utilizar o Azure Search a partir de um aplicativo .NET ](search-howto-dotnet-sdk.md) para obter mais descrições de códigos de exemplo. 
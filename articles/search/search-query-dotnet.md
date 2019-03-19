---
title: Consultar um índice no código utilizando o SDK de .NET - Azure Search
description: C#exemplo de código para a criação de uma consulta de pesquisa no Azure Search. Adicione parâmetros de pesquisa para filtrar e ordenar os resultados da pesquisa.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/19/2017
ms.custom: seodec2018
ms.openlocfilehash: ecd4c7e862db43dc01685af313d618a38a688d5b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58138258"
---
# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Consultar o índice de Pesquisa do Azure utilizando o SDK .NET
> [!div class="op_single_selector"]
> * [Descrição geral](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Este artigo mostra como consultar um índice através do [SDK .NET de Pesquisa do Azure](https://aka.ms/search-sdk).

Antes de iniciar estas instruções, já deverá ter [criado um índice de Pesquisa do Azure](search-what-is-an-index.md) e [preenchido este com dados](search-what-is-data-import.md).

> [!NOTE]
> Todo o código de exemplo neste artigo está escrito em C#. Pode localizar o código de origem completo [no GitHub](https://aka.ms/search-dotnet-howto). Também pode ler sobre o [Azure Search .NET SDK (SDK .NET do Azure Search)](search-howto-dotnet-sdk.md) para um percurso mais detalhado através do código de exemplo.

## <a name="identify-your-azure-search-services-query-api-key"></a>Identificar a sua chave de API de consulta do serviço Azure Search
Agora que criou um índice de Pesquisa do Azure, está quase pronto para emitir consultas utilizando o SDK .NET. Em primeiro lugar, precisa de obter uma das chaves de API de consulta que foi gerada para o serviço de pesquisa que aprovisionou. O SDK .NET irá enviar esta chave de API em cada pedido para o seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

1. Para localizar as chaves de API do seu serviço, pode iniciar sessão no [portal do Azure](https://portal.azure.com/)
2. Aceda ao painel do seu serviço Azure Search
3. Clique no ícone "Chaves"

O seu serviço terá *chaves de administração* e *chaves de consulta*.

* As suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade para gerir o serviço, criar e eliminar índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária caso opte por voltar a gerar a chave principal e vice-versa.
* As suas *chaves de consulta* concedem acesso só de leitura para índices e documentos e, normalmente, são distribuídas por aplicações cliente que emitem pedidos de pesquisa.

Para efeitos de consulta de um índice, pode utilizar uma das chaves de consulta. As chaves de administração também podem ser utilizadas para consultas, contudo, deve utilizar uma chave de consulta no código da aplicação, uma vez que tal segue melhor o [Princípio de menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Criar uma instância da classe SearchIndexClient
Para começar a emitir consultas com o SDK .NET de Pesquisa do Azure, terá de criar uma instância da classe `SearchIndexClient`. Esta classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa, o nome do índice e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` molda a sua chave de API.

O código abaixo cria uma nova `SearchIndexClient` para o índice "hotéis" (criado em [Criar um índice de Pesquisa do Azure com o SDK .NET](search-create-index-dotnet.md)) ao utilizar valores para o nome do serviço de pesquisa e a chave de API que são armazenados num ficheiro de configuração da aplicação (`appsettings.json` no caso do [exemplo de aplicação](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` tem uma propriedade `Documents`. Esta propriedade fornece todos os métodos de que necessita para consultar os índices de Pesquisa do Azure.

## <a name="query-your-index"></a>Consultar o índice
A pesquisa com o SDK .NET é tão simples como chamar o método `Documents.Search` no seu `SearchIndexClient`. Este método aceita alguns parâmetros, incluindo o texto de pesquisa, juntamente com um objeto `SearchParameters` que podem ser utilizados para aperfeiçoar ainda mais a consulta.

#### <a name="types-of-queries"></a>Tipos de consultas
Os dois principais [tipos de consulta](search-query-overview.md#types-of-queries) que irá utilizar são `search` e `filter`. Uma consulta `search` pesquisa um ou mais termos em todos os campos *pesquisáveis* no seu índice. Uma consulta `filter` avalia uma expressão booleana através de todos os campos *filtráveis* num índice. Pode utilizar as pesquisas e os filtros em conjunto ou separadamente.

As pesquisas e os filtros são efetuados utilizando o método `Documents.Search`. Uma consulta de pesquisa pode ser transmitida no parâmetro `searchText`, enquanto uma expressão de filtro pode ser transmitida na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText` . Para pesquisar sem filtrar, basta deixar a propriedade `Filter` por definir ou não transmitir numa instância `SearchParameters` de todo.

#### <a name="example-queries"></a>Consultas de exemplo
O seguinte código de exemplo mostra algumas formas diferentes de consultar o índice "hotéis" definido em [Criar um índice de Pesquisa do Azure utilizando o SDK .NET](search-create-index-dotnet.md#DefineIndex). Tenha em atenção que os documentos devolvidos com os resultados da pesquisa são instâncias da classe `Hotel`, que foi definida em [Importação de dados na Pesquisa do Azure utilizando o SDK .NET](search-import-data-dotnet.md#HotelClass). O código de exemplo utiliza um método `WriteDocuments` para enviar os resultados da pesquisa para a consola. Este método é descrito na secção seguinte.

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

Eis o aspeto dos resultados das consultas na secção anterior, partindo do princípio de que o índice "hotéis" é preenchido com os dados de exemplo em [Importação de Dados na Pesquisa do Azure utilizando o SDK .NET](search-import-data-dotnet.md):

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

O código de exemplo acima utiliza a consola para produzir os resultados da pesquisa. Da mesma forma, terá de apresentar os resultados da pesquisa na sua própria aplicação. Consulte [este exemplo no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) para obter um exemplo de como apresentar resultados de pesquisa numa aplicação Web do ASP.NET baseada em MVC.


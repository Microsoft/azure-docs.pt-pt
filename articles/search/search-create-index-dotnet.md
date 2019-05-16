---
title: 'Início rápido: Criar um índice numa C# consola de aplicação - Azure Search'
description: Saiba como criar um índice de texto completo pesquisável no C# utilizando o SDK de .NET de pesquisa do Azure.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/16/2019
ms.openlocfilehash: 8d186ae83e1016de9c4548d4b1c39303025a5270
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65758464"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Início rápido: 1 - criar um índice da Azure Search noC#
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-fiddler.md)
>*

Este artigo explica o processo de criação [um índice da Azure Search](search-what-is-an-index.md) com C# e o [SDK de .NET](https://aka.ms/search-sdk). Esta é a primeira lição um exercício de 3 partes para criar, carregamento e consulta um índice. Criação de índices é conseguida através da execução dessas tarefas:

> [!div class="checklist"]
> * Criar uma [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) objeto para ligar a um serviço de pesquisa.
> * Criar uma [ `Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) objeto passar como um parâmetro para `Indexes.Create`.
> * Chamar o `Indexes.Create` método no `SearchServiceClient` para enviar o `Index` a um serviço.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste início rápido. 

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este início rápido.

+ [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição. Código de exemplo e instruções foram testadas na edição de Comunidade gratuita.

+ [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) fornece a solução de exemplo, uma aplicação de consola .NET Core, escrita em C#, localizado no repositório do GitHub de exemplos do Azure. Baixe e extraia a solução. Por predefinição, as soluções são só de leitura. Com o botão direito a solução e desmarque o atributo só de leitura, de modo que pode modificar os arquivos. Dados estão incluídos na solução.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

Chamadas para o serviço requerem um ponto de final do URL e uma chave de acesso em cada solicitação. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="1---configure-and-build"></a>1 - configurar e criar

1. Abra o **DotNetHowTo.sln** ficheiro no Visual Studio.

1. No appSettings, substitua o conteúdo com o exemplo abaixo do padrão e, em seguida, forneça o nome de serviço e o administrador a chave de api para o seu serviço. 


   ```json
   {
       "SearchServiceName": "Put your search service name here (not the full URL)",
       "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
    }
   ```

  Para o nome de serviço, precisa apenas o nome próprio. Por exemplo, se o seu URL for https://mydemo.search.windows.net, adicione `mydemo` para o ficheiro JSON.

1. Prima F5 para compilar a solução e executar a aplicação de consola. Os passos restantes neste exercício e aqueles que se seguem são uma explicação de como esse código funciona. 

Em alternativa, pode consultar [como utilizar o Azure Search a partir de um aplicativo .NET](search-howto-dotnet-sdk.md) para obter mais cobertura dos comportamentos SDK. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - criar um cliente

Para começar a utilizar o SDK .NET da Azure Search, crie uma instância do `SearchServiceClient` classe. Esta classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` molda a sua chave de API.

O código a seguir pode ser encontrado no ficheiro Program.cs. Ele cria um novo `SearchServiceClient` utilizando valores para o nome de serviço de pesquisa e a chave de api que são armazenadas no ficheiro de configuração do aplicativo (appSettings).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` tem uma propriedade `Indexes`. Esta propriedade fornece todos os métodos de que necessita para criar, listar, atualizar ou eliminar os índices da Azure Search.

> [!NOTE]
> A classe `SearchServiceClient` gere ligações com o seu serviço de pesquisa. Se possível, para evitar abrir demasiadas ligações, tente partilhar uma única instância de `SearchServiceClient` na sua aplicação. Os seus métodos são seguros para threads de forma a permitir esta partilha.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - criar índice
Uma única chamada para o `Indexes.Create` método cria um índice. Este método aceita como parâmetro um `Index` objeto que define o índice da Azure Search. Criar um `Index` de objeto e inicializá-lo da seguinte forma:

1. Defina a propriedade `Name` do objeto `Index` para o nome do seu índice.

2. Defina a propriedade `Fields` do objeto `Index` para uma matriz de objetos `Field`. A forma mais fácil para criar os objetos `Field` é com o método `FieldBuilder.BuildForType`, transmitindo uma classe de modelo para o parâmetro de tipo. Uma classe de modelo tem propriedades que mapeiam os campos do seu índice. Isto permite-lhe vincular documentos a partir do índice de pesquisa para instâncias da sua classe de modelo.

> [!NOTE]
> Se não pretender utilizar uma classe de modelo, ainda pode definir o índice ao criar `Field` objetos diretamente. Pode fornecer o nome do campo ao construtor, juntamente com o tipo de dados (ou analisador para os campos de cadeia). Também pode definir outras propriedades, como `IsSearchable`, `IsFilterable`, para citar alguns.
>
>

É importante ter suas necessidades de negócio e experiência de utilizador do pesquisa em mente ao criar o seu índice. Cada campo tem de ser atribuído a [atributos](https://docs.microsoft.com/rest/api/searchservice/Create-Index) que estabelecem quais as funcionalidades (filtragem, facetamento, ordenação e assim por diante) de pesquisa aplicáveis a cada campo. Por cada propriedade que não seja definida de forma explicita, a classe `Field` predefine como desativada a funcionalidade de pesquisa correspondente, a menos que a ative especificamente.

Neste exemplo, o nome de índice é "Hotéis" e campos são definidos usando uma classe de modelo. Cada propriedade da classe do modelo tem atributos que determinam os comportamentos relacionados com a pesquisa do campo de índice correspondente. A classe de modelo é definida da seguinte forma:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Escolhemos cuidadosamente os atributos para cada propriedade com base na forma como acreditamos que serão utilizados numa aplicação. Por exemplo, é provável que as pessoas que procuram hotéis estejam interessadas em correspondências de palavras-chave no campo `description`, desta forma, ativamos a pesquisa em texto completo para esse campo através da adição do atributo `IsSearchable` à propriedade `Description`.

Tenha em atenção que exatamente um campo no seu índice do tipo `string` deve ser designado como o campo *chave* adicionando o atributo `Key` (consulte `HotelId` no exemplo acima).

A definição de índice acima utiliza um analisador de idioma para o campo `description_fr` porque destina-se ao armazenamento de texto em francês. Para obter mais informações, consulte [adicionar analisadores de idiomas para um índice da Azure Search](index-add-language-analyzers.md).

> [!NOTE]
> Por predefinição, o nome de cada propriedade na classe do modelo corresponde ao nome do campo no índice. Se pretender mapear todos os nomes de propriedade para os nomes dos campos camel-case, marque a classe com o atributo `SerializePropertyNamesAsCamelCase`. Se pretender mapear para um nome diferente, pode utilizar o atributo `JsonProperty` como a propriedade `DescriptionFr` acima. O atributo `JsonProperty` tem precedência sobre o atributo `SerializePropertyNamesAsCamelCase`.
> 
> 

Agora que definimos uma classe de modelo, podemos criar uma definição de índice muito facilmente:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - chamada Indexes.Create
Agora que tem um inicializado `Index` objeto, criar o índice ao chamar `Indexes.Create` no seu `SearchServiceClient` objeto:

```csharp
serviceClient.Indexes.Create(definition);
```

Para um pedido com êxito, o método regressará de forma normal. Se existir um problema com o pedido, tal como um parâmetro inválido, o método irá gerar `CloudException`.

Quando terminar com um índice e pretender eliminá-lo, chame o `Indexes.Delete` método no seu `SearchServiceClient`. Por exemplo:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos síncronos do SDK .NET da Azure Search por uma questão de simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, nos exemplos acima pode utilizar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, criou um índice da Azure Search vazio com base num esquema que define os tipos de dados de campo e comportamentos. O índice é um índice de "básica" constituída por um nome e uma coleção de campos atribuídas. Um índice mais realista seria incluir outros elementos, como [perfis de classificação](index-add-scoring-profiles.md), [sugestores](index-add-suggesters.md) para obter suporte, typeahead [sinónimos](search-synonyms.md)e, possivelmente, [ analisadores personalizados](index-add-custom-analyzers.md). Recomendamos que reveja estas capacidades depois de compreender o fluxo de trabalho básico.

O próximo início rápido nesta série aborda como carregar o índice com conteúdo pesquisável.

> [!div class="nextstepaction"]
> [Carregar dados para um índice de pesquisa do Azure com oC#](search-import-data-dotnet.md)
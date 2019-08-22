---
title: Filtros de faceta para navegação de pesquisa em aplicativos-Azure Search
description: Filtrar critérios por identidade de segurança do usuário, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem hospedado no Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a2fe29cf1d7c183aa62e6b86a4b29479d1f34ff8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649868"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Como criar um filtro de faceta no Azure Search 

A navegação facetada é usada para a filtragem autodirigida nos resultados da consulta em um aplicativo de pesquisa, em que seu aplicativo oferece controles de interface do usuário para a pesquisa de escopo para grupos de documentos (por exemplo, categorias ou marcas) e Azure Search fornece a estrutura de dados para trás do ocorrer. Neste artigo, examine rapidamente as etapas básicas para criar uma estrutura de navegação facetada que faça backup da experiência de pesquisa que você deseja fornecer. 

> [!div class="checklist"]
> * Escolher campos para filtragem e faceta
> * Definir atributos no campo
> * Compilar o índice e carregar dados
> * Adicionar filtros de faceta a uma consulta
> * Processar resultados

As facetas são dinâmicas e retornadas em uma consulta. As respostas de pesquisa trazem a elas as categorias de faceta usadas para navegar nos resultados. Se você não estiver familiarizado com facetas, o exemplo a seguir é uma ilustração de uma estrutura de navegação de faceta.

  ![](./media/search-filters-facets/facet-nav.png)

Novo para navegação facetada e deseja obter mais detalhes? Veja [como implementar a navegação facetada no Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Escolher campos

As facetas podem ser calculadas sobre campos de valor único, bem como coleções. Os campos que funcionam melhor na navegação facetada têm baixa cardinalidade: um pequeno número de valores distintos que se repetem em documentos em seu corpus de pesquisa (por exemplo, uma lista de cores, países/regiões ou nomes de marca). 

A facetação é habilitada em uma base de campo por campo quando você cria o índice definindo o `facetable` atributo como `true`. Em geral, você também deve `filterable` definir o `true` atributo como para esses campos para que o aplicativo de pesquisa possa filtrar esses campos com base em facetas que o usuário final seleciona. 

Ao criar um índice usando a API REST, qualquer [tipo de campo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) que possivelmente possa ser usado na navegação facetada é marcado `facetable` como por padrão:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Tipos de campo numéricos `Edm.Int64`: `Edm.Int32`,,`Edm.Double`
+ Coleções dos tipos acima (por exemplo, `Collection(Edm.String)` ou) `Collection(Edm.Double)`

Você não pode `Edm.GeographyPoint` usar `Collection(Edm.GeographyPoint)` os campos ou na navegação facetada. As facetas funcionam melhor em campos com baixa cardinalidade. Devido à resolução de coordenadas geográficas, é raro que quaisquer dois conjuntos de coestações sejam iguais em um determinado conjunto de dados. Dessa forma, as facetas não têm suporte para coordenadas geográficas. Você precisaria de um campo de cidade ou região para facetar por local.

## <a name="set-attributes"></a>Definir atributos

Atributos de índice que controlam como um campo é usado são adicionados a definições de campo individuais no índice. No exemplo a seguir, os campos com baixa cardinalidade, úteis para facetar, consistem em `category` : (Hotel, motel, Hostel) `tags`, e `rating`. Esses campos têm os `filterable` atributos `facetable` e definidos explicitamente no exemplo a seguir para fins ilustrativos. 

> [!Tip]
> Como prática recomendada para otimização de desempenho e armazenamento, desative a faceta para campos que nunca devem ser usados como uma faceta. Em particular, os campos de cadeia de caracteres para valores exclusivos, como uma ID ou nome de produto, `"facetable": false` devem ser definidos como para evitar seu uso acidental (e ineficaz) na navegação facetada.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Essa definição de índice é copiada de [criar um índice de Azure Search usando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). É idêntico, exceto pelas diferenças superficial nas definições de campo. Os `filterable` `facetable` `tags` `parkingIncluded`atributos e são explicitamente adicionados aos campos `smokingAllowed` ,,`rating` , e. `category` Na prática, `filterable` e `facetable` seria habilitado por padrão nesses campos ao usar a API REST. Ao usar o SDK do .NET, esses atributos devem ser habilitados explicitamente.

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) antes de formular uma consulta. Mencionamos esta etapa aqui para fins de integridade. Uma maneira de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicionar filtros de faceta a uma consulta

No código do aplicativo, construa uma consulta que especifica todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de Pontuação – qualquer coisa usada para formular uma solicitação. O exemplo a seguir cria uma solicitação que cria a navegação da faceta com base no tipo de acomodação, classificação e outros comodidades.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Retornar resultados filtrados em eventos de clique

Quando o usuário final clica em um valor de faceta, o manipulador para o evento de clique deve usar uma expressão de filtro para perceber a intenção do usuário. Dada uma `category` faceta, clicar na categoria "Motel" é implementada com `$filter` uma expressão que seleciona acomodações desse tipo. Quando um usuário clica em "Motel" para indicar que apenas motéis deve ser mostrado, a próxima consulta que o aplicativo `$filter=category eq 'motel'`envia inclui.

O trecho de código a seguir adicionará a categoria ao filtro se um usuário selecionar um valor da faceta de categoria.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Se o usuário clicar em um valor de faceta para um campo de `tags`coleção como, por exemplo, o valor "pool", seu aplicativo deverá usar a seguinte sintaxe de filtro:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Dicas e soluções alternativas

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializar uma página com facetas em vigor

Se você quiser inicializar uma página com facetas em vigor, poderá enviar uma consulta como parte da inicialização da página para propagar a página com uma estrutura de faceta inicial.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Preservar uma estrutura de navegação de faceta de forma assíncrona de resultados filtrados

Um dos desafios da navegação de faceta no Azure Search é que as facetas existem apenas para os resultados atuais. Na prática, é comum manter um conjunto estático de facetas para que o usuário possa navegar na ordem inversa, redirecionando etapas para explorar caminhos alternativos por meio de conteúdo de pesquisa. 

Embora esse seja um caso de uso comum, não é algo que a estrutura de navegação da faceta atualmente fornece prontamente. Os desenvolvedores que desejam facetas estáticas normalmente configuram a limitação emitindo duas consultas filtradas: um com escopo para os resultados, o outro usado para criar uma lista estática de facetas para fins de navegação.

## <a name="see-also"></a>Consulte também

+ [Filtros no Azure Search](search-filters.md)
+ [Criar API REST de índice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Pesquisar API REST de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)

---
title: Filtros facet para navegação de pesquisa em apps
titleSuffix: Azure Cognitive Search
description: Filtrar critérios por identidade de segurança do utilizador, geolocalização ou valores numéricos para reduzir os resultados de pesquisa em consultas em Azure Cognitive Search, um serviço de pesquisa em nuvem hospedado no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9360fc000e01e1c52561cbaa3e2f2968e67e2fa2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91740875"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Como construir um filtro de faceta na Pesquisa Cognitiva Azure 

A navegação frontal é utilizada para filtragem auto-direcionada em resultados de consulta numa aplicação de pesquisa, onde a sua aplicação oferece controlos de UI para pesquisar para pesquisar grupos de documentos (por exemplo, categorias ou marcas), e a Azure Cognitive Search fornece a estrutura de dados para apoiar a experiência. Neste artigo, reveja rapidamente os passos básicos para criar uma estrutura de navegação facetada apoiando a experiência de pesquisa que pretende proporcionar. 

> [!div class="checklist"]
> * Escolha campos para filtragem e faceta
> * Definir atributos no campo
> * Construa o índice e carregue os dados
> * Adicione filtros de faceta a uma consulta
> * Processar resultados

As facetas são dinâmicas e devolvidas numa consulta. As respostas de pesquisa trazem consigo as categorias de facetas utilizadas para navegar nos resultados. Se não está familiarizado com as facetas, o exemplo a seguir é uma ilustração de uma estrutura de navegação faceta.

:::image type="complex" source="media/search-filters-facets/facet-nav.png" alt-text="resultados de pesquisa filtrados&quot;:::
&quot;Uma imagem mostrando um diálogo de pesquisa com resultados de pesquisa filtrados agrupados por título de negócio. Uma seta indica que os resultados são facetas que são exibidas numa estrutura de navegação faceta" :::image-end:::

Nova navegação virada para a frente e quer mais detalhes? Veja [como implementar a navegação facetada na Pesquisa Cognitiva Azure.](search-faceted-navigation.md)

## <a name="choose-fields"></a>Escolha campos

As facetas podem ser calculadas sobre campos de valor único, bem como coleções. Os campos que funcionam melhor na navegação facetada têm baixa cardinalidade: um pequeno número de valores distintos que se repetem ao longo de documentos no seu corpus de pesquisa (por exemplo, uma lista de cores, países/regiões ou nomes de marcas). 

A faceta é ativada numa base campo por campo quando cria o índice, definindo o `facetable` atributo para `true` . Em geral, deve também definir o `filterable` atributo para esses campos para que a sua `true` aplicação de pesquisa possa filtrar nesses campos com base em facetas que o utilizador final seleciona. 

Ao criar um índice utilizando a API REST, qualquer [tipo de campo](/rest/api/searchservice/supported-data-types) que possa ser utilizado na navegação facetada é marcado como por `facetable` padrão:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Tipos de campo numérico: `Edm.Int32` `Edm.Int64` , `Edm.Double`
+ Coleções dos tipos acima (por exemplo, `Collection(Edm.String)` `Collection(Edm.Double)` ou)

Não é possível utilizar `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)` campos na navegação com face. As facetas funcionam melhor em campos com baixa cardinalidade. Devido à resolução de geo-coordenadas, é raro que quaisquer dois conjuntos de coordenadas sejam iguais num determinado conjunto de dados. Como tal, as facetas não são suportadas para geo-coordenadas. Precisaria de um campo de cidade ou região para enfrentar por local.

## <a name="set-attributes"></a>Definir atributos

Os atributos de índice que controlam a forma como um campo é utilizado são adicionados às definições individuais de campo no índice. No exemplo seguinte, os campos com baixa cardinalidade, úteis para a faceta, consistem em: `category` (hotel, motel, hostel), `tags` e `rating` . Estes campos têm os `filterable` `facetable` atributos e atributos explicitamente definidos no seguinte exemplo para fins ilustrativos. 

> [!Tip]
> Como uma melhor prática para a otimização de desempenho e armazenamento, vire-se para campos que nunca devem ser usados como faceta. Em particular, os campos de cordas para valores únicos, como um ID ou nome do produto, devem ser definidos `"facetable": false` para evitar a sua utilização acidental (e ineficaz) na navegação facetada.


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
> Esta definição de índice é copiada a partir de [Criar um índice de pesquisa cognitiva Azure utilizando a API REST](./search-get-started-powershell.md). É idêntico, exceto para diferenças superficiais nas definições de campo. Os `filterable` `facetable` atributos e atributos são explicitamente adicionados em `category` , , , e `tags` `parkingIncluded` `smokingAllowed` `rating` campos. Na prática, `filterable` e `facetable` seria ativado por padrão nestes campos quando utilizar a API REST. Ao utilizar o .NET SDK, estes atributos devem ser ativados explicitamente.

## <a name="build-and-load-an-index"></a>Construa e carregue um índice

Um passo intermédio (e talvez óbvio) é que é preciso [construir e preencher o índice](./search-get-started-dotnet.md#1---create-an-index) antes de formular uma consulta. Mencionamos este passo aqui para a completude. Uma forma de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicione filtros de faceta a uma consulta

No código de aplicação, construa uma consulta que especifique todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação, qualquer coisa usada para formular um pedido. O exemplo a seguir constrói um pedido que cria navegação faceta com base no tipo de alojamento, classificação e outras comodidades.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Devolva os resultados filtrados em eventos de clique

Quando o utilizador final clica num valor faceta, o manipulador do evento de clique deve usar uma expressão de filtro para perceber a intenção do utilizador. Dada a `category` faceta, clicar na categoria "motel" é implementado com uma `$filter` expressão que seleciona acomodações desse tipo. Quando um utilizador clica em "motel" para indicar que apenas motéis devem ser mostrados, a próxima consulta que a aplicação envia inclui `$filter=category eq 'motel'` .

O seguinte corte de código adiciona categoria ao filtro se um utilizador selecionar um valor da faceta da categoria.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Se o utilizador clicar num valor faceta para um campo de recolha `tags` como, por exemplo, o valor "pool", a sua aplicação deve utilizar a seguinte sintaxe de filtro: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Dicas e soluções alternativas

### <a name="initialize-a-page-with-facets-in-place"></a>Inicialize uma página com facetas no lugar

Se pretender rubricar uma página com as facetas no lugar, pode enviar uma consulta como parte da inicialização da página para semear a página com uma estrutura de faceta inicial.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Preservar uma estrutura de navegação faceta assíncronea dos resultados filtrados

Um dos desafios com a navegação faceta na Azure Cognitive Search é que as facetas existem apenas para os resultados atuais. Na prática, é comum reter um conjunto estático de facetas para que o utilizador possa navegar em marcha-atrás, retirando passos para explorar caminhos alternativos através do conteúdo de pesquisa. 

Embora este seja um caso de uso comum, não é algo que a estrutura de navegação faceta fornece atualmente fora da caixa. Os desenvolvedores que querem facetas estáticas normalmente funcionam em torno da limitação, emitindo duas consultas filtradas: uma com o cuidado dos resultados, a outra usada para criar uma lista estática de facetas para fins de navegação.

## <a name="see-also"></a>Consulte também

+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
+ [Criar Índice REST API](/rest/api/searchservice/create-index)
+ [Search Documents REST API](/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST)
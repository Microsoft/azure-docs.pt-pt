---
title: Filtros de faceta para navegação de pesquisa em apps
titleSuffix: Azure Cognitive Search
description: Filtrar critérios por identidade de segurança do utilizador, geolocalização ou valores numéricos para reduzir os resultados de pesquisa em consultas em Azure Cognitive Search, um serviço de pesquisa em nuvem hospedado no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792901"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Como construir um filtro de faceta na Pesquisa Cognitiva Azure 

A navegação facetada é usada para filtragem auto-direcionada em resultados de consulta numa aplicação de pesquisa, onde a sua aplicação oferece controlos ui para pesquisa de scoping a grupos de documentos (por exemplo, categorias ou marcas) e a Pesquisa Cognitiva Azure fornece a estrutura de dados para apoiar a experiência. Neste artigo, reveja rapidamente os passos básicos para criar uma estrutura de navegação facetada apoiando a experiência de pesquisa que pretende proporcionar. 

> [!div class="checklist"]
> * Escolha campos para filtragem e faceta
> * Definir atributos no campo
> * Construir os dados de índice e carga
> * Adicione filtros de faceta a uma consulta
> * Processar resultados

As facetas são dinâmicas e devolvidas numa consulta. As respostas de pesquisa trazem consigo as categorias de facetas usadas para navegar nos resultados. Se não está familiarizado com as facetas, o exemplo seguinte é uma ilustração de uma estrutura de navegação faceta.

  ![](./media/search-filters-facets/facet-nav.png)

Nova navegação facetada e quer mais detalhes? Ver [Como implementar navegação faceada na Pesquisa Cognitiva Azure](search-faceted-navigation.md).

## <a name="choose-fields"></a>Escolha campos

As facetas podem ser calculadas sobre campos de valor único, bem como coleções. Os campos que funcionam melhor na navegação facetada têm baixa cardeal: um pequeno número de valores distintos que se repetem ao longo de documentos no seu corpus de pesquisa (por exemplo, uma lista de cores, países/regiões ou marcas). 

O faceting é ativado numa base campo a campo quando `facetable` se `true`cria o índice, fixando o atributo para . Geralmente, também deve `filterable` definir `true` o atributo para tais campos para que a sua aplicação de pesquisa possa filtrar nesses campos com base em facetas que o utilizador final seleciona. 

Ao criar um índice utilizando a API REST, qualquer [tipo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) de campo `facetable` que possa ser utilizado na navegação faceada é marcado como por padrão:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Tipos de campo `Edm.Int32` `Edm.Int64`numéricos: ,`Edm.Double`
+ Coleções dos tipos acima referidos (por exemplo, `Collection(Edm.String)` ou `Collection(Edm.Double)`)

Não é `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` possível utilizar ou campos na navegação faceada. As facetas funcionam melhor em campos com baixa cardeal. Devido à resolução de geo-coordenadas, é raro que quaisquer dois conjuntos de coordenadas sejam iguais num determinado conjunto de dados. Como tal, as facetas não são apoiadas para geo-coordenadas. Precisaria de uma cidade ou campo regional para enfrentar a localização.

## <a name="set-attributes"></a>Definir atributos

Os atributos de índice que controlam a forma como um campo é usado são adicionados às definições individuais de campo no índice. No exemplo seguinte, campos com baixa cardeal, úteis `category` para enfrentar, consistem em: (hotel, motel, hostel), `tags`e `rating`. Estes campos `filterable` têm `facetable` os e atributos definidos explicitamente no exemplo seguinte para fins ilustrativos. 

> [!Tip]
> Como uma boa prática para otimização de desempenho e armazenamento, desligue-se de campos que nunca devem ser usados como faceta. Em particular, devem ser definidos campos de cordas para valores únicos, como identificação ou nome do produto, para `"facetable": false` evitar a sua utilização acidental (e ineficaz) na navegação facial.


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
> Esta definição de índice é copiada a partir de Criar um índice de [Pesquisa Cognitiva Azure utilizando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). É idêntico, exceto diferenças superficiais nas definições de campo. Os `filterable` `facetable` atributos e `category`atributos `smokingAllowed`são `rating` explicitamente adicionados em campos `tags`e `parkingIncluded`campos. Na `filterable` prática, `facetable` e seria ativado por padrão nestes campos ao utilizar a API REST. Ao utilizar o .NET SDK, estes atributos devem ser ativados explicitamente.

## <a name="build-and-load-an-index"></a>Construir e carregar um índice

Um passo intermédio (e talvez óbvio) é que você tem que [construir e povoar o índice](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) antes de formular uma consulta. Mencionamos este passo aqui para a completude. Uma forma de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicione filtros de faceta a uma consulta

No código de aplicação, construa uma consulta que especifique todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação, qualquer coisa usada para formular um pedido. O exemplo seguinte constrói um pedido que cria navegação de faceta com base no tipo de alojamento, classificação e outras comodidades.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Volte a ter resultados filtrados em eventos de clique

Quando o utilizador final clica num valor facial, o manipulador para o evento clique deve utilizar uma expressão de filtro para perceber a intenção do utilizador. Dada `category` uma faceta, clicar na categoria "motel" é implementado com uma `$filter` expressão que seleciona acomodações desse tipo. Quando um utilizador clica em "motel" para indicar que apenas os motéis devem ser mostrados, a próxima consulta que a aplicação envia inclui `$filter=category eq 'motel'`.

O seguinte código de corte adiciona categoria ao filtro se um utilizador selecionar um valor a partir da faceta da categoria.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Se o utilizador clicar num valor facial para `tags`um campo de recolha como, por exemplo, o valor "pool", a sua aplicação deve utilizar a seguinte sintaxe do filtro:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Dicas e suposições

### <a name="initialize-a-page-with-facets-in-place"></a>Inicialize uma página com facetas no lugar

Se quiser inicializar uma página com facetas no lugar, pode enviar uma consulta como parte da inicialização da página para semear a página com uma estrutura inicial de faceta.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Preservar uma estrutura de navegação faceta assincronicamente de resultados filtrados

Um dos desafios com a navegação faceta na Pesquisa Cognitiva Azure é que as facetas existem apenas para os resultados atuais. Na prática, é comum reter um conjunto estático de facetas para que o utilizador possa navegar em marcha-atrás, retirando passos para explorar caminhos alternativos através de conteúdos de pesquisa. 

Embora este seja um caso de uso comum, não é algo que a estrutura de navegação faceta atualmente fornece fora da caixa. Os desenvolvedores que querem facetas estáticas normalmente trabalham em torno da limitação através da emissão de duas consultas filtradas: uma com vista aos resultados, a outra usada para criar uma lista estática de facetas para fins de navegação.

## <a name="see-also"></a>Consulte também

+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
+ [Criar API de REPOUSO De Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST)

---
title: Filtros de facetas para a navegação de pesquisa em aplicações - Azure Search
description: Filtre critérios por identidade de segurança do utilizador, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem alojada no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 88171487fd180931d4659390f0db3c8619fb2d62
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653458"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Como criar um filtro de faceta no Azure Search 

Navegação por facetas é utilizada para filtragem auto-direcionada nos resultados de consulta num aplicativo de pesquisa, em que seu aplicativo oferece controles de interface do Usuário para definir o âmbito de pesquisa para grupos de documentos (por exemplo, categorias ou marcas) e Azure Search fornece a estrutura de dados para fazer uma cópia do experiência. Neste artigo, analise rapidamente os passos básicos para a criação de uma estrutura de navegação por facetas, deseja fornecer a experiência de pesquisa de segurança. 

> [!div class="checklist"]
> * Escolha os campos para filtragem e facetamento
> * Conjunto de atributos no campo
> * Criar os índice e carregar dados
> * Adicionar filtros de faceta para uma consulta
> * Processar resultados

Facetas são dinâmicos e retornados numa consulta. As respostas da pesquisa apresentam as categorias de faceta usadas para navegar os resultados. Se não estiver familiarizado com as facetas, o exemplo seguinte é uma ilustração de uma estrutura de navegação de faceta.

  ![](./media/search-filters-facets/facet-nav.png)

Navegação por facetas a e pretender mais detalham? Ver [como implementar a navegação por facetas no Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Escolha os campos

Facetas podem ser calculadas ao longo de campos de valor único, bem como coleções. Campos que funcionam melhor no painel de navegação por facetas tem a cardinalidade baixa: um pequeno número de valores distintos que repetem em toda a documentos no corpo (por exemplo, uma lista de cores, países/regiões ou nomes de marca). 

Facetamento é ativado numa base de campo por campo ao criar o índice, definindo a `facetable` atributo para `true`. Em geral, deve também definir o `filterable` atributo para `true` para campos de como, por exemplo, para que a sua aplicação de pesquisa pode filtrar esses campos com base em facetas que seleciona o utilizador final. 

Ao criar um índice através da API REST, qualquer [tipo de campo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) que, possivelmente, poderia ser usado na navegação por facetas está marcada como `facetable` por predefinição:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Tipos de campo numérico: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ Coleções de tipos de acima (por exemplo, `Collection(Edm.String)` ou `Collection(Edm.Double)`)

Não é possível usar `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)` campos no painel de navegação por facetas. Facetas funcionam melhor em campos com cardinalidade baixa. Devido a resolução de geo coordenadas de mensagens em fila, é raro que qualquer dois conjuntos de ordinates conjunta será iguais num determinado conjunto de dados. Como tal, facetas não são suportadas para geo coordenadas. Precisaria de um campo Cidade ou a região a faceta por localização.

## <a name="set-attributes"></a>Conjunto de atributos

Atributos de índice que controlam a forma como um campo é utilizado são adicionados às definições de campo individual no índice. No exemplo a seguir, os campos com cardinalidade baixa, útil para facetamento, consistem em: `category` (hotel, hotel, hostel), `tags`, e `rating`. Estes campos têm o `filterable` e `facetable` atributos conjunto explicitamente no exemplo a seguir para fins ilustrativos. 

> [!Tip]
> Como melhor prática para o desempenho e otimização de armazenamento, desative facetamento para os campos que nunca devem ser usados como um aspeto. Em particular, os campos de cadeia de caracteres para valores exclusivos, como um nome de ID ou o produto devem ser definidos como `"facetable": false` para evitar seu uso acidental (e ineficaz) no painel de navegação por facetas.


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
> Esta definição de índice é copiada da [criar um índice da Azure Search utilizando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). É idêntico, exceto para superficial diferenças nas definições de campo. O `filterable` e `facetable` atributos explicitamente são adicionados no `category`, `tags`, `parkingIncluded`, `smokingAllowed`, e `rating` campos. Na prática, `filterable` e `facetable` seria ativado por predefinição nesses campos ao utilizar a API de REST. Quando utilizar o SDK de .NET, esses atributos devem ser habilitados explicitamente.

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Um passo intermédio (e talvez óbvio) é que precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) antes de formular uma consulta. Mencionamos este passo para ser completo. Uma forma de determinar se o índice está disponível é ao verificar a lista de índices [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicionar filtros de faceta para uma consulta

No código da aplicação, crie uma consulta que especifica a todas as partes de uma consulta válida, incluindo as expressões de pesquisa, facetas, filtros, classificação perfis – nada utilizado para formular um pedido. O exemplo seguinte cria um pedido que cria a navegação de faceta com base no tipo de accommodation, classificação e outras características.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Devolver resultados filtrados em eventos de clique

Quando o usuário final clicar num valor de Faceta, o manipulador para o evento click deve usar uma expressão de filtro para concretizar a intenção do usuário. Tendo em conta um `category` Faceta, clicando a categoria "motel" é implementado com um `$filter` expressão que seleciona acomodações desse tipo. Quando um utilizador clica em "motel" para indicar que devem ser mostrados apenas cheios de baratas, a consulta seguinte, a aplicação envia inclui `$filter=category eq 'motel'`.

O fragmento de código seguinte adiciona categoria para o filtro se um usuário selecionar um valor da faceta de categoria.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Se o usuário clica num valor de faceta para um campo de coleção, como `tags`, por exemplo, o conjunto"valor", seu aplicativo deve usar a seguinte sintaxe de filtro: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Dicas e soluções alternativas

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializar uma página com as facetas no local

Se quiser inicializar uma página com as facetas no local, pode enviar uma consulta como parte da inicialização de página para efetuar o seeding a página com uma estrutura de faceta inicial.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Preservar uma estrutura de navegação de faceta assíncrona de resultados filtrados

Um dos desafios com navegação de faceta no Azure Search é que as facetas existem para apenas os resultados atuais. Na prática, é comum para manter um conjunto estático de facetas para que o usuário pode navegar na ordem inversa, retracing passos para explorar os caminhos alternativos por meio de conteúdo de pesquisa. 

Embora isso seja um caso de uso comum, não é algo a estrutura de navegação de faceta fornece atualmente out-of-the-box. Os desenvolvedores que desejam facetas estáticas normalmente solucionar a limitação através da emissão de duas consultas filtradas: um âmbito para os resultados, o outro utilizado para criar uma lista estática de facetas para fins de navegação.

## <a name="see-also"></a>Consulte também

+ [Filtros no Azure Search](search-filters.md)
+ [Criar API de REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Procurar nos documentos de REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

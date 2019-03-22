---
title: Filtros de facetas para a navegação de pesquisa em aplicações - Azure Search
description: Filtre critérios por identidade de segurança do utilizador, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem alojada no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8793f6f4d135d6099541d24aa5f5cfc0b6c21b30
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339436"
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

Facetas podem ser calculadas ao longo de campos de valor único, bem como coleções. Campos que funcionam melhor no painel de navegação por facetas tem a cardinalidade baixa: um pequeno número de valores distintos que repetem em toda a documentos no corpo (por exemplo, uma lista de cores, países ou nomes de marca). 

Facetamento é ativado numa base de campo por campo ao criar o índice, definindo os seguintes atributos como TRUE: `filterable`, `facetable`. Apenas os campos filtráveis podem ser facetados.

Qualquer [tipo de campo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) que, possivelmente, poderia ser usado na navegação por facetas está marcada como "facetável":

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Tipos de campo numérico: Edm.Int32, Edm.Int64, Edm.Double

Não é possível utilizar geographypoint no painel de navegação por facetas. Facetas são construídas a partir de texto legível ou números. Como tal, facetas não são suportadas para geo coordenadas. Precisaria de um campo Cidade ou a região a faceta por localização.

## <a name="set-attributes"></a>Conjunto de atributos

Atributos de índice que controlam a forma como um campo é utilizado são adicionados às definições de campo individual no índice. No exemplo a seguir, os campos com cardinalidade baixa, útil para facetamento, consistem em: categoria (hotel, hotel, hostel), características e as classificações. 

Na API do .NET, a filtragem de atributos tem de ser definido explicitamente. Na API do REST, facetamento e filtragem estão ativadas por predefinição, o que significa que só precisa de definir explicitamente os atributos quando pretender desativá-las. Embora não seja tecnicamente necessária, vamos mostrar as atribuições no seguinte exemplo do REST para fins educativos. 

> [!Tip]
> Como melhor prática para o desempenho e otimização de armazenamento, desative facetamento para os campos que nunca devem ser usados como um aspeto. Em particular, os campos de cadeia de caracteres para os valores de singleton, como um nome de ID ou o produto devem ser definidos como "Facetável": Falso para impedir que os seus acidental e (ineficaz) utilizar no painel de navegação por facetas.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Esta definição de índice é copiada da [criar um índice da Azure Search utilizando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). É idêntico, exceto para superficial diferenças nas definições de campo. Atributos filtrável e facetável explicitamente são adicionados na categoria, etiquetas, parkingIncluded, smokingAllowed e campos de classificação. Na prática, obtém filtrável e facetável para gratuitos no EDM, Boolean e Edm.Int32 tipos de campo. 

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Um passo intermédio (e talvez óbvio) é que precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index) antes de formular uma consulta. Mencionamos este passo para ser completo. Uma forma de determinar se o índice está disponível é ao verificar a lista de índices [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicionar filtros de faceta para uma consulta

No código da aplicação, crie uma consulta que especifica a todas as partes de uma consulta válida, incluindo as expressões de pesquisa, facetas, filtros, classificação perfis – nada utilizado para formular um pedido. O exemplo seguinte cria um pedido que cria a navegação de faceta com base no tipo de accommodation, classificação e outras características.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Devolver resultados filtrados em eventos de clique

A expressão de filtro processa o evento de clique no valor de faceta. Dada uma faceta de categoria, clicar a categoria "motel" é implementado por meio de um `$filter` expressão que seleciona acomodações desse tipo. Quando um utilizador clica em "cheios de baratas" para indicar que devem ser mostrados apenas cheios de baratas, a consulta seguinte, a aplicação envia inclui $filter = 'cheios de baratas categoria eq'.

O fragmento de código seguinte adiciona categoria para o filtro se um usuário selecionar um valor da faceta de categoria.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Com a API REST, a solicitação seria articulada como `$filter=category eq 'c1'`. Para tornar um campo de valor múltiplo de categoria, utilize a seguinte sintaxe: `$filter=category/any(c: c eq 'c1')`

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

---
title: 'Exemplo: Faceta de múltiplos níveis - Azure Search'
description: Aprenda a criar estruturas de facetamento de vários níveis taxonomias, criação de uma estrutura de navegação aninhadas que podem ser incluídos em páginas do aplicativo.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 7fa17528931be40109d81edac0f15a6a6822ec01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291784"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Exemplo: Faceta de múltiplos níveis no Azure Search

Esquemas de pesquisa do Azure não suportam explicitamente categorias de taxonomia de vários níveis, mas pode aproximá-los com a manipulação de conteúdo antes da indexação e, em seguida, aplicar alguns tratamento especial para os resultados. 

## <a name="start-with-the-data"></a>Comece com os dados

O exemplo neste artigo baseia-se um exemplo anterior, [modelar a base de dados de inventário do AdventureWorks](search-example-adventureworks-modeling.md), para demonstrar facetamento com múltiplos nível no Azure Search.

AdventureWorks tem uma taxonomia de dois níveis simple com uma relação principal-subordinado. Para a fundo o taxonomia de comprimento fixo essa estrutura, uma consulta de associação SQL simples pode servir-se para a taxonomia de grupo:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Consultar resultados](./media/search-example-adventureworks/prod-query-results.png "consultar resultados")

## <a name="indexing-to-a-collection-field"></a>Indexação a um campo de coleção

O índice que contém esta estrutura, criar um **Collection(Edm.String)** campo no esquema do Azure Search para armazenar estes dados, certificar-se de que os atributos de campo incluem pesquisável, filtrável, facetável e recuperável.

Agora, se precisa de indexar conteúdo que se refere a uma categoria específica de taxonomia, envio de taxonomia como uma matriz que contém o texto de cada nível de taxonomia. Por exemplo, para uma entidade com `ProductCategoryId = 5 (Mountain Bikes)`, submeta o campo como `[ "Bikes", "Bikes|Mountain Bikes"]`

Observe a inclusão da categoria principal "Bicicletas" no valor de categoria do filho "Bicicletas de montanha". Cada subcategoria deve incorporar o caminho completo relativo ao elemento raiz. O separador de caráter de pipe é arbitrário, mas tem de ser consistente e não deve aparecer no texto de origem. O caráter de separação será utilizado no código da aplicação para reconstruir a árvore de taxonomia de resultados de faceta.

## <a name="construct-the-query"></a>Construir a consulta

Quando emite consultas, incluem a especificação de faceta seguintes (onde taxonomia é o campo de taxonomia facetável): `facet = taxonomy,count:50,sort:value`

O valor de contagem deve ser alto o suficiente para retornar todos os valores de taxonomia possíveis. Os dados da AdventureWorks contém 41 valores de taxonomia distintos, então, `count:50` é suficiente.

  ![Filtrar por facetas](./media/search-example-adventureworks/facet-filter.png "filtro por facetas")

## <a name="build-the-structure-in-client-code"></a>Criar a estrutura no código do cliente

No código da aplicação de cliente, reconstrua a árvore de taxonomia, dividindo cada valor de faceta no caráter de pipe.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

O **categorias** objeto agora pode ser usado para processar uma árvore de taxonomia recolhível com contagens precisas:

  ![vários níveis filtro por facetas](./media/search-example-adventureworks/multi-level-facet.png "vários níveis filtro por facetas")

 
Cada ligação na árvore da deve aplicar o filtro relacionado. Por exemplo:

+ **Taxonomia/any** `(x:x eq 'Accessories')` devolve todos os documentos no ramo de acessórios
+ **Taxonomia/any** `(x:x eq 'Accessories|Bike Racks')` devolve apenas os documentos com uma subcategoria de Racks de bicicletas sob a ramificação de acessórios.

Essa técnica será dimensionada para abranger cenários mais complexos como árvores de taxonomia mais aprofundadas e duplicados subcategorias que ocorrem em categorias do elemento principal diferente (por exemplo, `Bike Components|Forks` e `Camping Equipment|Forks`).

> [!TIP]
> Velocidade de consulta é afetada pelo número de facetas devolvido. Para suportar conjuntos de taxonomia muito grandes, considere adicionar um facetável **EDM** campo para conter o valor de taxonomia de nível superior para cada documento. Em seguida, aplicar a mesma técnica acima, mas apenas executar a consulta de faceta de coleção (filtrada no campo de taxonomia de raiz) quando o utilizador expande um nó de nível superior. Em alternativa, se 100% de recolhimento não for necessário, simplesmente reduza a contagem de faceta para um número razoável e certifique-se de que as entradas de faceta são ordenadas por contagem.

## <a name="see-also"></a>Consulte também

[Exemplo: Modelo da base de dados de inventário do AdventureWorks para o Azure Search](search-example-adventureworks-modeling.md)
---
title: 'Exemplo: facetas de vários níveis-Azure Search'
description: Saiba como criar estruturas de faceta para taxonomias de vários níveis, criando uma estrutura de navegação aninhada que você pode incluir nas páginas do aplicativo.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 9a56bba55f9b3a59126168bc2bbbd50927c3fc78
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70274090"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Exemplo: facetas de vários níveis no Azure Search

Os esquemas de Azure Search não dão suporte explicitamente a categorias de taxonomia de vários níveis, mas você pode aproximar-los manipulando o conteúdo antes da indexação e, em seguida, aplicando uma manipulação especial aos resultados. 

## <a name="start-with-the-data"></a>Comece com os dados

O exemplo neste artigo se baseia em um exemplo anterior, [Modele o banco de dados de inventário AdventureWorks](search-example-adventureworks-modeling.md), para demonstrar a faceta de vários níveis no Azure Search.

O AdventureWorks tem uma taxonomia simples de dois níveis com uma relação pai-filho. Para obter as profundidades de taxonomia de comprimento fixo dessa estrutura, uma consulta de junção SQL simples pode ser usada para agrupar a taxonomia:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Resultados da consulta](./media/search-example-adventureworks/prod-query-results.png "Resultados da consulta")

## <a name="indexing-to-a-collection-field"></a>Indexando em um campo de coleção

No índice que contém essa estrutura, crie um campo de **coleção (EDM. String)** no esquema de Azure Search para armazenar esses dados, verificando se os atributos de campo incluem pesquisáveis, filtráveis, de face e recuperáveis.

Agora, ao indexar conteúdo que se refere a uma categoria de taxonomia específica, envie a taxonomia como uma matriz que contém o texto de cada nível da taxonomia. Por exemplo, para uma entidade com `ProductCategoryId = 5 (Mountain Bikes)`, envie o campo como `[ "Bikes", "Bikes|Mountain Bikes"]`

Observe a inclusão da categoria pai "bicicletas" no valor de categoria filho "Mountain Bikes". Cada subcategoria deve inserir seu caminho inteiro relativo ao elemento raiz. O separador de caracteres de pipe é arbitrário, mas deve ser consistente e não deve aparecer no texto de origem. O caractere separador será usado no código do aplicativo para reconstruir a árvore de taxonomia dos resultados da faceta.

## <a name="construct-the-query"></a>Construir a consulta

Ao emitir consultas, inclua a seguinte especificação de faceta (em que taxonomia é o campo de taxonomia facetable): `facet = taxonomy,count:50,sort:value`

O valor da contagem deve ser alto o suficiente para retornar todos os valores de taxonomia possíveis. Os dados da AdventureWorks contêm 41 valores de taxonomia distintos, portanto `count:50` é suficiente.

  ![Filtro facetado](./media/search-example-adventureworks/facet-filter.png "Filtro facetado")

## <a name="build-the-structure-in-client-code"></a>Criar a estrutura no código do cliente

No código do aplicativo cliente, reconstrua a árvore de taxonomia dividindo cada valor de faceta no caractere de pipe.

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

O objeto **Categories** agora pode ser usado para renderizar uma árvore de taxonomia recolhível com contagens precisas:

  ![filtro facetado de vários níveis](./media/search-example-adventureworks/multi-level-facet.png "filtro facetado de vários níveis")

 
Cada link na árvore deve aplicar o filtro relacionado. Por exemplo:

+ **taxonomia/qualquer** `(x:x eq 'Accessories')` retorna todos os documentos na ramificação acessórios
+ **taxonomia/qualquer** `(x:x eq 'Accessories|Bike Racks')` retorna apenas os documentos com uma subcategoria de racks de bicicletas sob a ramificação acessórios.

Essa técnica será dimensionada para abranger cenários mais complexos, como árvores de taxonomia mais profundas e subcategorias duplicadas que ocorrem em categorias pai diferentes (por exemplo, `Bike Components|Forks` e `Camping Equipment|Forks`).

> [!TIP]
> A velocidade da consulta é afetada pelo número de facetas retornadas. Para dar suporte a conjuntos de taxonomia muito grandes, considere adicionar um campo **EDM. String** de facetable para manter o valor de taxonomia de nível superior para cada documento. Em seguida, aplique a mesma técnica acima, mas apenas execute a consulta coleção-faceta (filtrada no campo taxonomia raiz) quando o usuário expande um nó de nível superior. Ou, se a recall de 100% não for necessária, simplesmente reduza a contagem de faceta para um número razoável e garanta que as entradas de faceta sejam classificadas por contagem.

## <a name="see-also"></a>Ver também

[Exemplo: Modele o banco de dados de inventário AdventureWorks para Azure Search](search-example-adventureworks-modeling.md)
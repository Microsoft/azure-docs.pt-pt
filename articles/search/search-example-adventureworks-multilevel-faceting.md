---
title: 'Exemplo: Facetas de vários níveis'
titleSuffix: Azure Cognitive Search
description: Aprenda a construir estruturas de rosto para taxonomias de vários níveis, criando uma estrutura de navegação aninhada que pode incluir nas páginas de candidatura.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792945"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Exemplo: Facetas de vários níveis na Pesquisa Cognitiva Azure

Os esquemas de pesquisa cognitiva azure não suportam explicitamente categorias de taxonomia multi-nível, mas pode aproximar-se deles manipulando o conteúdo antes da indexação e, em seguida, aplicando algum manuseamento especial aos resultados. 

## <a name="start-with-the-data"></a>Comece com os dados

O exemplo deste artigo baseia-se num exemplo anterior, [Model the AdventureWorks Inventory database,](search-example-adventureworks-modeling.md)para demonstrar a faceta de vários níveis na Pesquisa Cognitiva Azure.

A AdventureWorks tem uma simples taxonomia de dois níveis com uma relação pai-filho. Para as profundezas da taxonomia de comprimento fixo desta estrutura, uma simples consulta de adesão sQL pode ser usada para agrupar a taxonomia:

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

## <a name="indexing-to-a-collection-field"></a>Indexação para um campo de recolha

No índice que contém esta estrutura, crie um campo **de Recolha (Edm.String)** no esquema de Pesquisa Cognitiva Azure para armazenar estes dados, certificando-se de que os atributos de campo incluem pesquisável, filterável, faceta e recuperável.

Agora, ao indexar o conteúdo que se refere a uma categoria de taxonomia específica, submeta a taxonomia como um conjunto contendo texto de cada nível da taxonomia. Por exemplo, para `ProductCategoryId = 5 (Mountain Bikes)`uma entidade com, submeter o campo como`[ "Bikes", "Bikes|Mountain Bikes"]`

Note a inclusão da categoria de progenitores "Bicicletas" na categoria infantil de valor "Mountain Bikes". Cada subcategoria deve incorporar todo o seu caminho em relação ao elemento raiz. O separador de caracteres do tubo é arbitrário, mas deve ser consistente e não deve aparecer no texto de origem. O carácter separador será usado no código de aplicação para reconstruir a árvore taxonomia a partir de resultados de faceta.

## <a name="construct-the-query"></a>Construa a consulta

Ao emitir consultas, inclua a seguinte especificação faceta (onde a taxonomia é o seu campo de taxonomia faceta):`facet = taxonomy,count:50,sort:value`

O valor da contagem deve ser alto o suficiente para devolver todos os valores possíveis de taxonomia. Os dados da AdventureWorks contêm 41 valores `count:50` de taxonomia distintos, pelo que é suficiente.

  ![Filtro facetado](./media/search-example-adventureworks/facet-filter.png "Filtro facetado")

## <a name="build-the-structure-in-client-code"></a>Construir a estrutura no código do cliente

No código de aplicação do seu cliente, reconstrói a árvore de taxonomia dividindo cada valor facial no caráter do tubo.

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

O objeto **de categorias** pode agora ser usado para tornar uma árvore de taxonomia desmontável com contagens precisas:

  ![filtro facetado multinível](./media/search-example-adventureworks/multi-level-facet.png "filtro facetado multinível")

 
Cada elo na árvore deve aplicar o filtro relacionado. Por exemplo:

+ **taxonomia/quaisquer** `(x:x eq 'Accessories')` devoluções todos os documentos da sucursal de Acessórios
+ **taxonomia/qualquer** `(x:x eq 'Accessories|Bike Racks')` devolução apenas os documentos com uma subcategoria de Porta-bicicletas sob a sucursal de Acessórios.

Esta técnica irá abranger cenários mais complexos, como árvores de taxonomia mais profundas e subcategorias duplicadas que ocorrem em diferentes categorias de pais (por exemplo, `Bike Components|Forks` e). `Camping Equipment|Forks`

> [!TIP]
> A velocidade de consulta é afetada pelo número de facetas devolvidas. Para apoiar conjuntos de taxonomia muito grandes, considere adicionar um campo **edm.string** facetable para manter o valor de taxonomia de alto nível para cada documento. Em seguida, aplique a mesma técnica acima, mas apenas execute a consulta de faceta de recolha (filtrada no campo de taxonomia raiz) quando o utilizador expandir um nó de nível superior. Ou, se não for necessária uma recolha de 100%, basta reduzir a contagem de facetas para um número razoável, e garantir que as entradas de faceta são ordenadas pela contagem.

## <a name="see-also"></a>Consulte também

[Exemplo: Modelar a base de dados de inventário AdventureWorks para pesquisa cognitiva azure](search-example-adventureworks-modeling.md)
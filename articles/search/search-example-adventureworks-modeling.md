---
title: 'Exemplo: Modelar a base de dados de inventário AdventureWorks'
titleSuffix: Azure Cognitive Search
description: Aprenda a modelar dados relacionais, transformando-os num conjunto de dados achatado, para indexação e pesquisa completa de texto em Pesquisa Cognitiva Azure.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792999"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Exemplo: Modelar a base de dados de inventário AdventureWorks para pesquisa cognitiva azure

A Pesquisa Cognitiva Azure aceita um conjunto de linhas achatado como inputs para o [pipeline de indexação (ingestão](search-what-is-an-index.md)de dados). Se os seus dados de origem forem originários de uma base de dados relacional do SQL Server, este artigo demonstra uma abordagem para criar um conjunto de linhas achatado antes da indexação, utilizando a base de dados da amostra AdventureWorks como exemplo.

## <a name="about-adventureworks"></a>Sobre a AdventureWorks

Se tiver uma instância do SQL Server, poderá estar familiarizado com a base de dados de [amostras AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Entre as tabelas incluídas nesta base de dados estão cinco tabelas que expõem a informação do produto.

+ **ProductModel**: nome
+ **Produto**: nome, cor, custo, tamanho, peso, imagem, categoria (cada linha une-se a um Modelo de Produto específico)
+ **Descrição do produto**: descrição
+ **ProductModelProductDescription**: local (cada linha une um ProductModel a um ProdutoDescription para um idioma específico)
+ **Categoria de Produtos**: nome, categoria de pais

Combinar todos estes dados num conjunto de linhas achatado que pode ser ingerido num índice de pesquisa é o objetivo deste exemplo. 

## <a name="considering-our-options"></a>Considerando as nossas opções

A abordagem ingénua seria indexar todas as linhas da tabela produto (adesão, se for caso disso), uma vez que a tabela produto tem as informações mais específicas. No entanto, essa abordagem exporia o índice de pesquisa a duplicados percebidos num conjunto de resultados. Por exemplo, o modelo Road-650 está disponível em duas cores e seis tamanhos. Uma consulta para "bicicletas de estrada" seria então dominada por doze instâncias do mesmo modelo, diferenciadas apenas pelo tamanho e cor. Os outros seis modelos específicos da estrada seriam todos relegados para o mundo mais macio da pesquisa: a página dois.

  ![Lista de produtos](./media/search-example-adventureworks/products-list.png "Lista de produtos")
 
Note que o modelo Road-650 tem doze opções. As filas de entidades de um a muitas entidades são melhor representadas como campos de vários valores ou campos de valor pré-agregado no índice de pesquisa.

Resolver esta questão não é tão simples como mover o índice-alvo para a tabela ProductModel. Ao fazê-lo, ignoraria os dados importantes da tabela Produto que ainda deveriam estar representados nos resultados da pesquisa.

## <a name="use-a-collection-data-type"></a>Utilize um tipo de dados de recolha

A "abordagem correta" é utilizar uma funcionalidade de search-schema que não tenha um paralelo direto no modelo de base de dados: **Collection(Edm.String)**. Esta construção é definida no esquema do índice de pesquisa cognitiva Azure. Um tipo de dados de recolha é usado quando você precisa representar uma lista de cordas individuais, em vez de uma corda muito longa (única). Se tiver etiquetas ou palavras-chave, utilizará um tipo de dados de Recolha para este campo.

Ao definir campos de índice de vários valores de **Coleção (Edm.String)** para "cor", "tamanho" e "imagem", a informação acessória é mantida para enfrentar e filtrar sem poluir o índice com entradas duplicadas. Da mesma forma, aplique funções agregadas nos campos de produtos numéricos, indexando **minListPrice** em vez de cada lista de **produtosPrice**.

Dado um índice com estas estruturas, uma procura por "bicicletas de montanha" mostraria modelos de bicicletas discretos, preservando ao mesmo tempo metadados importantes como a cor, o tamanho e o preço mais baixo. A seguinte imagem fornece uma ilustração.

  ![Exemplo de pesquisa de bicicletas de montanha](./media/search-example-adventureworks/mountain-bikes-visual.png "Exemplo de pesquisa de bicicletas de montanha")

## <a name="use-script-for-data-manipulation"></a>Utilizar script para manipulação de dados

Infelizmente, este tipo de modelação não pode ser facilmente alcançado apenas através de declarações SQL. Em vez disso, use um simples script NodeJS para carregar os dados e, em seguida, mapeá-los em entidades JSON amigas da pesquisa.

O mapeamento final de pesquisa de base de dados é assim:

+ modelo (Edm.String: pesquisável, filterável, recuperável) de "ProductModel.Name"
+ description_en (Edm.String: pesquisável) de "ProductDescription" para o modelo onde cultura='en'
+ cor (Coleção(Edm.String): pesquisável, filterável, facetable, recuperável): valores únicos de "Product.Color" para o modelo
+ tamanho (Coleção(Edm.String): pesquisável, filterável, facetable, recuperável): valores únicos de "Produto.Tamanho" para o modelo
+ imagem (Coleção(Edm.String): recuperável): valores únicos de "Product.MiniaturaPhoto" para o modelo
+ minStandardCost (Edm.Double: filterable, facetable, sortable, recuperável): mínimo agregado de todos os "Product.StandardCost" para o modelo
+ minListPrice (Edm.Double: filterable, facetable, sortable, recuperável): mínimo agregado de todos os "Produto.ListPrice" para o modelo
+ minWeight (Edm.Double: filterable, facetable, sortable, recuperável): mínimo agregado de todos os "Produto.Peso" para o modelo
+ produtos (Coleção(Edm.String): pesquisável, filterável, recuperável): valores únicos de "Product.Name" para o modelo

Depois de juntar a tabela ProductModel com produto, e ProductDescription, use [lodash](https://lodash.com/) (ou Linq em C#) para transformar rapidamente o conjunto de resultados:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

O JSON resultante é assim:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Finalmente, aqui está a consulta SQL para devolver o recorde inicial. Usei o módulo [Mssql](https://www.npmjs.com/package/mssql) npm para carregar os dados na minha aplicação NodeJS.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exemplo: Taxonomies facet multinível na Pesquisa Cognitiva Azure](search-example-adventureworks-multilevel-faceting.md)
---
title: 'Exemplo: Modele o banco de dados de inventário AdventureWorks-Azure Search'
description: Saiba como modelar dados relacionais, transformando-os em um conjunto de dados mesclado, para indexação e pesquisa de texto completo em Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: c25dd34460e7e92bb20913f5b812044623dd38e3
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274026"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Exemplo: Modele o banco de dados de inventário AdventureWorks para Azure Search

Azure Search aceita um conjunto de linhas achatado como entradas para o [pipeline de indexação (ingestão de dados)](search-what-is-an-index.md). Se os dados de origem forem provenientes de um SQL Server banco de dado relacional, este artigo demonstra uma abordagem para criar um conjunto de linhas achatado antes da indexação, usando o banco de dados de exemplo AdventureWorks como exemplo.

## <a name="about-adventureworks"></a>Sobre o AdventureWorks

Se você tiver uma instância do SQL Server, talvez esteja familiarizado com o [banco de dados de exemplo AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Entre as tabelas incluídas neste banco de dados estão cinco tabelas que expõem informações do produto.

+ **ProductModel**: nome
+ **Produto**: nome, cor, custo, tamanho, peso, imagem, categoria (cada linha se une a um ProductModel específico)
+ **ProductDescription**: Descrição
+ **ProductModelProductDescription**: locale (cada linha une um ProductModel a um ProductDescription específico para um idioma específico)
+ **ProductCategory**: nome, categoria pai

Combinar todos esses dados em um conjunto de linhas achatado que pode ser ingerido em um índice de pesquisa é o objetivo deste exemplo. 

## <a name="considering-our-options"></a>Considerando nossas opções

A abordagem ingênua seria indexar todas as linhas da tabela Product (Unidas quando apropriado), já que a tabela Product tem as informações mais específicas. No entanto, essa abordagem exporia o índice de pesquisa a duplicatas observadas em um ResultSet. Por exemplo, o modelo Road-650 está disponível em duas cores e seis tamanhos. Uma consulta para "bicicletas de estrada" seria dominada por doze instâncias do mesmo modelo, diferenciadas apenas por tamanho e cor. Os outros seis modelos específicos de estrada seriam relegados ao mundo nem da pesquisa: a página dois.

  ![Lista de produtos](./media/search-example-adventureworks/products-list.png "Lista de produtos")
 
Observe que o modelo Road-650 tem doze opções. As linhas de entidade de um para muitos são mais bem representadas como campos de valores múltiplos ou campos de valor agregado no índice de pesquisa.

Resolver esse problema não é tão simples quanto mover o índice de destino para a tabela ProductModel. Isso ignoraria os dados importantes na tabela Product que ainda devem ser representados nos resultados da pesquisa.

## <a name="use-a-collection-data-type"></a>Usar um tipo de dados de coleção

A "abordagem correta" é utilizar um recurso de esquema de pesquisa que não tenha um paralelo direto no modelo de banco de dados: **Collection(Edm.String)** . Esse constructo é definido no esquema de índice de Azure Search. Um tipo de dados de coleção é usado quando você precisa representar uma lista de cadeias de caracteres individuais, em vez de uma cadeia de caracteres muito longa (única). Se você tiver marcas ou palavras-chave, use um tipo de dados de coleção para esse campo.

Ao definir campos de índice de vários valores da **coleção (EDM. String)** para "Color", "Size" e "Image", as informações complementares são mantidas para facetar e filtrar sem poluir o índice com entradas duplicadas. Da mesma forma, aplique funções de agregação aos campos numéricos do produto, indexando **minListPrice** em vez de cada único produto **listPrice**.

Dado um índice com essas estruturas, uma pesquisa por "Mountain Bikes" mostraria modelos de bicicleta discretos, preservando metadados importantes como cor, tamanho e preço mais baixo. A captura de tela a seguir fornece uma ilustração.

  ![Exemplo de pesquisa de bicicletas de montanha](./media/search-example-adventureworks/mountain-bikes-visual.png "Exemplo de pesquisa de bicicletas de montanha")

## <a name="use-script-for-data-manipulation"></a>Usar script para manipulação de dados

Infelizmente, esse tipo de modelagem não pode ser facilmente conseguido através de instruções SQL. Em vez disso, use um script NodeJS simples para carregar os dados e, em seguida, mapeá-los em entidades JSON amigáveis para pesquisa.

O mapeamento de pesquisa de banco de dados final tem esta aparência:

+ modelo (EDM. String: pesquisável, filtrável, recuperável) de "ProductModel.Name"
+ description_en (EDM. String: pesquisável) de "ProductDescription" para o modelo em que Culture = ' en '
+ Color (coleção (EDM. String): pesquisável, filtrável, facetable, recuperável): valores exclusivos de "Product. Color" para o modelo
+ Size (coleção (EDM. String): pesquisável, filtrável, facetable, recuperável): valores exclusivos de "Product. Size" para o modelo
+ Image (coleção (EDM. String): Retrieve): valores exclusivos de "Product. ThumbnailPhoto" para o modelo
+ minStandardCost (EDM. Double: filtrável, facetable, classificável, recuperável): o mínimo agregado de todos os "Product. StandardCost" para o modelo
+ minListPrice (EDM. Double: filtrável, facetable, classificável, recuperável): o mínimo agregado de todos os "Product. ListPrice" para o modelo
+ minWeight (EDM. Double: filtrável, facetable, classificável, recuperável): o mínimo agregado de todos os "Product. Weight" para o modelo
+ produtos (coleção (EDM. String): pesquisável, filtrável, recuperável): valores exclusivos de "Product.Name" para o modelo

Depois de ingressar na tabela ProductModel com Product e ProductDescription, use [lodash](https://lodash.com/) (ou LINQ C#no) para transformar rapidamente o ResultSet:

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

O JSON resultante é semelhante a este:

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

Finalmente, aqui está a consulta SQL para retornar o conjunto de registros inicial. Usei o módulo [MSSQL](https://www.npmjs.com/package/mssql) NPM para carregar os dados em meu aplicativo NodeJS.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Exemplo: Taxonomias de faceta de vários níveis no Azure Search](search-example-adventureworks-multilevel-faceting.md)
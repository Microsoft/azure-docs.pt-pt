---
title: 'Exemplo: Modelar a base de dados do inventário do AdventureWorks - Azure Search'
description: Saiba como modelar dados relacionais, transformá-los num conjunto de dados simplificado, para a pesquisa em texto completo e indexação no Azure Search.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291913"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Exemplo: Modelo da base de dados de inventário do AdventureWorks para o Azure Search

Modelagem estruturado base de dados do conteúdo para um índice de pesquisa eficiente raramente é um exercício bastante simples. Agendamento e gerenciamento de alterações à parte, existe o desafio de desnormalização de linhas de origem para fora do seu estado associado a um de tabela em entidades de pesquisa. Este artigo utiliza os dados de exemplo AdventureWorks, disponíveis online, para realçar experiências comuns na transição da base de dados para procurar. 

## <a name="about-adventureworks"></a>Sobre o AdventureWorks

Se tiver uma instância do SQL Server, poderá estar familiarizado com a base de dados de exemplo AdventureWorks. Entre as tabelas incluídas nesta base de dados estão cinco tabelas que expõem informações sobre o produto.

+ **ProductModel**: nome
+ **Produto**: nome, cor, custo, tamanho, peso, imagem, categoria (cada linha é associado a um ProductModel específico)
+ **ProductDescription**: Descrição
+ **ProductModelProductDescription**: Localidade (cada linha é associado um ProductModel para um ProductDescription específico para um idioma específico)
+ **ProductCategory**: nome, categoria principal

Combinar todos esses dados num conjunto de linhas bidimensional que pode ser ingerido num índice de pesquisa é a tarefa em questão. 

## <a name="considering-our-options"></a>Considerando as nossas opções

Essa abordagem simples seria indexar todas as linhas da tabela de produto (associados quando apropriado) desde a tabela de produto tem as informações mais específicas. No entanto, essa abordagem seria expor o índice de pesquisa duplicatas percebidos num conjunto de resultados. Por exemplo, o modelo de estrada 650 está disponível em duas cores e tamanhos de seis. Uma consulta para "bicicletas de viagem", em seguida, poderia ser dominada por doze instâncias do mesmo modelo, diferenciado apenas pelo tamanho e cor. Os outros modelos específicos de estrada seis seriam todos estariam relegados ao mundo nether da pesquisa: página dois.

  ![Lista de produtos](./media/search-example-adventureworks/products-list.png "lista de produtos")
 
Tenha em atenção que o modelo de estrada 650 tem doze opções. Linhas de entidade de um-para-muitos são a melhor forma os campos de valores múltiplos ou campos de pré-aggregated valor no índice de pesquisa.

Não é tão simples como mover o índice de destino para a tabela de ProductModel resolver este problema. Se o fizer, ignoraria a diferenciação dos dados importantes na tabela Produto que ainda deve ser apresentado nos resultados da pesquisa.

## <a name="use-a-collection-data-type"></a>Utilizar um tipo de dados de coleção

A abordagem"correta" é utilizar um recurso de esquema de pesquisa que não tenha um paralelo direto no modelo de base de dados: **Collection(Edm.String)**. Um tipo de dados de coleção é usado quando tem uma lista de cadeias individuais, em vez de muito longa cadeia de caracteres (única). Se tiver etiquetas ou palavras-chave, usaria um tipo de dados de coleção para este campo.

Definindo campos de índice de valores múltiplos de **Collection(Edm.String)** para "color", "size" e "image", as informações auxiliares são retida para facetamento e filtragem sem poluir o índice com entradas duplicadas. Da mesma forma, aplicam-se as funções de agregação para os campos numéricos do produto, indexação **minListPrice** em vez de todos os produtos **listPrice**.

Devido um índice com estas estruturas, uma pesquisa de "bicicletas de montanha" mostraria modelos de bicicletas discretos, preservando os metadados importantes, como cor, tamanho e preço mais baixo. Captura de ecrã seguinte fornece uma ilustração.

  ![Exemplo de pesquisa de bicicletas de montanha](./media/search-example-adventureworks/mountain-bikes-visual.png "exemplo de pesquisa de bicicletas de montanha")

## <a name="use-script-for-data-manipulation"></a>Use um script para manipulação de dados

Infelizmente, esse tipo de modelação não pode ser feito facilmente por meio de instruções SQL sozinhos. Em vez disso, utilize um script de NodeJS simples para carregar os dados e, em seguida, mapeá-lo em entidades JSON de pesquisa.

O mapeamento de pesquisa de base de dados final tem esta aparência:

+ modelo (EDM: pesquisável, filtrável, recuperável) de "ProductModel.Name"
+ description_en (EDM: pesquisável) de "ProductDescription" para o modelo de cultura em que = "en"
+ cor (Collection(Edm.String): pesquisável, filtrável, facetável, recuperável): valores exclusivos de "Product.Color" para o modelo
+ tamanho (Collection(Edm.String): pesquisável, filtrável, facetável, recuperável): valores exclusivos de "Product.Size" para o modelo
+ imagem (Collection(Edm.String): recuperável): valores exclusivos de "Product.ThumbnailPhoto" para o modelo
+ minStandardCost (Edm.Double: filtrável, facetável, ordenável, recuperável): mínimo de agregado de todos os "Product.StandardCost" para o modelo
+ minListPrice (Edm.Double: filtrável, facetável, ordenável, recuperável): mínimo de agregado de todos os "Product.ListPrice" para o modelo
+ minWeight (Edm.Double: filtrável, facetável, ordenável, recuperável): mínimo de agregado de todos os "Product.Weight" para o modelo
+ produtos (Collection(Edm.String): pesquisável, filtrável, recuperável): valores exclusivos de "Product.Name" para o modelo

Depois de associar a tabela de ProductModel com utilização de produto e ProductDescription, [lodash](https://lodash.com/) (ou Linq em C#) para transformar rapidamente o conjunto de resultados:

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

O JSON resultante tem esta aparência:

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

Por fim, eis a consulta SQL para devolver o conjunto de registros inicial. Usei o [mssql](https://www.npmjs.com/package/mssql) módulo de npm para carregar os dados para a minha aplicação NodeJS.

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
> [Exemplo: Taxonomias de faceta de múltiplos níveis no Azure Search](search-example-adventureworks-multilevel-faceting.md)



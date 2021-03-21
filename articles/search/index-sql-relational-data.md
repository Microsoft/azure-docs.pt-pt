---
title: Modelo SQL dados relacionais para importação e indexação
titleSuffix: Azure Cognitive Search
description: Aprenda a modelar dados relacionais, desal normalizados num conjunto de resultados plano, para indexar e pesquisar texto completo na Pesquisa Cognitiva Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924525"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Como modelar dados SQL relacionais para importação e indexação em Pesquisa Cognitiva Azure

A Azure Cognitive Search aceita um conjunto de linha plana como entrada para o [gasoduto de indexação](search-what-is-an-index.md). Se os seus dados de origem forem originários de tabelas unidas numa base de dados relacional do SQL Server, este artigo explica como construir o conjunto de resultados e como modelar uma relação pai-filho num índice de Pesquisa Cognitiva Azure.

Como ilustração, vamos referir-nos a uma base de dados hipotética de hotéis, com base em [dados de demonstração.](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels) Assuma que a base de dados é composta por uma mesa Hotels$ com 50 hotéis, e uma mesa de quartos com quartos de diferentes tipos, tarifas e comodidades, para um total de 750 quartos. Há uma relação entre muitas entre as mesas. Na nossa abordagem, uma vista irá fornecer a consulta que devolve 50 linhas, uma linha por hotel, com detalhes de quarto associado embutidos em cada linha.

   ![Tabelas e vista na base de dados dos hotéis](media/index-sql-relational-data/hotels-database-tables-view.png "Tabelas e vista na base de dados dos hotéis")


## <a name="the-problem-of-denormalized-data"></a>O problema dos dados desnormalizados

Um dos desafios em trabalhar com relacionamentos únicos é que as consultas padrão construídas em tabelas unidas devolverão dados desnormalizados, o que não funciona bem num cenário de Pesquisa Cognitiva Azure. Considere o seguinte exemplo que junta hotéis e quartos.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Os resultados desta consulta devolvem todos os campos do Hotel, seguidos por todos os campos de quartos, com informações preliminares do hotel a repetirem-se para cada valor do quarto.

   ![Dados denormalizados, dados de hotéis redundantes quando os campos dos quartos são adicionados](media/index-sql-relational-data/denormalize-data-query.png "Dados denormalizados, dados de hotéis redundantes quando os campos dos quartos são adicionados")


Embora esta consulta seja bem sucedida na superfície (fornecendo todos os dados em uma linha plana), falha em fornecer a estrutura documental certa para a experiência de pesquisa esperada. Durante a indexação, a Azure Cognitive Search criará um documento de pesquisa para cada linha ingerida. Se os seus documentos de pesquisa se parecessem com os resultados acima, teria percebido duplicados - sete documentos separados apenas para o hotel Twin Dome. Uma consulta sobre "hotéis na Flórida" devolveria sete resultados apenas para o hotel Twin Dome, empurrando outros hotéis relevantes para os resultados da pesquisa.

Para obter a experiência esperada de um documento por hotel, você deve fornecer um conjunto de linha na granularidade certa, mas com informações completas. Felizmente, pode fazê-lo facilmente adotando as técnicas deste artigo.

## <a name="define-a-query-that-returns-embedded-json"></a>Defina uma consulta que retorna json incorporado

Para fornecer a experiência de pesquisa esperada, o seu conjunto de dados deve consistir em uma linha para cada documento de pesquisa em Azure Cognitive Search. No nosso exemplo, queremos uma fila para cada hotel, mas também queremos que os nossos utilizadores possam pesquisar em outros campos relacionados com o quarto que lhes interessam, como a tarifa noturna, o tamanho e o número de camas, ou uma vista para a praia, todas elas fazem parte de um detalhe do quarto.

A solução é capturar o detalhe da sala como JSON aninhado, e depois inserir a estrutura JSON num campo numa vista, como mostra o segundo passo. 

1. Assuma que você tem duas mesas unidas, Hotéis $ Quartos$, que contêm detalhes para 50 hotéis e 750 quartos, e se juntam no campo HotelID. Individualmente, estas mesas contêm 50 hotéis e 750 quartos relacionados.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Crie uma vista composta por todos os campos da tabela dos pais `SELECT * from dbo.Hotels$` (), com a adição de um novo campo *De Quartos* que contenha a saída de uma consulta aninhada. Uma cláusula **PARA JSON AUTO** sobre `SELECT * from dbo.Rooms$` estruturas a saída como JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   A imagem que se segue mostra a vista resultante, com o campo *de nvarchar dos quartos* na parte inferior. O campo *de quartos* existe apenas na vista do HotelRooms.

   ![Vista para o HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Vista hoteRooms")

1. Corra `SELECT * FROM dbo.HotelRooms` para recuperar o conjunto de linhas. Esta consulta devolve 50 linhas, uma por hotel, com informação de quarto associada como coleção JSON. 

   ![Rowset da vista do HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Rowset da vista do HotelRooms")

Este conjunto de linhas está agora pronto para ser importado para a Pesquisa Cognitiva Azure.

> [!NOTE]
> Esta abordagem pressupõe que o JSON incorporado está sob os [limites máximos de tamanho da coluna do SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server). 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Use uma coleção complexa para o lado "muitos" de uma relação um-para-muitos

No lado da Pesquisa Cognitiva Azure, crie um esquema de índice que modele a relação de um a muitos usando JSON aninhado. O conjunto de resultados que criou na secção anterior geralmente corresponde ao esquema de índice fornecido abaixo (cortamos alguns campos para a brevidade).

O exemplo a seguir é semelhante ao exemplo em [Como modelar tipos de dados complexos.](search-howto-complex-data-types.md#creating-complex-fields) A estrutura *dos Quartos,* que tem sido o foco deste artigo, está na coleção de campos de um índice denominado *hotéis.* Este exemplo também mostra um tipo complexo de *Endereço,* que difere de *Quartos* na medida em que é composto por um conjunto fixo de itens, em oposição ao número múltiplo e arbitrário de itens permitidos numa coleção.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Tendo em conta o conjunto de resultados anteriores e o esquema de índice acima, tem todos os componentes necessários para uma operação de indexação bem sucedida. O conjunto de dados achatado satisfaz os requisitos de indexação, mas preserva informações detalhadas. No índice Azure Cognitive Search, os resultados da pesquisa cairão facilmente em entidades hoteleiras, preservando ao mesmo tempo o contexto de quartos individuais e seus atributos.

## <a name="next-steps"></a>Passos seguintes

Utilizando o seu próprio conjunto de dados, pode utilizar o [assistente de dados De importação](search-import-data-portal.md) para criar e carregar o índice. O assistente deteta a coleção JSON incorporada, como a contida nas *Salas,* e infere um esquema de índice que inclui uma coleção de tipo complexo. 

  ![Índice inferido pelo assistente de dados de importação](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Índice inferido pelo assistente de dados de importação")

Experimente o seguinte quickstart para aprender os passos básicos do assistente de dados De importação.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice de pesquisa utilizando o portal Azure](search-get-started-portal.md)
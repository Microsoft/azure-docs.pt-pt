---
title: Dados relacionais Modelo SQL para importação e indexação
titleSuffix: Azure Cognitive Search
description: Saiba como modelar dados relacionais, desnormalizados num conjunto de resultados planos, para indexação e pesquisa completa de texto em Pesquisa Cognitiva Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2ebeb7f6fee77c43c9da97b922fc215d75196145
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117574"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Como modelar dados relacionais de SQL para importação e indexação em Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure aceita um conjunto de linhas plana como entrada para o [pipeline de indexação](search-what-is-an-index.md). Se os seus dados de origem forem originados de tabelas unidas numa base de dados relacional do SQL Server, este artigo explica como construir o conjunto de resultados e como modelar uma relação pai-filho num índice de Pesquisa Cognitiva Azure.

Como ilustração, vamos referir-nos a uma base de dados hipotética de hotéis, com base em [dados de demonstração.](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels) Assuma que a base de dados é composta por uma mesa Hotels$ com 50 hotéis, e uma mesa de quartos$ com quartos de diferentes tipos, tarifas e comodidades, num total de 750 quartos. Há uma relação entre as mesas. Na nossa abordagem, uma vista proporcionará a consulta que devolve 50 filas, uma linha por hotel, com detalhes de quarto associados embutidos em cada linha.

   ![Tabelas e vista na base de dados hotéis](media/index-sql-relational-data/hotels-database-tables-view.png "Tabelas e vista na base de dados hotéis")


## <a name="the-problem-of-denormalized-data"></a>O problema dos dados desnormalizados

Um dos desafios no trabalho com relações de um a muitas é que as consultas padrão construídas em mesas unidas vão devolver dados desnormalizados, o que não funciona bem num cenário de Pesquisa Cognitiva Azure. Considere o seguinte exemplo que une hotéis e quartos.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Os resultados desta consulta devolvem todos os campos do Hotel, seguidos por todos os campos do quarto, com informações preliminares do hotel a repetirem-se pelo valor de cada quarto.

   ![Dados desnormalizados, dados redundantes do hotel quando os campos de quarto são adicionados](media/index-sql-relational-data/denormalize-data-query.png "Dados desnormalizados, dados redundantes do hotel quando os campos de quarto são adicionados")


Embora esta consulta tenha sucesso na superfície (fornecendo todos os dados num conjunto de linha plana), falha na entrega da estrutura documental certa para a experiência de pesquisa esperada. Durante a indexação, a Pesquisa Cognitiva Azure criará um documento de pesquisa para cada linha ingerida. Se os seus documentos de pesquisa se parecessem com os resultados acima referidos, teria percebido duplicados - sete documentos separados para o hotel Twin Dome sozinho. Uma consulta sobre "hotéis na Florida" devolveria sete resultados apenas para o hotel Twin Dome, empurrando outros hotéis relevantes para os resultados da pesquisa.

Para obter a experiência esperada de um documento por hotel, você deve fornecer um conjunto de linhas na granularidade certa, mas com informações completas. Felizmente, pode fazê-lo facilmente adotando as técnicas deste artigo.

## <a name="define-a-query-that-returns-embedded-json"></a>Defina uma consulta que devolve jSON incorporado

Para fornecer a experiência de pesquisa esperada, o seu conjunto de dados deve consistir numa linha para cada documento de pesquisa em Pesquisa Cognitiva Azure. No nosso exemplo, queremos uma fila para cada hotel, mas também queremos que os nossos utilizadores possam pesquisar outros campos relacionados com o quarto que lhes interessam, como a diária, o tamanho e o número de camas, ou uma vista para a praia, todas elas fazem parte de um detalhe de quarto.

A solução é capturar o detalhe da sala como JSON aninhado, e depois inserir a estrutura JSON num campo numa vista, como mostrado no segundo passo. 

1. Assuma que você tem duas mesas unidas, Hotéis$ e Quartos$, que contêm detalhes para 50 hotéis e 750 quartos, e estão unidos no campo HotelID. Individualmente, estas mesas contêm 50 hotéis e 750 quartos relacionados.

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

2. Crie uma vista composta por todos`SELECT * from dbo.Hotels$`os campos da tabela dos pais com a adição de um campo de novos *Quartos* que contenha a saída de uma consulta aninhada. Uma cláusula **JSON** `SELECT * from dbo.Rooms$` AUTO sobre estruturas a saída como JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   A imagem seguinte mostra a vista resultante, com o campo nvarchar *rooms* na parte inferior. O campo *de quartos* existe apenas na vista do HotelRooms.

   ![Vista para hotéis](media/index-sql-relational-data/hotelsrooms-view.png "Vista HoteRooms")

1. Corra `SELECT * FROM dbo.HotelRooms` para recuperar o conjunto de filas. Esta consulta devolve 50 filas, uma por hotel, com informações de quarto associadas como uma coleção JSON. 

   ![Rowset da vista HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Rowset da vista HotelRooms")

Este conjunto de linhas está agora pronto para ser importado para a Pesquisa Cognitiva Azure.

> [!NOTE]
> Esta abordagem pressupõe que o JSON incorporado está sob os [limites máximos de tamanho da coluna do Servidor SQL](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server). 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Use uma coleção complexa para o lado "muitos" de uma relação de um a muitos

Do lado da Pesquisa Cognitiva Azure, crie um esquema de índice que modele a relação de um a muitos usando o Anado JSON. O conjunto de resultados que criou na secção anterior corresponde geralmente ao esquema de índice fornecido abaixo (cortamos alguns campos para brevidade).

O exemplo que se segue é semelhante ao exemplo de [Como modelar tipos de dados complexos](search-howto-complex-data-types.md#creating-complex-fields). A estrutura dos *Quartos,* que tem sido o foco deste artigo, está na coleção de campos de um índice chamado *hotéis.* Este exemplo também mostra um tipo complexo de *Endereços,* que difere dos *Quartos* na medida em que é composto por um conjunto fixo de itens, em oposição ao número múltiplo e arbitrário de itens permitidos numa coleção.

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

Dado o conjunto de resultados anterior e o esquema de índice acima, você tem todos os componentes necessários para uma operação de indexação bem sucedida. O conjunto de dados achatado satisfaz os requisitos de indexação, mas preserva informações detalhadas. No índice de Pesquisa Cognitiva Azure, os resultados da pesquisa cairão facilmente em entidades hoteleiras, preservando ao mesmo tempo o contexto de quartos individuais e seus atributos.

## <a name="next-steps"></a>Passos seguintes

Utilizando o seu próprio conjunto de dados, pode utilizar o assistente de [dados da Importação](search-import-data-portal.md) para criar e carregar o índice. O assistente deteta a coleção JSON incorporada, como a contida em *Salas,* e infere um esquema de índice que inclui uma coleção de tipo complexo. 

  ![Índice inferido por assistente de dados de importação](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Índice inferido por assistente de dados de importação")

Experimente o seguinte quickstart para aprender os passos básicos do assistente de dados da Importação.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice de pesquisa utilizando o portal Azure](search-get-started-portal.md)
---
title: Modelar dados relacionais do SQL para importação e indexação-Azure Search
description: Saiba como modelar dados relacionais, desnormalizados em um conjunto de resultados simples, para indexação e pesquisa de texto completo em Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: heidist
ms.openlocfilehash: f6cc119387482e4f5403e91de98916cbe2aaff2a
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963502"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-search"></a>Como modelar dados SQL relacionais para importação e indexação no Azure Search

Azure Search aceita um conjunto de linhas simples como entrada para o [pipeline de indexação](search-what-is-an-index.md). Se os dados de origem forem provenientes de tabelas unidas em um banco de dados relacional SQL Server, este artigo explicará como construir o conjunto de resultados e como modelar uma relação pai-filho em um índice de Azure Search.

Como ilustração, vamos nos referir a um banco de dados de hotéis hipotéticos, com base na [demonstração](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Suponha que o banco de dados consiste em um Hotéis $ Table com 50 hotéis e uma tabela Rooms $ com salas de tipos variados, taxas e comodidades, para um total de 750 salas. Há uma relação um-para-muitos entre as tabelas. Em nossa abordagem, uma exibição fornecerá a consulta que retorna 50 linhas, uma linha por Hotel, com detalhes de sala associado inseridos em cada linha.

   ![Tabelas e exibições no banco de dados de hotéis](media/index-sql-relational-data/hotels-database-tables-view.png "Tabelas e exibições no banco de dados de hotéis")


## <a name="the-problem-of-denormalized-data"></a>O problema dos dados desnormalizados

Um dos desafios de trabalhar com relações um-para-muitos é que as consultas padrão criadas em tabelas unidas retornarão dados desnormalizados, o que não funciona bem em um cenário de Azure Search. Considere o exemplo a seguir que une Hotéis e salas.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Os resultados dessa consulta retornam todos os campos de Hotel, seguidos por todos os campos de sala, com a repetição de informações preliminares do hotel para cada valor de sala.

   ![Dados desnormalizados, dados de Hotel redundantes quando campos de sala são adicionados](media/index-sql-relational-data/denormalize-data-query.png "Dados desnormalizados, dados de Hotel redundantes quando campos de sala são adicionados")


Embora essa consulta tenha êxito na superfície (fornecendo todos os dados em um conjunto de linhas simples), ela falha ao fornecer a estrutura de documentos correta para a experiência de pesquisa esperada. Durante a indexação, Azure Search criará um documento de pesquisa para cada linha ingerida. Se os documentos de pesquisa parecerem com os resultados acima, você terá percebido duplicatas-sete documentos separados para o Hotel Dome. Uma consulta em "Hotéis na Flórida" retornaria sete resultados apenas para o Hotel Dome, enviando outros hotéis relevantes profundamente nos resultados da pesquisa.

Para obter a experiência esperada de um documento por Hotel, você deve fornecer um conjunto de linhas à granularidade correta, mas com informações completas. Felizmente, você pode fazer isso facilmente adotando as técnicas neste artigo.

## <a name="define-a-query-that-returns-embedded-json"></a>Definir uma consulta que retorna JSON inserido

Para fornecer a experiência de pesquisa esperada, seu conjunto de dados deve consistir em uma linha para cada documento de pesquisa no Azure Search. Em nosso exemplo, queremos uma linha para cada hotel, mas também queremos que nossos usuários sejam capazes de pesquisar outros campos relacionados à sala com os quais eles se preocupam, como a taxa noturna, o tamanho e o número de ambientes, ou uma exibição da praia, todas que fazem parte de um detalhe de sala.

A solução é capturar os detalhes da sala como JSON aninhado e, em seguida, inserir a estrutura JSON em um campo em uma exibição, conforme mostrado na segunda etapa. 

1. Suponha que você tenha duas tabelas unidas, hotéis $ e quartos $, que contenham detalhes de 50 hotéis e 750 quartos, e que sejam Unidas no campo Hotelid. Individualmente, essas tabelas contêm 50 hotéis e 750 salas relacionadas.

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

2. Crie uma exibição composta de todos os campos na tabela pai (`SELECT * from dbo.Hotels$`), com a adição de um novo campo de *salas* que contenha a saída de uma consulta aninhada. Uma cláusula **for JSON auto** em `SELECT * from dbo.Rooms$` estrutura a saída como JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   A captura de tela a seguir mostra a exibição resultante, com o campo nvarchar *Rooms* na parte inferior. O campo de *salas* existe apenas na exibição HotelRooms.

   ![Exibição de HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Exibição de HoteRooms")

1. Execute `SELECT * FROM dbo.HotelRooms` para recuperar o conjunto de linhas. Essa consulta retorna 50 linhas, uma por Hotel, com informações de sala associadas como uma coleção JSON. 

   ![Conjunto de linhas da exibição HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Conjunto de linhas da exibição HotelRooms")

Este conjunto de linhas agora está pronto para importação no Azure Search.

> [!NOTE]
> Essa abordagem pressupõe que o JSON inserido esteja abaixo dos [limites máximos de tamanho de coluna de SQL Server](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server). Se os dados não couberem, você poderá experimentar uma abordagem programática, conforme ilustrado no [exemplo: Modele o banco de dados de](search-example-adventureworks-modeling.md)inventário AdventureWorks para Azure Search.

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Use uma coleção complexa para o lado "muitos" de uma relação um-para-muitos

No lado do Azure Search, crie um esquema de índice que modela a relação um-para-muitos usando JSON aninhado. O conjunto de resultados criado na seção anterior geralmente corresponde ao esquema de índice fornecido abaixo (cortamos alguns campos para fins de brevidade).

O exemplo a seguir é semelhante ao exemplo em [como modelar tipos de dados complexos](search-howto-complex-data-types.md#creating-complex-fields). A estrutura de *salas* , que tem sido o foco deste artigo, está na coleção Fields de um índice chamado *Hotéis*. Este exemplo também mostra um tipo complexo para *endereço*, que difere de *salas* em que ele é composto de um conjunto fixo de itens, em oposição ao múltiplo, o número arbitrário de itens permitidos em uma coleção.

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

Considerando o conjunto de resultados anterior e o esquema de índice acima, você tem todos os componentes necessários para uma operação de indexação bem-sucedida. O conjunto de dados achatados atende aos requisitos de indexação, mas preserva as informações detalhadas. No índice de Azure Search, os resultados da pesquisa ficarão facilmente em entidades baseadas em Hotel, preservando, ao mesmo tempo, o contexto de salas individuais e seus atributos.

## <a name="next-steps"></a>Passos Seguintes

Você pode usar o [Assistente para importar dados](search-import-data-portal.md) para indexar um conjunto de linhas semelhante ao descrito neste artigo. O assistente detecta a coleção JSON inserida em *salas* e infere um esquema de índice que fornece a coleção de tipos complexos apropriada. 

  ![Índice inferido pelo assistente de importação de dados](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Índice inferido pelo assistente de importação de dados")

Para concluir a importação e criar um índice utilizável, você precisaria selecionar a chave e definir os atributos por conta própria. Se você não estiver familiarizado com esse assistente, tente o início rápido a seguir para aprender as etapas básicas.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice de pesquisa usando portal do Azure](search-get-started-portal.md)
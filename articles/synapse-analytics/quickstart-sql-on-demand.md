---
title: 'Quickstart: Use a piscina SQL sem servidor'
description: Neste arranque rápido, você verá e aprenderá como é fácil consultar vários tipos de ficheiros usando piscina SQL sem servidor (pré-visualização).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2e502a984e71a06eb57b345371d70d659c6a031
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321672"
---
# <a name="quickstart-use-serverless-sql-pool"></a>Quickstart: Use a piscina SQL sem servidor

Synapse serverless SQL pool (pré-visualização) é um serviço de consulta sem servidor que lhe permite executar consultas SQL em ficheiros colocados no Azure Storage. Neste arranque rápido, você vai aprender a consultar vários tipos de ficheiros usando piscina SQL sem servidor. Os formatos suportados estão listados em [OPENROWSET](sql/develop-openrowset.md).

Este quickstart mostra consultas: ficheiros CSV, Apache Parquet e JSON.

## <a name="prerequisites"></a>Pré-requisitos

Escolha um cliente SQL para emitir consultas:

- [O Azure Synapse Studio](quickstart-synapse-studio.md) é uma ferramenta web que pode usar para navegar em ficheiros no armazenamento e criar consultas SQL.
- [O Azure Data Studio](sql/get-started-azure-data-studio.md) é uma ferramenta de cliente que lhe permite executar consultas e cadernos SQL na sua base de dados On demand.
- [SQL Server Management Studio](sql/get-started-ssms.md) é uma ferramenta de cliente que lhe permite executar consultas SQL na sua base de dados on-demand.

Parâmetros para este arranque rápido:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| endereço final de serviço de piscina sem servidor SQL    | Usado como nome de servidor                                   |
| região de ponto final de serviço de piscina SQL sem servidor     | Usado para determinar que armazenamento vamos usar em amostras |
| Nome de utilizador e senha para acesso ao ponto final | Usado para aceder ao ponto final                               |
| A base de dados usada para criar pontos de vista         | Base de dados utilizada como ponto de partida em amostras       |

## <a name="first-time-setup"></a>Configuração pela primeira vez

Antes de utilizar as amostras:

- Crie base de dados para as suas opiniões (caso pretenda utilizar vistas)
- Criar credenciais para ser usado pela piscina SQL sem servidor para aceder a ficheiros no armazenamento

### <a name="create-database"></a>Criar base de dados

Crie a sua própria base de dados para fins de demonstração. Você usará esta base de dados para criar as suas opiniões e para as consultas de amostra neste artigo.

> [!NOTE]
> As bases de dados são utilizadas apenas para visualizar metadados, não para dados reais.
>Anote o nome da base de dados que usa para ser usado mais tarde no Quickstart.

Use a seguinte consulta, `mydbname` alterando-se para um nome à sua escolha:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Criar fonte de dados

Para executar consultas utilizando o pool SQL sem servidor, crie uma fonte de dados que o pool SQL sem servidor pode usar para aceder a ficheiros no armazenamento.
Execute o seguinte corte de código para criar fonte de dados utilizada em amostras nesta secção:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>Consultar ficheiros CSV

A imagem a seguir é uma pré-visualização do ficheiro a ser consultado:

![Primeiras 10 linhas do ficheiro CSV sem cabeçalho, nova linha estilo Windows.](./sql/media/query-single-csv-file/population.png)

A seguinte consulta mostra como ler um ficheiro CSV que não contém uma linha de cabeçalho, com uma nova linha ao estilo do Windows e colunas delimitadas em vírgula:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Pode especificar o esquema na hora da compilação de consultas.
Para mais exemplos, consulte como [consultar o ficheiro CSV](sql/query-single-csv-file.md).

## <a name="query-parquet-files"></a>Consultar ficheiros de Parquet

A amostra que se segue mostra as capacidades automáticas de inferência de esquema para consulta de ficheiros Parquet. Devolve o número de filas em setembro de 2017 sem especificar o esquema.

> [!NOTE]
> Não é preciso especificar colunas na `OPENROWSET WITH` cláusula ao ler ficheiros Parquet. Nesse caso, a piscina SQL sem servidor utiliza metadados no ficheiro Parquet e liga colunas pelo nome.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

Saiba mais sobre [a consulta dos ficheiros parquet](sql/query-parquet-files.md).

## <a name="query-json-files"></a>Consultar ficheiros JSON

### <a name="json-sample-file"></a>Ficheiro de amostra JSON

Os ficheiros são armazenados em recipiente *json,* *livros* de pastas, e contêm entrada de livro único com a seguinte estrutura:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>Consultar ficheiros JSON

A seguinte consulta mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para recuperar valores escalar (título, editor) de um livro com o título *Métodos Probabilísticos e Estatísticos em Criptologia, Uma Introdução por Artigos Selecionados* :

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Estamos a ler todo o ficheiro JSON como uma única linha/coluna. Assim, o FIELDTERMINATOR, o FIELDQUOTE e o ROWTERMINATOR estão definidos para 0x0b porque não esperamos encontrá-lo no ficheiro.

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para continuar com os seguintes artigos:

- [Consulta único ficheiro CSV](sql/query-single-csv-file.md)
- [Pastas de consulta e vários ficheiros CSV](sql/query-folders-multiple-csv-files.md)
- [Consulta de ficheiros específicos](sql/query-specific-files.md)
- [Consultar ficheiros de Parquet](sql/query-parquet-files.md)
- [Consultar tipos aninhados de Parquet](sql/query-parquet-nested-types.md)
- [Consultar ficheiros JSON](sql/query-json-files.md)
- [Criar e usar vistas](sql/create-use-views.md)
- [Criar e utilizar tabelas externas](sql/create-use-external-tables.md)
- [Persistência resultado da consulta para o armazenamento do Azure](sql/create-external-table-as-select.md)
- [Consulta único ficheiro CSV](sql/query-single-csv-file.md)

---
title: Utilização de SQL a pedido (pré-visualização)
description: Neste arranque rápido, você verá e aprenderá como é fácil consultar vários tipos de ficheiros usando sQL on-demand (pré-visualização).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0d543abc88c1e45f2c1f5503473d8e92566fc582
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457387"
---
# <a name="quickstart-using-sql-on-demand"></a>Quickstart: Utilização a pedido do SQL

Synapse SQL on-demand (pré-visualização) é um serviço de consulta sem servidor que lhe permite executar as consultas SQL nos seus ficheiros colocados no Armazenamento Azure. Neste arranque rápido, você vai aprender a consultar vários tipos de ficheiros usando sQL on-demand.

Os seguintes tipos de ficheiros são suportados: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Pré-requisitos

Escolha um cliente SQL para emitir consultas:

- [O Azure Synapse Studio](quickstart-synapse-studio.md) é uma ferramenta web que pode usar para navegar ficheiros no armazenamento e criar consultas SQL.
- [O Azure Data Studio](sql/get-started-azure-data-studio.md) é uma ferramenta de cliente que lhe permite executar consultas e cadernos SQL na sua base de dados On-demand.
- [O SQL Server Management Studio](sql/get-started-ssms.md) é uma ferramenta de cliente que lhe permite executar consultas SQL na sua base de dados On-demand.

Parâmetros para o arranque rápido:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço final do serviço sql on-demand    | Usado como nome do servidor                                   |
| Região de ponto final do serviço sql a pedido     | Usado para determinar que armazenamento usaremos em amostras |
| Nome de utilizador e senha para acesso ao ponto final | Usado para aceder ao ponto final                               |
| A base de dados usada para criar pontos de vista         | Base de dados utilizada como ponto de partida em amostras       |

## <a name="first-time-setup"></a>Configuração pela primeira vez

Antes da utilização de amostras:

- Crie base de dados para os seus pontos de vista (caso pretenda utilizar pontos de vista)
- Criar credenciais a utilizar pela SQL a pedido para aceder a ficheiros no armazenamento

### <a name="create-database"></a>Criar base de dados

Crie a sua própria base de dados para fins de demonstração. Esta é a base de dados em que se cria a sua opinião. Utilize esta base de dados nas consultas de amostra neste artigo.

> [!NOTE]
> As bases de dados são utilizadas apenas para visualizar metadados, não para dados reais.
>
> Escreva o nome da base de dados que utilizar para ser utilizado mais tarde no Quickstart.

Use a seguinte consulta, mudando `mydbname` para um nome à sua escolha:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Criar credenciais

Para executar consultas utilizando o SQL on-demand, crie credenciais para a SQL a pedido de utilização para aceder a ficheiros no armazenamento.

> [!NOTE]
> Note que precisa de criar credenciais para acesso à conta de armazenamento. Embora a SQL on-demand possa aceder a armazenamentos de diferentes regiões, ter armazenamento e espaço de trabalho Azure Synapse na mesma região proporcionará uma melhor experiência de desempenho.

Modificar o seguinte código de corte para criar credenciais para recipientes CSV, JSON e Parquet:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>Consulta de ficheiros CSV

A seguinte imagem é uma pré-visualização do ficheiro a ser consultado:

![Primeiras 10 linhas do ficheiro CSV sem cabeçalho, nova linha estilo Windows.](./sql/media/query-single-csv-file/population.png)

A seguinte consulta mostra como ler um ficheiro CSV que não contém uma linha de cabeçalho, com nova linha ao estilo do Windows, e colunas delimitadas de vírem:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
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

Pode especificar o esquema no tempo de compilação de consultas.
Para mais exemplos, veja como consultar o [ficheiro CSV](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Consulta de ficheiros de parquet

A amostra que se segue mostra as capacidades automáticas de inferência do esquema para consulta de ficheiros Parquet. Devolve o número de filas em setembro de 2017 sem especificar o esquema.

> [!NOTE]
> Não é preciso especificar colunas na `OPENROWSET WITH` cláusula ao ler ficheiros Parquet. Nesse caso, a SQL a pedido utiliza metadados no ficheiro Parquet e liga colunas pelo nome.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Encontre mais informações sobre a [consulta de ficheiros de parquet].](sql/query-parquet-files.md)

## <a name="querying-json-files"></a>Consulta de ficheiros JSON

### <a name="json-sample-file"></a>Arquivo de amostra JSON

Os ficheiros são armazenados em recipiente *json,* *livros*de pastas, e contêm uma única entrada de livro com a seguinte estrutura:

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

### <a name="querying-json-files"></a>Consulta de ficheiros JSON

A consulta mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar valores escalar (título, editor) de um livro com o título *Probabilístico e Métodos Estatísticos em Criptologia, Uma Introdução por artigos selecionados:*

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
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
> Estamos a ler todo o ficheiro JSON como uma única linha/coluna para que FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR estejam definidos para 0x0b porque não esperamos encontrá-lo no ficheiro.

## <a name="next-steps"></a>Passos seguintes

Agora está pronto para começar com os seguintes artigos da Quickstart:

- [Consulta single CSV arquivo](sql/query-single-csv-file.md)
- [Questiões e vários ficheiros CSV](sql/query-folders-multiple-csv-files.md)
- [Consulta de ficheiros específicos](sql/query-specific-files.md)
- [Arquivos De Consulta Parquet](sql/query-parquet-files.md)
- [Tipos aninhados de Parquet de consulta](sql/query-parquet-nested-types.md)
- [Consulta de ficheiros JSON](sql/query-json-files.md)
- [Criação e utilização de pontos de vista](sql/create-use-views.md)
- [Criação e utilização de tabelas externas](sql/create-use-external-tables.md)
- [Persistir resultado da consulta ao armazenamento do Azure](sql/create-external-table-as-select.md)

Avance para o próximo artigo para aprender a consultar um único ficheiro CSV.
> [!div class="nextstepaction"]
> [Consulta single CSV arquivo](sql/query-single-csv-file.md)

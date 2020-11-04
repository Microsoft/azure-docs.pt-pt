---
title: Utilize tabelas externas com Sinapse SQL
description: Ler ou escrever ficheiros de dados com O SQL da Sinapse
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: a5a958228d79c86550604109d7aaf19e68593a57
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314960"
---
# <a name="use-external-tables-with-synapse-sql"></a>Utilize tabelas externas com Sinapse SQL

Uma tabela externa aponta para dados localizados em Hadoop, Azure Storage blob, ou Azure Data Lake Storage. As tabelas externas são usadas para ler dados a partir de ficheiros ou escrever dados para ficheiros no Azure Storage. Com o Synapse SQL, pode utilizar tabelas externas para ler e escrever dados para piscina SQL dedicada ou piscina SQL sem servidor (pré-visualização).

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Mesas externas em piscina SQL dedicada e piscina SQL sem servidor

### <a name="dedicated-sql-pool"></a>[Piscina SQL dedicada](#tab/sql-pool) 

Na piscina SQL dedicada, você pode usar uma mesa externa para:

- Consulta Azure Blob Storage e Azure Data Lake Gen2 com declarações Transact-SQL.
- Importar e armazenar dados da Azure Blob Storage e do Azure Data Lake Storage em piscinas SQL dedicadas.

Quando utilizado em conjunto com a declaração [CREATE TABLE AS SELECT,](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) selecionando de uma tabela externa os dados de importação para uma tabela dentro do pool SQL. Além da [declaração COPY,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)as tabelas externas são úteis para o carregamento de dados. 

Para um tutorial de carregamento, consulte [o PolyBase para carregar os dados do Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="serverless-sql-pool"></a>[Piscina SQL sem servidor](#tab/sql-on-demand)

Para piscina SQL sem servidor, você usará uma mesa externa para:

- Dados de consulta no Azure Blob Storage ou Azure Data Lake Storage com declarações Transact-SQL
- Armazenar a consulta de piscina SQL sem servidor resulta em ficheiros no Azure Blob Storage ou no Azure Data Lake Storage usando [o CETAS](develop-tables-cetas.md)

Pode criar tabelas externas utilizando a piscina SQL sem servidor através dos seguintes passos:

1. CRIAR FONTE DE DADOS EXTERNA
2. CREATE EXTERNAL FILE FORMAT
3. CRIAR TABELA EXTERNA

---

### <a name="security"></a>Segurança

O utilizador deve ter `SELECT` permissão na tabela externa para ler os dados.
Acesso externo à tabela subjacente ao armazenamento do Azure utilizando a credencial de âmbito de base de dados definida na fonte de dados utilizando as seguintes regras:
- Fonte de dados sem credencial permite que tabelas externas acedam a ficheiros publicamente disponíveis no armazenamento Azure.
- A fonte de dados pode ter credencial que permite que as tabelas externas acedam apenas aos ficheiros do armazenamento Azure utilizando o token SAS ou o espaço de trabalho Identidade Gerida - Por exemplo, consulte o artigo [de controlo de acesso a ficheiros de armazenamento de armazenamento.](develop-storage-files-storage-access-control.md#examples)

> [!IMPORTANT]
> No pool DE SQL dedicado, uma fonte de dados criada sem uma credencial permite que os utilizadores de Azure AD acedam a ficheiros de armazenamento usando a sua identidade AZure AD. Na piscina SQL sem servidor, você precisa criar uma fonte de dados com uma credencial de base de dados que tem `IDENTITY='User Identity'` propriedade - veja [exemplos aqui](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>CRIAR FONTE DE DADOS EXTERNA

Fontes de dados externas são usadas para se ligarem a contas de armazenamento. A documentação completa está delineada [aqui.](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

### <a name="syntax-for-create-external-data-source"></a>Sintaxe para criar fonte de dados externos

#### <a name="dedicated-sql-pool"></a>[Piscina SQL dedicada](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="serverless-sql-pool"></a>[Piscina SQL sem servidor](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argumentos para criar fonte de dados externos

data_source_name

Especifica o nome definido pelo utilizador para a fonte de dados. O nome deve ser único na base de dados.

#### <a name="location"></a>Localização
LOCALIZAÇÃO = `'<prefix>://<path>'`   - Fornece o protocolo de conectividade e o caminho para a fonte de dados externa. Os seguintes padrões podem ser utilizados no local:

| Fonte de Dados Externos        | Prefixo de localização | Caminho de localização                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Armazenamento de Blobs do Azure          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Armazenamento de Blobs do Azure          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` o prefixo permite-lhe utilizar a sub-dobragem no caminho.

#### <a name="credential"></a>Credencial
CREDENCIAL = `<database scoped credential>` é credencial opcional que será usada para autenticar no armazenamento Azure. Fonte externa de dados sem credencial pode aceder à conta de armazenamento público. 

Fontes de dados externas sem credencial em pool DE SQL dedicado utilizarão a identidade AZure AD do chamador para aceder a ficheiros no armazenamento. Uma fonte de dados externa para o pool SQL sem servidor com credencial  `IDENTITY='User Identity'` utilizará a identidade AD do chamador para aceder a ficheiros.
- No pool DE SQL dedicado, a credencial de âmbito de base de dados pode especificar a identidade personalizada da aplicação, o espaço de trabalho Identidade Gerida ou a chave SAK. 
- Na piscina SQL sem servidor, a credencial de área de dados pode especificar a identidade AD do chamador, a identidade gerida do espaço de trabalho ou a chave SAS. 

#### <a name="type"></a>TIPO
TYPE = `HADOOP` é a opção obrigatória na piscina DE SQL dedicada e especifica que a tecnologia Polybase é usada para aceder a ficheiros subjacentes. Este parâmetro não pode ser usado em piscina SQL sem servidor que usa leitor nativo incorporado.

### <a name="example-for-create-external-data-source"></a>Exemplo para CRIAR FONTE DE DADOS EXTERNA

#### <a name="dedicated-sql-pool"></a>[Piscina SQL dedicada](#tab/sql-pool)

O exemplo a seguir cria uma fonte de dados externa para o Azure Data Lake Gen2 que aponta para o conjunto de dados de Nova Iorque:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="serverless-sql-pool"></a>[Piscina SQL sem servidor](#tab/sql-on-demand)

O exemplo a seguir cria uma fonte de dados externa para o Azure Data Lake Gen2 que pode ser acedido através da credencial SAS:

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

O exemplo a seguir cria uma fonte de dados externa para o Azure Data Lake Gen2 que aponta para o conjunto de dados de Nova Iorque disponível ao público:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Cria um objeto de formato de ficheiro externo que define dados externos armazenados no Azure Blob Storage ou no Azure Data Lake Storage. A criação de um formato de ficheiro externo é um pré-requisito para a criação de uma tabela externa. A documentação completa está [aqui.](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Ao criar um formato de ficheiro externo, especifica o layout real dos dados referenciados por uma tabela externa.

### <a name="syntax-for-create-external-file-format"></a>Sintaxe para criar formato de ficheiro externo

#### <a name="sql-pool"></a>[Conjunto de SQL](#tab/sql-pool)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

#### <a name="serverless-sql-pool"></a>[Piscina SQL sem servidor](#tab/sql-on-demand)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

---


### <a name="arguments-for-create-external-file-format"></a>Argumentos para criar formato de ficheiro externo

file_format_name- Especifica um nome para o formato de ficheiro externo.

FORMAT_TYPE = [ PARQUET ] DELIMITEDTEXT]- Especifica o formato dos dados externos.

- PARQUET - Especifica um formato Parquet.
- DELIMITEDTEXT - Especifica um formato de texto com delimiters de coluna, também chamados exterminadores de campo.

FIELD_TERMINATOR = *field_terminator* - Aplica-se apenas a ficheiros de texto delimitados. O exterminador de campo especifica um ou mais caracteres que marcam a extremidade de cada campo (coluna) no ficheiro delimitado por texto. O padrão é o caractere do tubo (ꞌ/ꞌ).

Exemplos:

- FIELD_TERMINATOR = '''
- FIELD_TERMINATOR = '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* - Especifica o exterminador de campo para dados de tipo de cadeia no ficheiro delimitado por texto. O delimiter de cordas tem um ou mais caracteres de comprimento e é fechado com citações individuais. O padrão é a corda vazia ("").

Exemplos:

- STRING_DELIMITER = '''
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* - Especifica o número de linha que é lido primeiro e se aplica a todos os ficheiros. Definir o valor em dois faz com que a primeira linha em cada ficheiro (linha do cabeçalho) seja ignorada quando os dados são carregados. As linhas são ignoradas com base na existência de terminadores de linha (/r/n, /r, /n).

USE_TYPE_DEFAULT = { TRUE **FALSO}** - Especifica como lidar com valores em falta em ficheiros de texto delimitados ao recuperar dados do ficheiro de texto.

VERDADEIRO - Se estiver a recolher dados do ficheiro de texto, guarde cada valor em falta utilizando o tipo de dados do valor predefinido para a coluna correspondente na definição da tabela externa. Por exemplo, substitua um valor em falta por:

- 0 se a coluna for definida como uma coluna numérica. As colunas decimais não são suportadas e causarão um erro.
- Corda vazia (")se a coluna for uma coluna de cordas.
- 1900-01-01 se a coluna for uma coluna de data.

FALSO - Guarde todos os valores em falta como NULO. Quaisquer valores NULOS que sejam armazenados utilizando a palavra NULO no ficheiro de texto delimitado são importados como o string 'NULL'.

Codificação = {'UTF8' 'UTF16'} - A piscina SQL sem servidor pode ler ficheiros de texto delimitados UTF8 e UTF16 codificados.

DATA_COMPRESSION = *data_compression_method* - Este argumento especifica o método de compressão de dados para os dados externos. 

O tipo de formato de ficheiro PARQUET suporta os seguintes métodos de compressão:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

Ao ler as tabelas externas do PARQUET, este argumento é ignorado, mas é utilizado ao escrever para mesas externas utilizando [o CETAS](develop-tables-cetas.md).

O tipo de formato de ficheiro DELIMITEDTEXT suporta o seguinte método de compressão:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = 'parser_version' Especifica a versão parser a ser utilizada ao ler ficheiros. Verifique PARSER_VERSION argumento nos [argumentos da OPENROWSET](develop-openrowset.md#arguments) para obter mais detalhes.

### <a name="example-for-create-external-file-format"></a>Exemplo para CRIAR FORMATO DE FICHEIRO EXTERNO

O exemplo a seguir cria um formato de ficheiro externo para ficheiros de recenseamento:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CRIAR TABELA EXTERNA

O comando CREATE EXTERNAL TABLE cria uma tabela externa para o Sinapse SQL aceder aos dados armazenados no Azure Blob Storage ou no Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Sintaxe para criar tabela externa

```sql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

### <a name="arguments-create-external-table"></a>Argumentos CRIAM TABELA EXTERNA

*{ database_name.schema_name.table_name / schema_name.table_name [ table_name }*

O nome de uma a três partes da mesa para criar. Para uma tabela externa, a piscina SQL sem servidor armazena apenas os metadados da tabela. Nenhum dado real é movido ou armazenado em piscina SQL sem servidor.

<column_definition>... *n* ]

CRIAR TABELA EXTERNA suporta a capacidade de configurar o nome da coluna, o tipo de dados, a nulidade e a colagem. Não é possível utilizar o RESTRIÇÃO PADRÃO em tabelas externas.

>[!IMPORTANT]
>As definições de coluna, incluindo os tipos de dados e o número de colunas, têm de corresponder aos dados nos ficheiros externos. Se existir um erro de correspondência, as linhas do ficheiro serão rejeitadas ao consultar os dados reais.

Ao ler os ficheiros Parquet, pode especificar apenas as colunas que pretende ler e saltar as restantes.

LOCALIZAÇÃO = ' *folder_or_filepath* '

Especifica a pasta ou o caminho do ficheiro e o nome do ficheiro para os dados reais no Azure Blob Storage. A localização começa a partir da pasta raiz. A pasta raiz é a localização de dados especificada na origem de dados externa.

Se especificar uma pasta LOCATION, uma consulta de piscina SQL sem servidor selecionará a partir da tabela externa e recuperará ficheiros da pasta.

> [!NOTE]
> Ao contrário de Hadoop e PolyBase, a piscina SQL sem servidor não devolve subpastas. Devolve ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.).

Neste exemplo, se o LOCATION='/webdata/', uma consulta de piscina SQL sem servidor, regressará às linhas de mydata.txt e _hidden.txt. Não vai voltar mydata2.txt e mydata3.txt porque estão localizados numa sub-página.

![Dados recursivos para tabelas externas](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* - Especifica o nome da fonte de dados externa que contém a localização dos dados externos. Para criar uma fonte de dados externa, utilize [CREATE EXTERNAL DATA SOURCE](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* - Especifica o nome do objeto de formato de ficheiro externo que armazena o tipo de ficheiro e o método de compressão para os dados externos. Para criar um formato de ficheiro externo, utilize [o FORMATO DE FICHEIRO EXTERNO](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Permissões CRIAR TABELA EXTERNA

Para selecionar a partir de uma tabela externa, precisa de credenciais adequadas com a lista e ler permissões.

### <a name="example-create-external-table"></a>EXEMPLO CRIAR TABELA EXTERNA

O exemplo a seguir cria uma tabela externa. Devolve a primeira linha:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Criar e consultar tabelas externas a partir de um ficheiro no Lago de Dados Azure

Utilizando as capacidades de exploração do Data Lake, pode agora criar e consultar uma tabela externa utilizando uma piscina SQL dedicada ou piscina SQL sem servidor com um simples clique à direita no ficheiro.

### <a name="prerequisites"></a>Pré-requisitos

- Tem de ter acesso ao espaço de trabalho com, pelo menos, a função de acesso arm-contribuinte de dados de armazenamento à Conta ADLS Gen2

- Você deve ter pelo menos [permissões para criar](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2&preserve-view=true) e consultar mesas externas na piscina SQL ou SQL OD

- O serviço ligado associado à Conta ADLS Gen2 **deve ter acesso ao ficheiro.** Por exemplo, se o mecanismo de autenticação do serviço ligado for identidade gerida, o espaço de trabalho Identidade Gerida deve ter pelo menos permissão do leitor blob de armazenamento na conta de armazenamento

A partir do painel de dados, selecione o ficheiro que pretende criar a tabela externa a partir de:
> [!div class="mx-imgBorder"]
>![externa1](./media/develop-tables-external-tables/external-table-1.png)

Uma janela de diálogo abrir-se-á. Selecione piscina SQL dedicada ou piscina SQL sem servidor, dê um nome à mesa e selecione script aberto:

> [!div class="mx-imgBorder"]
>![externa2](./media/develop-tables-external-tables/external-table-2.png)

O Script SQL é autogerado inferindo o esquema a partir do ficheiro:
> [!div class="mx-imgBorder"]
>![externa3](./media/develop-tables-external-tables/external-table-3.png)

Execute o script. O script será executado automaticamente um Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externa4](./media/develop-tables-external-tables/external-table-4.png)

A tabela externa é agora criada, para futura exploração do conteúdo desta tabela externa, o utilizador pode questioná-lo diretamente a partir do painel de dados:
> [!div class="mx-imgBorder"]
>![externa5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Passos seguintes

Verifique o artigo [CETAS](develop-tables-cetas.md) sobre como guardar os resultados da consulta para uma tabela externa no Azure Storage. Ou pode começar a consultar o [Apache Spark para as tabelas externas do Azure Synapse.](develop-storage-files-spark-tables.md)

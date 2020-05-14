---
title: Utilize tabelas externas com SYnapse SQL
description: Ler ou escrever ficheiros de dados com synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 0405644af24eb277aa47db64348c9a217cf72239
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195971"
---
# <a name="use-external-tables-with-synapse-sql"></a>Utilize tabelas externas com SYnapse SQL

Uma tabela externa aponta para dados localizados em Hadoop, Azure Storage blob ou Azure Data Lake Storage. As tabelas externas são utilizadas para ler dados de ficheiros ou escrever dados para ficheiros no Armazenamento Do Azure. Com o Synapse SQL, pode utilizar tabelas externas para ler e escrever dados para piscina SQL ou SQL on-demand (pré-visualização).

## <a name="external-tables-in-synapse-sql"></a>Tabelas externas em Synapse SQL

### <a name="sql-pool"></a>[Conjunto de SQL](#tab/sql-pool)

Na piscina SQL, pode utilizar uma tabela externa para:

- Consulta Azure Blob Storage e Azure Data Lake Gen2 com declarações transact-SQL.
- Importar e armazenar dados do Armazenamento De Azure Blob e do Armazenamento do Lago Azure Data na piscina SQL.

Quando utilizado em conjunto com a declaração [CREATE TABLE AS SELECT,](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) selecionando a partir de uma tabela externa importa dados para uma tabela dentro do pool SQL. Em adição à [declaração copy,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)as tabelas externas são úteis para o carregamento de dados. Para um tutorial de carregamento, consulte [Use PolyBase para carregar dados do Armazenamento De Blob Azure](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="sql-on-demand"></a>[SQL a pedido](#tab/sql-ondemand)

Para a SQL a pedido, você usará uma tabela externa para:

- Dados de consulta em Armazenamento de Blob Azure ou Armazenamento de Lagos De Dados Azure com declarações transact-SQL
- Guarde os resultados da consulta a pedido da SQL para ficheiros em Armazenamento de Blob Azure ou armazenamento de lagos de dados azure utilizando [CETAS](develop-tables-cetas.md).

Pode criar tabelas externas utilizando o SQL a pedido através dos seguintes passos:

1. CRIAR FONTE DE DADOS EXTERNAS
2. CREATE EXTERNAL FILE FORMAT
3. CRIAR TABELA EXTERNA

---

### <a name="security"></a>Segurança

O utilizador deve ter `SELECT` permissão na mesa externa para ler os dados.
Acesso à tabela externa subjacente ao armazenamento do Azure utilizando a credencial de base de dados definida na fonte de dados utilizando as seguintes regras:
- Fonte de dados sem credenciais permite que as tabelas externas acedam a ficheiros publicamente disponíveis no armazenamento do Azure.
- A fonte de dados pode ter credenciais que permitem que as tabelas externas acedam apenas aos ficheiros no armazenamento do Azure utilizando token SAS ou workspace Managed Identity - consulte [exemplos aqui](develop-storage-files-storage-access-control.md#examples).

> [!IMPORTANT]
> No pool SQL, o datasource sem creatrepermite que o utilizador da AD Azure aceda a ficheiros de armazenamento utilizando a sua identidade Azure AD. No SQL on-demand, é necessário criar fonte de dados com credenciais com base de dados que tem `IDENTITY='User Identity'` propriedade - consulte [exemplos aqui](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>CRIAR FONTE DE DADOS EXTERNAS

Fontes de dados externas são utilizadas para se conectarem a contas de armazenamento. A documentação completa está [aqui](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)descrita.

### <a name="syntax-for-create-external-data-source"></a>Sintaxe para criar FONTE DE DADOS EXTERNOS

#### <a name="sql-pool"></a>[Conjunto de SQL](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="sql-on-demand"></a>[SQL a pedido](#tab/sql-ondemand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```
---

### <a name="arguments-for-create-external-data-source"></a>Argumentos para criar FONTE DE DADOS EXTERNOS

data_source_name -Especifica o nome definido pelo utilizador para a fonte de dados. O nome deve ser único dentro da base de dados.

#### <a name="location"></a>Localização
LOCALIZAÇÃO = `'<prefix>://<path>'` - Fornece o protocolo de conectividade e o caminho para a fonte externa de dados. O caminho pode incluir um recipiente na forma de `'<prefix>://<path>/container'` , e uma pasta na forma de `'<prefix>://<path>/container/folder'` .

| Fonte de Dados Externos        | Prefixo de localização | Percurso de localização                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Armazenamento de Blobs do Azure          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Data Lake Store Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Store Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

#### <a name="credential"></a>Credencial
CREDENTIAL = `<database scoped credential>` é credencial opcional que será usada para autenticar no armazenamento Azure. Fonte de dados externa sem credencial pode aceder à conta de armazenamento público. Fontes de dados externas sem credenciais no pool SQL também podem usar a identidade do IdA para aceder a ficheiros no armazenamento. Fonte externa de dados com identidade de utilização credencial especificada na credencial para aceder a ficheiros.
- No pool SQL, a credencial de base de dados pode especificar identidade de aplicação personalizada, workspace Identidade Gerida ou chave SAK. 
- No SQL on-demand, a credencial de base de dados pode especificar a identidade Azure AD do chamador, a identidade gerida do espaço de trabalho ou a chave SAS. 

#### <a name="type"></a>TIPO
TYPE = `HADOOP` é opção obrigatória no pool SQL e especifica que a tecnologia Polybase é usada para aceder a ficheiros subjacentes. Este parâmetro não pode ser utilizado no serviço SQL on-demand que utiliza leitor nativo incorporado.

### <a name="example-for-create-external-data-source"></a>Exemplo para criar FONTE DE DADOS EXTERNOS

#### <a name="sql-pool"></a>[Conjunto de SQL](#tab/sql-pool)

O exemplo seguinte cria uma fonte externa de dados para o Azure Data Lake Gen2 apontando para o conjunto de dados de Nova Iorque:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="sql-on-demand"></a>[SQL a pedido](#tab/sql-ondemand)

O exemplo seguinte cria uma fonte externa de dados para o Lago de Dados Azure Gen2 que pode ser acedido usando a credencial SAS:

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

O exemplo seguinte cria uma fonte externa de dados para o Azure Data Lake Gen2 que aponta para o conjunto de dados de Nova Iorque disponível ao público:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Cria um objeto de formato de ficheiro externo que define dados externos armazenados no Armazenamento de Blob Azure ou no Armazenamento do Lago Azure Data. A criação de um formato de ficheiro externo é um pré-requisito para a criação de uma tabela externa. A documentação completa está [aqui.](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Ao criar um formato de ficheiro externo, especifice o layout real dos dados referenciados por uma tabela externa.

### <a name="syntax-for-create-external-file-format"></a>Sintaxe para criar formato de arquivo externo

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

### <a name="arguments-for-create-external-file-format"></a>Argumentos para criar formato de arquivo externo

file_format_name- Especifica um nome para o formato de ficheiro externo.

FORMAT_TYPE = [ PARQUET ] DELIMITEDTEXT]- Especifica o formato dos dados externos.

- PARQUET - Especifica um formato Parquet.
- DELIMITEDTEXT - Especifica um formato de texto com delimitadores de colunas, também chamados exterminadores de campo.

FIELD_TERMINATOR = *field_terminator* - Aplica-se apenas a ficheiros de texto delimitados. O exterminador de campo especifica um ou mais caracteres que marcam a extremidade de cada campo (coluna) no ficheiro delimitado por texto. O predefinido é o carácter do tubo ([...].

Exemplos:

- FIELD_TERMINATOR = '[].
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR =

STRING_DELIMITER = *string_delimiter* - Especifica o exterminador de campo para dados de cadeia de tipo no ficheiro delimitado por texto. O delimitador de cordas tem um ou mais caracteres de comprimento e é incluído com citações únicas. O padrão é a corda vazia ("").

Exemplos:

- STRING_DELIMITER = '''
- STRING_DELIMITER = '*'
- STRING_DELIMITER =

FIRST_ROW = *First_row_int* - Especifica o número da linha que é lido primeiro e se aplica a todos os ficheiros. A definição do valor para dois faz com que a primeira linha em cada ficheiro (linha de cabeçalho) seja ignorada quando os dados são carregados. As linhas são ignoradas com base na existência de exterminadores de linha (/r/n, /r, /n).

USE_TYPE_DEFAULT = = VERDADE / **FALSO** } - Especifica como lidar com valores em falta em ficheiros de texto delimitados ao recuperar dados do ficheiro de texto.

VERDADE - Se estiver a recuperar dados do ficheiro de texto, guarde cada valor em falta utilizando o tipo de dados do valor predefinido para a coluna correspondente na definição de tabela externa. Por exemplo, substitua um valor em falta com:

- 0 se a coluna for definida como uma coluna numérica. As colunas decimais não são suportadas e causarão um erro.
- Corda vazia (") se a coluna for uma coluna de cordas.
- 1900-01-01 se a coluna for uma coluna de data.

FALSO - Guarde todos os valores em falta como NULO. Quaisquer valores NULOS armazenados utilizando a palavra NULO no ficheiro de texto delimitado são importados como a corda 'NULO'.

Codificação = {'UTF8' [ 'UTF16'} - SQL a pedido pode ler uTF8 e UTF16 códigos de texto delimitados.

DATA_COMPRESSION = *data_compression_method* - Este argumento especifica o método de compressão de dados para os dados externos. 

O tipo de formato de ficheiro PARQUET suporta os seguintes métodos de compressão:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

Ao ler a partir de tabelas externas DO PARQUET, este argumento é ignorado, mas é utilizado ao escrever para tabelas externas utilizando o [CETAS](develop-tables-cetas.md).

O tipo de formato de ficheiro DELIMITEDTEXT suporta o seguinte método de compressão:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

### <a name="example-for-create-external-file-format"></a>Exemplo para criar formato de ficheiro externo

O exemplo seguinte cria um formato de ficheiro externo para ficheiros de recenseamento:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CRIAR TABELA EXTERNA

O comando CREATE EXTERNAL TABLE cria uma tabela externa para a Synapse SQL aceder aos dados armazenados no Armazenamento de Blob Azure ou no Armazenamento de Lagos Azure Data. 

### <a name="syntax-for-create-external-table"></a>Sintaxe para criar mesa externa

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

### <a name="arguments-create-external-table"></a>Argumentos CRIAM MESA EXTERNA

*{ database_name.schema_name.table_name / schema_name.table_name / table_name }*

O nome de uma a três partes da mesa para criar. Para uma tabela externa, a SQL a pedido armazena apenas os metadados da tabela. Nenhum dado real é movido ou armazenado em SQL a pedido.

<column_definition>, ... *n* ]

CRIAR TABELA EXTERNA suporta a capacidade de configurar o nome da coluna, o tipo de dados, a nulidade e a colagem. Não é possível utilizar o PRETOPRE EM tabelas externas.

>[!IMPORTANT]
>As definições de coluna, incluindo os tipos de dados e o número de colunas, têm de corresponder aos dados nos ficheiros externos. Se existir um erro de correspondência, as linhas do ficheiro serão rejeitadas ao consultar os dados reais.

Ao ler os ficheiros Parquet, só pode especificar as colunas que pretende ler e saltar o resto.

LOCALIZAÇÃO = '*folder_or_filepath*'

Especifica a pasta ou o caminho do ficheiro e o nome do ficheiro para os dados reais no Armazenamento De Blob Azure. A localização começa a partir da pasta raiz. A pasta raiz é a localização de dados especificada na origem de dados externa.

Se especificar uma pasta LOCALIZAÇÃO, uma consulta a pedido sQL selecionará a partir da tabela externa e recuperará ficheiros da pasta.

> [!NOTE]
> Ao contrário de Hadoop e PolyBase, a SQL a pedido não devolve subpastas. Devolve ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.).

Neste exemplo, se O LOCATION='/webdata/', uma consulta a pedido da SQL, devolver as linhas do mydata.txt e _hidden.txt. Não devolverá o mydata2.txt e o mydata3.txt porque estão localizados numa subpasta.

![Dados recursivos para tabelas externas](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* - Especifica o nome da fonte externa de dados que contém a localização dos dados externos. Para criar uma fonte de dados externa, utilize [a CREATE EXTERNAL DATA SOURCE](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* - Especifica o nome do objeto de formato de ficheiro externo que armazena o tipo de ficheiro e o método de compressão para os dados externos. Para criar um formato de ficheiro externo, utilize o [FORMATO DE FICHEIRO EXTERNO](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Permissões CRIAM MESA EXTERNA

Para selecionar a partir de uma mesa externa, precisa de credenciais adequadas com lista e leia permissões.

### <a name="example-create-external-table"></a>Exemplo CRIAR MESA EXTERNA

O exemplo seguinte cria uma tabela externa. Devolve a primeira fila:

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

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Criar e consultar tabelas externas a partir de um arquivo no Lago de Dados Azure

Utilizando as capacidades de exploração do Data Lake, pode agora criar e consultar uma tabela externa utilizando piscina SQL ou SQL on-demand com um simples clique direito no ficheiro.

### <a name="prerequisites"></a>Pré-requisitos

- Você deve ter acesso ao espaço de trabalho com pelo menos a função de acesso ARM de contribuinte de armazenamento blob de dados para a Conta ADLS Gen2

- Você deve ter pelo menos [permissões para criar](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) e consultar tabelas externas na piscina SQL ou SQL OD

- O serviço vinculado associado à Conta ADLS Gen2 **deve ter acesso ao ficheiro**. Por exemplo, se o mecanismo de autenticação do serviço ligado for identidade gerida, a identidade gerida pelo espaço de trabalho deve ter pelo menos permissão do leitor de armazenamento blob na conta de armazenamento

A partir do painel Dados, selecione o ficheiro que pretende criar a tabela externa a partir de:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Uma janela de diálogo abre-se. Selecione piscina SQL ou SQL on-demand, dê um nome à mesa e selecione script aberto:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

O Script SQL é gerado automaticamente inferindo o esquema do ficheiro:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Execute o script. O script executará automaticamente um Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

A tabela externa é agora criada, para futura exploração do conteúdo desta tabela externa, o utilizador pode consulta-lo diretamente a partir do painel de dados:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Próximos passos

Consulte o artigo [cetas](develop-tables-cetas.md) sobre como guardar os resultados da consulta para uma tabela externa no Armazenamento Azure. Ou pode começar a consultar [mesas de Faísca.](develop-storage-files-spark-tables.md)

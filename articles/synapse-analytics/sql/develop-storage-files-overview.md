---
title: Ficheiros de armazenamento de consulta utilizando SQL on-demand (pré-visualização) dentro do Synapse SQL
description: Descreve ficheiros de armazenamento de consulta utilizando recursos SQL on-demand (pré-visualização) dentro do Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 204fd1b1a0a2984886684bbabf33dc7e73c1b45c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653533"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Ficheiros de armazenamento de consulta utilizando recursos sql on-demand (pré-visualização) dentro do Synapse SQL

A SQL on-demand (pré-visualização) permite-lhe consultar dados no seu lago de dados. Oferece uma área de superfície de consulta T-SQL que acomoda consultas de dados semi-estruturadas e não estruturadas.

Para consulta, são suportados os seguintes aspetos T-SQL:

- Área de superfície [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) completa, incluindo a maioria das funções SQL, operadores, e assim por diante.
- Criar tabela externa COMO SELECT[(CETAS)](develop-tables-cetas.md)cria uma [tabela externa](develop-tables-external-tables.md) e, em seguida, exporta, paralelamente, os resultados de uma declaração seleta transact-SQL para o Armazenamento Azure.

Para obter mais informações sobre o que é vs. o que não é suportado atualmente, leia o artigo sobre a procura da [SQL.](on-demand-workspace-overview.md)

Quando os utilizadores da AD Azure executam consultas, o padrão é para que as contas de armazenamento sejam acedidas através do protocolo de autenticação pass-through Azure AD. Como tal, os utilizadores serão personificados e as permissões verificadas ao nível do armazenamento. Pode [controlar o acesso](develop-storage-files-storage-access-control.md) ao armazenamento de acordo com as suas necessidades.

## <a name="extensions"></a>Extensões

Para suportar uma experiência suave para a consulta de dados que está localizado em ficheiros de Armazenamento Azure, a SQL on-demand utiliza a função [OPENROWSET](develop-openrowset.md) com capacidades adicionais:

- [Consulta de vários ficheiros ou pastas](#query-multiple-files-or-folders)
- [Formato de ficheiro PARQUET](#parquet-file-format)
- [Opções adicionais para trabalhar com texto delimitado (exterminador de campo, exterminador de linha, escape char)](#additional-options-for-working-with-delimited-text)
- [Leia um subconjunto escolhido de colunas](#read-a-chosen-subset-of-columns)
- [Inferência de esquema](#schema-inference)
- [função de nome de arquivo](#filename-function)
- [função de arquivo](#filepath-function)
- [Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Consulta de vários ficheiros ou pastas

Para executar uma consulta T-SQL sobre um conjunto de ficheiros dentro de uma pasta ou conjunto de pastas enquanto os trata como uma única entidade ou rowset, fornecer um caminho para uma pasta ou um padrão (usando wildcards) sobre um conjunto de ficheiros ou pastas.

Aplicam-se as seguintes regras:

- Os padrões podem aparecer em parte de um caminho de diretório ou em nome de ficheiro.
- Vários padrões podem aparecer no mesmo passo de diretório ou nome de ficheiro.
- Se houver vários wildcards, então os ficheiros dentro de todos os caminhos correspondentes serão incluídos no conjunto de ficheiros resultante.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Consulte [as pastas De consulta e vários ficheiros](query-folders-multiple-csv-files.md) para exemplos de utilização.

### <a name="parquet-file-format"></a>Formato de ficheiro PARQUET

Para consultar os dados de fonte do Parquet, utilize o FORMAT = 'PARQUET'

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Reveja o artigo de [ficheiros Query Parquet](query-parquet-files.md) para exemplos de utilização.

### <a name="additional-options-for-working-with-delimited-text"></a>Opções adicionais para trabalhar com texto delimitado

Estes parâmetros adicionais são introduzidos para trabalhar com ficheiros CSV (texto delimitado):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' Especifica o carácter no ficheiro que é usado para escapar a si próprio e todos os valores delimitadores no ficheiro. Se o personagem de fuga for seguido por um valor diferente de si mesmo ou de qualquer um dos valores delimitador, o personagem de fuga é abandonado ao ler o valor.
O parâmetro ESCAPE_CHAR será aplicado quer o FIELDQUOTE esteja ou não ativado. Não será usado para escapar ao personagem citando. O personagem citando é escapado com duas citações em alinhamento com o comportamento do Excel CSV.
- FIELDTERMINATOR ='field_terminator' Especifica o exterminador de campo a utilizar. O exterminador de campo padrão é uma vírem ("**,**")
- ROWTERMINATOR ='row_terminator' Especifica o exterminador de linha a utilizar. O exterminador de linha padrão é um personagem de linha nova: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Leia um subconjunto escolhido de colunas

Para especificar as colunas que pretende ler, pode fornecer uma cláusula com um COM opcional dentro da sua declaração OPENROWSET.

- Se existirem ficheiros de dados CSV, para ler todas as colunas, fornecer nomes de colunas e os seus tipos de dados. Se pretender um subconjunto de colunas, utilize números de ordinal para escolher as colunas dos ficheiros de dados originais por ordinal. As colunas ficarão ligadas pela designação ordinal.
- Se existirem ficheiros de dados do Parquet, forneça nomes de colunas que correspondam aos nomes das colunas nos ficheiros de dados originais. As colunas ficarão ligadas pelo nome.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Para obter amostras, consulte os [ficheiros CSV de leitura sem especificar todas as colunas](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Inferência de esquema

Ao omitir a cláusula COM da declaração OPENROWSET, pode instruir o serviço a detetar automaticamente (inferir) o esquema a partir de ficheiros subjacentes.

> [!NOTE]
> Isto funciona atualmente apenas para o formato de ficheiroS PARQUET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Certifique-se de que são utilizados tipos de [dados inferidos adequados](best-practices-sql-on-demand.md#check-inferred-data-types) para um desempenho ótimo. 

### <a name="filename-function"></a>Função nome de ficheiro

Esta função devolve o nome de ficheiro de que a linha é originária. 

Para consultar ficheiros específicos, leia a secção Filename no artigo de [ficheiros específicos](query-specific-files.md#filename) da Consulta.

O tipo de dados de devolução é nvarchar (1024). Para um desempenho ótimo, molde sempre o resultado da função de nome de ficheiro para o tipo de dados apropriado. Se utilizar o tipo de dados de caracteres, certifique-se de que o comprimento adequado é utilizado.

### <a name="filepath-function"></a>Função path de arquivo

Esta função devolve um caminho completo ou uma parte do caminho:

- Quando chamado sem parâmetro, devolve o caminho completo de arquivo de que uma linha provém.
- Quando chamado com parâmetro, devolve parte do caminho que corresponde ao wildcard na posição especificada no parâmetro. Por exemplo, o valor do parâmetro 1 devolveria parte do caminho que corresponde ao primeiro wildcard.

Para mais informações, leia a secção Filepath do artigo de [ficheiros específicos](query-specific-files.md#filepath) da Consulta.

O tipo de dados de devolução é nvarchar (1024). Para um desempenho ótimo, molde sempre o resultado da função de path path para o tipo de dados apropriado. Se utilizar o tipo de dados de caracteres, certifique-se de que o comprimento adequado é utilizado.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas

Para permitir uma experiência suave ao trabalhar com dados armazenados em tipos de dados aninhados ou repetidos, como nos ficheiros [Parquet,](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) a SQL on-demand adicionou as extensões abaixo.

#### <a name="project-nested-or-repeated-data"></a>Dados aninhados ou repetidos do projeto

Para projetar dados, faça uma declaração SELECT sobre o ficheiro Parquet que contenha colunas de tipos de dados aninhados. Na saída, os valores aninhados serão serializados em JSON e devolvidos como um tipo de dados SQL de varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Para obter informações mais detalhadas, consulte a secção de dados aninhada ou repetida do artigo de [tipos aninhados query Parquet.](query-parquet-nested-types.md#project-nested-or-repeated-data)

#### <a name="access-elements-from-nested-columns"></a>Elementos de acesso a partir de colunas aninhadas

Para aceder a elementos aninhados de uma coluna aninhada, como o Struct, use "notação de pontos" para concatenar nomes de campo no caminho. Forneça o caminho como column_name na cláusula COM da função OPENROWSET.

O exemplo do fragmento de sintaxe é o seguinte:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Por predefinição, a função OPENROWSET corresponde ao nome e caminho do campo de origem com os nomes de colunas fornecidos na cláusula COM. Os elementos contidos em diferentes níveis de nidificação dentro do mesmo ficheiro Parquet de origem podem ser acedidos através da cláusula COM.

**Valores de retorno**

- A função devolve um valor escalar, como int, decimal e varchar, do elemento especificado, e no caminho especificado, para todos os tipos de Parquet que não estejam no grupo Tipo Nested.
- Se o caminho apontar para um elemento de um Tipo Aninhado, a função devolve um fragmento JSON a partir do elemento superior no caminho especificado. O fragmento jSON é de tipo varchar (8000).
- Se a propriedade não puder ser encontrada no column_name especificado, a função devolve um erro.
- Se a propriedade não puder ser encontrada no column_path especificado, dependendo do [modo Path,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)a função devolve um erro quando em modo rígido ou nulo quando em modo laxista.

Para amostras de consulta, reveja os elementos de Acesso da secção colunas aninhadas no artigo [tipos aninhados query Parquet.](query-parquet-nested-types.md#access-elements-from-nested-columns)

#### <a name="access-elements-from-repeated-columns"></a>Elementos de acesso de colunas repetidas

Para aceder a elementos de uma coluna repetida, como um elemento de um Array ou Map, utilize a [função JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para cada elemento escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) que especifica o elemento ou propriedade a aceder, como um segundo parâmetro

Para aceder a elementos não escalar a partir de uma coluna repetida, utilize a [função JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para cada elemento não escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) que especifica o elemento ou propriedade a aceder, como um segundo parâmetro

Consulte o fragmento de sintaxe abaixo:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Pode encontrar amostras de consulta para aceder a elementos de colunas repetidas no artigo tipos [aninhados query Parquet.](query-parquet-nested-types.md#access-elements-from-repeated-columns)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre como consultar diferentes tipos de ficheiros e criar e utilizar pontos de vista, consulte os seguintes artigos:

- [Consulta single CSV arquivo](query-single-csv-file.md)
- [Consultar ficheiros de Parquet](query-parquet-files.md)
- [Consultar ficheiros JSON](query-json-files.md)
- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)
- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)
- [Utilizar metadados de ficheiros em consultas](query-specific-files.md)
- [Criar e utilizar vistas](create-use-views.md)

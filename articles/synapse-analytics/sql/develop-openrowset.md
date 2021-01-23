---
title: Como utilizar o OPENROWSET na piscina SQL sem servidor
description: Este artigo descreve a sintaxe de OPENROWSET na piscina SQL sem servidor e explica como usar argumentos.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2ead7291f52f33c271768ae2f470af65aca98030
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734752"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Como utilizar o OPENROWSET utilizando a piscina SQL sem servidor em Azure Synapse Analytics

A `OPENROWSET(BULK...)` função permite-lhe aceder a ficheiros no Azure Storage. `OPENROWSET` função lê o conteúdo de uma fonte de dados remoto (por exemplo, ficheiro) e devolve o conteúdo como um conjunto de linhas. Dentro do recurso de piscina SQL sem servidor, o fornecedor de conjunto de linha a granel OPENROWSET é acedido através da chamada da função OPENROWSET e especificando a opção BULK.  

A `OPENROWSET` função pode ser referenciada na `FROM` cláusula de uma consulta como se fosse uma nome de mesa `OPENROWSET` . Suporta operações a granel através de um fornecedor BULK incorporado que permite que os dados de um ficheiro sejam lidos e devolvidos como um conjunto de linhas.

## <a name="data-source"></a>Origem de dados

A função OPENROWSET na Synapse SQL lê o conteúdo dos ficheiros a partir de uma fonte de dados. A fonte de dados é uma conta de armazenamento Azure e pode ser explicitamente referenciada na `OPENROWSET` função ou pode ser deduzida dinamicamente a partir de URL dos ficheiros que pretende ler.
A `OPENROWSET` função pode, opcionalmente, conter um `DATA_SOURCE` parâmetro para especificar a fonte de dados que contém ficheiros.
- `OPENROWSET` sem `DATA_SOURCE` pode ser utilizado para ler diretamente o conteúdo dos ficheiros a partir da localização URL especificada como `BULK` opção:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Esta é uma forma rápida e fácil de ler o conteúdo dos ficheiros sem pré-configuração. Esta opção permite-lhe utilizar a opção de autenticação básica para aceder ao armazenamento (passe AD AD Azure para logins AD Azure e ficha SAS para logins SQL). 

- `OPENROWSET` com `DATA_SOURCE` pode ser usado para aceder a ficheiros na conta de armazenamento especificada:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Esta opção permite configurar a localização da conta de armazenamento na fonte de dados e especificar o método de autenticação que deve ser utilizado para aceder ao armazenamento. 
    
    > [!IMPORTANT]
    > `OPENROWSET` sem `DATA_SOURCE` uma forma rápida e fácil de aceder aos ficheiros de armazenamento, mas oferece opções de autenticação limitadas. Como exemplo, os principais da AD Azure podem aceder a ficheiros apenas utilizando a sua [identidade AZure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) ou ficheiros disponíveis publicamente. Se precisar de opções de autenticação mais poderosas, use `DATA_SOURCE` a opção e defina a credencial que pretende utilizar para aceder ao armazenamento.


## <a name="security"></a>Segurança

Um utilizador da base de dados deve ter `ADMINISTER BULK OPERATIONS` permissão para utilizar a `OPENROWSET` função.

O administrador de armazenamento também deve permitir que um utilizador aceda aos ficheiros fornecendo um token SAS válido ou permitindo que o Azure AD principal aceda a ficheiros de armazenamento. Saiba mais sobre o controlo de acesso ao armazenamento [neste artigo.](develop-storage-files-storage-access-control.md)

`OPENROWSET` utilizar as seguintes regras para determinar como autenticar para o armazenamento:
- Entrar `OPENROWSET` sem mecanismo de `DATA_SOURCE` autenticação depende do tipo de chamada.
  - Qualquer utilizador pode usar `OPENROWSET` sem `DATA_SOURCE` ler ficheiros disponíveis publicamente no armazenamento Azure.
  - Os logins AD do Azure podem aceder a ficheiros protegidos utilizando a sua própria [identidade AD Azure](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) se o armazenamento Azure permitir que o utilizador Azure AD aceda a ficheiros subjacentes (por exemplo, se o autor da chamada tiver `Storage Reader` permissão para o armazenamento do Azure).
  - Os logins SQL também podem ser utilizados `OPENROWSET` sem `DATA_SOURCE` aceder a ficheiros disponíveis ao público, ficheiros protegidos utilizando token SAS ou Identidade Gerida do espaço de trabalho synapse. Seria necessário [criar credenciais de âmbito de servidor](develop-storage-files-storage-access-control.md#examples) para permitir o acesso aos ficheiros de armazenamento. 
- Com o mecanismo de autenticação é definido na credencial de âmbito de `OPENROWSET` `DATA_SOURCE` base de dados atribuída à fonte de dados referenciada. Esta opção permite-lhe aceder ao armazenamento disponível ao público, ou ao armazenamento de acesso utilizando o token SAS, identidade gerida do espaço de trabalho ou [identidade AD AD do chamador](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (se o chamador for o Azure AD principal). Se `DATA_SOURCE` referenciar o armazenamento Azure que não é público, você precisaria [criar credencial de base de dados](develop-storage-files-storage-access-control.md#examples) e referenciar-lo `DATA SOURCE` para permitir o acesso a ficheiros de armazenamento.

O chamador deve ter `REFERENCES` permissão na credencial para usá-lo para autenticar para armazenamento.

## <a name="syntax"></a>Sintaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
```

## <a name="arguments"></a>Argumentos

Tem duas opções para ficheiros de entrada que contêm os dados-alvo para consulta. Os valores válidos são:

- 'CSV' - Inclui qualquer ficheiro de texto delimitado com separadores de linha/coluna. Qualquer personagem pode ser utilizado como separador de campo, como TSV: FIELDTERMINATOR = separador.

- 'PARQUET' - Arquivo binário em formato Parquet 

**'unstructured_data_path'**

O unstructured_data_path que estabelece um caminho para os dados pode ser um caminho absoluto ou relativo:
- O caminho absoluto no formato ' \<prefix> ' ' permite que um utilizador leia \<storage_account_path> / \<storage_path> diretamente os ficheiros.
- Percurso relativo no formato '<storage_path>' que deve ser utilizado com o `DATA_SOURCE` parâmetro e descreve o padrão de ficheiro dentro da localização <storage_account_path> definida em `EXTERNAL DATA SOURCE` . 

Abaixo encontrará os <storage account path> valores relevantes que irão ligar-se à sua fonte de dados externa. 

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Armazenamento de Blobs do Azure         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Armazenamento de Blobs do Azure         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /caminho/arquivo   |
| Azure Data Lake Store Gen2 | aufs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Especifica um caminho dentro do seu armazenamento que aponta para a pasta ou ficheiro que pretende ler. Se o caminho aponta para um recipiente ou pasta, todos os ficheiros serão lidos a partir desse recipiente ou pasta em particular. Os ficheiros nas sub-dobradeiras não serão incluídos. 

Pode utilizar wildcards para direcionar vários ficheiros ou pastas. É permitida a utilização de vários wildcards não consagrtivos.
Abaixo está um exemplo que lê todos os *ficheiros csv* começando com a *população* de todas as pastas começando com */csv/população*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Se especificar a unstructured_data_path ser uma pasta, uma consulta de piscina SQL sem servidor recuperará ficheiros dessa pasta. 

Pode instruir a piscina SQL sem servidor para atravessar pastas especificando /* no final do caminho como por exemplo: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Ao contrário de Hadoop e PolyBase, a piscina SQL sem servidor não devolve subpaminações a menos que especifique /** no final do caminho. Além disso, ao contrário de Hadoop e PolyBase, o pool SQL sem servidor devolveu ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.).

No exemplo abaixo, se o unstructured_data_path= `https://mystorageaccount.dfs.core.windows.net/webdata/` , uma consulta de piscina SQL sem servidor retornará as linhas de mydata.txt e _hidden.txt. Não vai voltar mydata2.txt e mydata3.txt porque estão localizados numa sub-página.

![Dados recursivos para tabelas externas](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A cláusula COM permite especificar colunas que pretende ler a partir de ficheiros.

- Para os ficheiros de dados do CSV, para ler todas as colunas, forneça nomes de colunas e seus tipos de dados. Se pretender um subconjunto de colunas, utilize números ordinais para recolher as colunas dos ficheiros de dados originários por ordinal. As colunas ficarão vinculadas pela designação ordinal. Se HEADER_ROW = TRUE é utilizado, então a ligação da coluna é feita pelo nome da coluna em vez da posição ordinal.
    > [!TIP]
    > Também pode omitir a cláusula COM para ficheiros CSV. Os tipos de dados serão automaticamente inferidos a partir do conteúdo do ficheiro. Pode utilizar HEADER_ROW argumento para especificar a existência da linha do cabeçalho, caso em que os nomes das colunas serão lidos a partir da linha do cabeçalho. Para mais detalhes, verifique [a descoberta automática do esquema](#automatic-schema-discovery).
    
- Para ficheiros de dados do Parquet, forneça nomes de colunas que correspondam aos nomes das colunas nos ficheiros de dados originários. As colunas serão ligadas pelo nome e são sensíveis ao caso. Se a cláusula WITH for omitida, todas as colunas dos ficheiros Parquet serão devolvidas.
    > [!IMPORTANT]
    > Os nomes das colunas nos ficheiros Parquet são sensíveis ao caso. Se especificar o nome da coluna com invólucro diferente do invólucro de nome de coluna no ficheiro Parquet, os valores NULL serão devolvidos para essa coluna.


column_name = Nome para a coluna de saída. Se fornecido, este nome substitui o nome da coluna no ficheiro de origem e no nome da coluna fornecido no caminho JSON, se houver um. Se json_path não for fornecida, será automaticamente adicionada como "$.column_name". Verifique json_path argumento de comportamento.

column_type = Tipo de dados para a coluna de saída. A conversão implícita do tipo de dados terá lugar aqui.

column_ordinal = Número ordinal da coluna nos ficheiros de origem. Este argumento é ignorado para os ficheiros Parquet, uma vez que a ligação é feita pelo nome. O exemplo a seguir devolveria uma segunda coluna apenas a partir de um ficheiro CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [expressão do caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) para coluna ou propriedade aninhada. O [modo de caminho](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) predefinido é laxante.

> [!NOTE]
> Em modo rigoroso, a consulta falhará com erro se o caminho fornecido não existir. Em modo laxista a consulta terá sucesso e a expressão do caminho JSON avaliará para NU.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Especifica o exterminador de campo a utilizar. O exterminador de campo predefinido é uma vírgula ("**,**").

ROWTERMINATOR ='row_terminator''

Especifica o exterminador de linha a utilizar. Se o exterminador de linha não for especificado, será utilizado um dos terminadores predefinidos. Os exterminadores predefinidos para PARSER_VERSION = '1.0' são \r\n, \n e \r. Os exterminadores predefinidos para PARSER_VERSION = '2.0' são \r\n e \n.

ESCAPE_CHAR = 'char'

Especifica o carácter no ficheiro que é usado para escapar a si mesmo e todos os valores delimiter no ficheiro. Se o personagem de fuga for seguido por um valor diferente de si mesmo, ou por qualquer um dos valores delimiter, o personagem de fuga é deixado cair ao ler o valor. 

O parâmetro ESCAPE_CHAR será aplicado independentemente de o FIELDQUOTE estar ou não ativado. Não será usado para escapar ao personagem citando. O personagem citando deve ser escapado com outro personagem citando. Citar o carácter só pode aparecer dentro do valor da coluna se o valor for encapsulado com caracteres citantes.

PRIMEIRA SOBRANCELHA = 'first_row' 

Especifica o número da primeira linha para carregar. O predefinido é 1 e indica a primeira linha no ficheiro de dados especificado. Os números da linha são determinados contando os exterminadores da linha. FirstROW é baseado em 1.

FIELDQUOTE = 'field_quote' 

Especifica um personagem que será usado como o personagem de citação no ficheiro CSV. Se não for especificado, o carácter de citação (") será utilizado. 

DATA_COMPRESSION = 'data_compression_method'

Especifica o método de compressão. Suportado apenas em PARSER_VERSION='1.0'. É suportado o seguinte método de compressão:

- GZIP

PARSER_VERSION = 'parser_version'

Especifica a versão parser a ser utilizada ao ler ficheiros. As versões de parser CSV atualmente suportadas são 1.0 e 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

A versão 1.0 do parser CSV é padrão e possui-se rica. A versão 2.0 foi construída para desempenho e não suporta todas as opções e codificações. 

Detalhes da versão 1.0 do parser CSV:

- As seguintes opções não são suportadas: HEADER_ROW.

Detalhes da versão 2.0 do parser CSV:

- Nem todos os tipos de dados são suportados.
- O limite máximo de tamanho da linha é de 8 MB.
- As seguintes opções não são suportadas: DATA_COMPRESSION.
- A corda vazia citada ("") é interpretada como uma corda vazia.
- Formato suportado para tipo de dados DATE: YYYYY-MM-DD
- Formato suportado para o tipo de dados TIME: HH:MM:SS[.fracional segundos]
- Formato suportado para tipo de dados DATETIME2: YYYY-MM-DD HH:MM:SS[.segundos fracionais]

HEADER_ROW = { TRUE | FALSO}

Especifica se o ficheiro CSV contém linha de cabeçalho. O padrão é FALSO. Apoiado em PARSER_VERSION='2.0'. Se VERDADEIRO, os nomes das colunas serão lidos da primeira linha de acordo com o argumento FIRSTROW. Se TRUE e schema forem especificados usando COM, a ligação dos nomes das colunas será feita por nome de coluna, não posições ordinais.

DATAFILETYPE = {'char' | 'widechar' }

Especifica a codificação: o carvão é utilizado para UTF8, o widechar é utilizado para ficheiros UTF16.

## <a name="fast-delimited-text-parsing"></a>Análise de texto delimitada rápida

Há duas versões deslimitadas de parser de texto que pode usar. A versão 1.0 do parser CSV é padrão e apresenta-se rica enquanto a versão parser 2.0 é construída para desempenho. A melhoria do desempenho no parser 2.0 provém de técnicas avançadas de análise e multi-rosca. A diferença de velocidade será maior à medida que o tamanho do ficheiro aumenta.

## <a name="automatic-schema-discovery"></a>Descoberta automática de esquemas

Pode facilmente consultar os ficheiros CSV e Parquet sem saber ou especificar o esquema omitindo a cláusula COM. Os nomes das colunas e os tipos de dados serão deduzidos dos ficheiros.

Os ficheiros parquet contêm metadados de coluna que serão lidos, os mapeamentos de tipo podem ser encontrados em [mapeamentos de tipo para Parquet](#type-mapping-for-parquet). Verifique a [leitura dos ficheiros parquet sem especificar o esquema](#read-parquet-files-without-specifying-schema) para amostras.

Para os ficheiros CSV, os nomes das colunas do CSV podem ser lidos a partir da linha do cabeçalho. Pode especificar se existe uma linha de cabeçalho utilizando HEADER_ROW argumento. Se HEADER_ROW = FALSO, serão usados nomes de colunas genéricas: C1, C2, ... CN onde n é número de colunas em arquivo. Os tipos de dados serão inferidos a partir das primeiras 100 linhas de dados. Verifique a [leitura dos ficheiros CSV sem especificar o esquema](#read-csv-files-without-specifying-schema) para amostras.

> [!IMPORTANT]
> Há casos em que o tipo de dados adequado não pode ser deduzido devido à falta de informação e o tipo de dados maior será usado em vez disso. Isto traz a sobrecarga de desempenho e é particularmente importante para as colunas de caracteres que serão inferidas como varchar(8000). Para obter um melhor desempenho, [verifique os tipos de dados inferidos](best-practices-sql-on-demand.md#check-inferred-data-types) e utilize tipos de [dados apropriados](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Tipo de mapeamento para Parquet

Os ficheiros parquet contêm descrições de tipo para cada coluna. A tabela a seguir descreve como os tipos de Parquet são mapeados para os tipos nativos SQL.

| Tipo parquet | Tipo lógico parquet (anotação) | Tipo de dados SQL |
| --- | --- | --- |
| BOOLEANA | | bit |
| BINÁRIO / BYTE_ARRAY | | varbinário |
| DUPLO | | float |
| FLUTUAR | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINÁRIO |UTF8 |varchar \* (colagem UTF8) |
| BINÁRIO |CORDA |varchar \* (colagem UTF8) |
| BINÁRIO |ENUM|varchar \* (colagem UTF8) |
| FIXED_LEN_BYTE_ARRAY |UUID |uniqueidentifier |
| BINÁRIO |DECIMAL |decimal |
| BINÁRIO |JSON |varchar(8000) \* (colagem UTF8) |
| BINÁRIO |Rio BSON | Não suportado |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVALO | Não suportado |
| INT32 |INT(8, verdade) |smallint |
| INT32 |INT(16, verdade) |smallint |
| INT32 |INT(32, verdade) |int |
| INT32 |INT(8, falso) |tinyint |
| INT32 |INT(16, falso) |int |
| INT32 |INT(32, falso) |bigint |
| INT32 |DATE |data |
| INT32 |DECIMAL |decimal |
| INT32 |TEMPO (MILLIS)|hora |
| INT64 |INT(64, verdade) |bigint |
| INT64 |INT(64, falso) |decimal (20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TEMPO (MICROS) |tempo - TEMPO (NANOS) não é suportado |
|INT64 |TIMETAMP (MILLIS / MICROS) |datatime2 - TIMESTAMP (NANOS) não é suportado |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar(8000), serializado em JSON |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAPA|varchar(8000), serializado em JSON |

## <a name="examples"></a>Exemplos

### <a name="read-csv-files-without-specifying-schema"></a>Ler ficheiros CSV sem especificar o esquema

O exemplo a seguir lê-se no ficheiro CSV que contém linha de cabeçalho sem especificar nomes de colunas e tipos de dados: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

O exemplo a seguir lê-se no ficheiro CSV que não contém linha de cabeçalho sem especificar nomes de colunas e tipos de dados: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Leia os ficheiros Parquet sem especificar o esquema

O exemplo a seguir devolve todas as colunas da primeira linha a partir do conjunto de dados de recenseamento, no formato Parquet, e sem especificar os nomes das colunas e os tipos de dados: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Ler colunas específicas a partir do ficheiro CSV

O exemplo a seguir devolve apenas duas colunas com os números ordinais 1 e 4 da população*.csv ficheiros. Como não há fila de cabeçalho nos ficheiros, começa a ler a partir da primeira linha:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Ler colunas específicas do arquivo Parquet

O exemplo a seguir devolve apenas duas colunas da primeira linha do conjunto de dados do recenseamento, no formato Parquet: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Especifique colunas usando caminhos JSON

O exemplo a seguir mostra como pode utilizar [expressões de trajetória JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) na cláusula COM e demonstra a diferença entre modos de caminho rigorosos e laxistas: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Próximos passos

Para obter mais amostras, consulte o [quickstart de armazenamento de dados](query-data-storage.md) de consulta para aprender a usar `OPENROWSET` para ler os formatos de ficheiro [CSV,](query-single-csv-file.md) [PARQUET](query-parquet-files.md)e [JSON.](query-json-files.md) Verifique as [melhores práticas](best-practices-sql-on-demand.md) para obter um desempenho ideal. Também pode aprender a guardar os resultados da sua consulta para o Azure Storage utilizando [o CETAS](develop-tables-cetas.md).
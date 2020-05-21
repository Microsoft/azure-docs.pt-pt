---
title: Como utilizar o OPENROWSET no SQL on-demand (pré-visualização)
description: Este artigo descreve a sintaxe da OPENROWSET na SQL on-demand (pré-visualização) e explica como usar argumentos.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 3861b981a1083b44e9cc522a01c50cf24f281e91
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702027"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Como utilizar o OPENROWSET com o SQL a pedido (pré-visualização)

A `OPENROWSET(BULK...)` função permite-lhe aceder a ficheiros no Armazenamento Azure. `OPENROWSET`função lê o conteúdo de uma fonte de dados remota (por exemplo, ficheiro) e devolve o conteúdo como um conjunto de linhas. Dentro do recurso SQL on-demand (pré-visualização), o fornecedor de linha de remo a granel OPENROWSET é acedido ligando para a função OPENROWSET e especificando a opção BULK.  

A `OPENROWSET` função pode ser referenciada na cláusula de uma consulta como se fosse um nome de `FROM` mesa `OPENROWSET` . Suporta operações a granel através de um fornecedor bulk incorporado que permite que os dados de um ficheiro sejam lidos e devolvidos como um conjunto de linhas.

## <a name="data-source"></a>Origem de dados

A função OPENROWSET no Synapse SQL lê o conteúdo dos ficheiros a partir de uma fonte de dados. A fonte de dados é uma conta de armazenamento Azure e pode ser explicitamente referenciada na `OPENROWSET` função ou pode ser dinferida dinamicamente a partir de URL dos ficheiros que pretende ler.
A `OPENROWSET` função pode conter opcionalmente um `DATA_SOURCE` parâmetro para especificar a fonte de dados que contém ficheiros.
- `OPENROWSET`sem `DATA_SOURCE` poder ser utilizado para ler diretamente o conteúdo dos ficheiros a partir da localização URL especificada como `BULK` opção:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

Esta é uma forma rápida e fácil de ler o conteúdo dos ficheiros sem pré-configuração. Esta opção permite-lhe utilizar a opção de autenticação básica para aceder ao armazenamento (passagem de AD Azure para logins Azure AD e token SAS para logins SQL). 

- `OPENROWSET`pode `DATA_SOURCE` ser utilizado para aceder a ficheiros sobre uma conta de armazenamento especificada:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```


    Esta opção permite configurar a localização da conta de armazenamento na fonte de dados e especificar o método de autenticação que deve ser usado para aceder ao armazenamento. 
    
    > [!IMPORTANT]
    > `OPENROWSET`sem `DATA_SOURCE` fornecer forma rápida e fácil de aceder aos ficheiros de armazenamento, mas oferece opções de autenticação limitadas. Como exemplo, o diretor da AD Azure só pode aceder a ficheiros utilizando a sua [identidade Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through) e não pode aceder a ficheiros disponíveis ao público. Se precisar de opções de autenticação mais poderosas, use `DATA_SOURCE` a opção e defina a credencial que pretende utilizar para aceder ao armazenamento.


## <a name="security"></a>Segurança

Um utilizador de base de dados deve ter `ADMINISTER BULK OPERATIONS` permissão para utilizar a `OPENROWSET` função.

O administrador de armazenamento também deve permitir que um utilizador aceda aos ficheiros fornecendo ficha SAS válida ou permitindo que o diretor da Azure AD aceda aos ficheiros de armazenamento. Saiba mais sobre o controlo de acesso ao armazenamento [neste artigo.](develop-storage-files-storage-access-control.md)

`OPENROWSET`Utilize as seguintes regras para determinar como autenticar o armazenamento:
- Dentro `OPENROWSET` sem mecanismo de autenticação depende do tipo de `DATA_SOURCE` chamada.
  - Os logins da AD Azure só podem aceder a ficheiros utilizando a sua própria [identidade Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) se o armazenamento do Azure permitir ao utilizador da AD Azure aceder a ficheiros subjacentes (por exemplo, se o autor da chamada tiver permissão do Leitor de Armazenamento no armazenamento) e se ativar a autenticação de passagem da [Azure AD](develop-storage-files-storage-access-control.md#force-azure-ad-pass-through) no serviço SYnapse SQL.
  - Os logins SQL também podem ser usados `OPENROWSET` sem `DATA_SOURCE` aceder a ficheiros publicamente disponíveis, ficheiros protegidos usando token SAS ou Espaço de trabalho Gerido do Synapse. Teria de [criar credenciais](develop-storage-files-storage-access-control.md#examples) com âmbito de servidor para permitir o acesso aos ficheiros de armazenamento. 
- No mecanismo de autenticação é definido na base de dados de `OPENROWSET` `DATA_SOURCE` credenciais de aplicação atribuída à fonte de dados referenciada. Esta opção permite-lhe aceder ao armazenamento disponível ao público ou ao armazenamento de acesso utilizando token SAS, Identidade Gerida do espaço de trabalho ou [identidade Azure AD do chamador](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (se o chamador for o principal da AD Azure). Se `DATA_SOURCE` referências ao armazenamento do Azure que não é público, terá de [criar credenciais](develop-storage-files-storage-access-control.md#examples) com um espaço de dados e referenciar para permitir o acesso aos ficheiros de `DATA SOURCE` armazenamento.

O chamador deve ter `REFERENCES` permissão na credencial para usá-lo para autenticar o armazenamento.

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
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>Argumentos

Tem duas opções para ficheiros de entrada que contêm os dados-alvo para consulta. Os valores válidos são:

- 'CSV' - Inclui qualquer ficheiro de texto delimitado com separadores de linha/coluna. Qualquer personagem pode ser usado como separador de campo, como TSV: FIELDTERMINATOR = separador.

- 'PARQUET' - Ficheiro binário em formato Parquet 

**'unstructured_data_path'**

O unstructured_data_path que estabeleça um caminho para os dados pode ser um caminho absoluto ou relativo:
- O caminho absoluto no \< 'prefixo>. storage_account_path> storage_path>' permite que \< um utilizador leia \< diretamente os ficheiros.
- Caminho relativo no formato '<storage_path>' que deve ser utilizado com o `DATA_SOURCE` parâmetro e descreve o padrão de ficheiro dentro da localização <storage_account_path> definida em `EXTERNAL DATA SOURCE` . 

 Abaixo encontrará os <storage account path> valores relevantes que se ligarão à sua fonte de dados externa específica. 

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Armazenamento de Blobs do Azure         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

\<'storage_path>'

 Especifica um caminho dentro do seu armazenamento que aponta para a pasta ou ficheiro que pretende ler. Se o caminho apontar para um recipiente ou pasta, todos os ficheiros serão lidos a partir desse recipiente ou pasta em particular. Os ficheiros em subpastas não serão incluídos. 

 Pode utilizar wildcards para direcionar vários ficheiros ou pastas. É permitido o uso de vários wildcards não consecutivos.
Abaixo está um exemplo que lê todos os *ficheiros cSV* começando com a *população* de todas as pastas começando com */csv/população:*  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Se especificar que o unstructured_data_path ser uma pasta, uma consulta a pedido do SQL irá recuperar ficheiros dessa pasta. 

> [!NOTE]
> Ao contrário de Hadoop e PolyBase, a SQL a pedido não devolve subpastas. Além disso, ao contrário de Hadoop e PloyBase, a SQL a pedido devolve ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.).

No exemplo abaixo, se o unstructured_data_path= `https://mystorageaccount.dfs.core.windows.net/webdata/` , uma consulta a pedido do SQL devolverá as filas do mydata.txt e _hidden.txt. Não devolverá o mydata2.txt e o mydata3.txt porque estão localizados numa subpasta.

![Dados recursivos para tabelas externas](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A cláusula COM permite especificar colunas que pretende ler a partir de ficheiros.

- Para ficheiros de dados CSV, para ler todas as colunas, fornecer nomes de colunas e os seus tipos de dados. Se pretender um subconjunto de colunas, utilize números de ordinal para escolher as colunas dos ficheiros de dados originais por ordinal. As colunas ficarão ligadas pela designação ordinal. 

    > [!IMPORTANT]
    > A cláusula COM é obrigatória para ficheiros CSV.
    >
    
- Para ficheiros de dados parquet, forneça nomes de colunas que correspondam aos nomes das colunas nos ficheiros de dados originais. As colunas ficarão ligadas pelo nome. Se a cláusula COM for omitida, todas as colunas dos ficheiros Parquet serão devolvidas.

column_name = Nome para a coluna de saída. Se fornecido, este nome substitui o nome da coluna no ficheiro fonte.

column_type = Tipo de dados para a coluna de saída. A conversão implícita do tipo de dados terá lugar aqui.

column_ordinal = Número ordinal da coluna nos ficheiros de origem. Este argumento é ignorado para ficheiros Parquet uma vez que a ligação é feita pelo nome. O exemplo seguinte devolveria uma segunda coluna apenas a partir de um ficheiro CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Especifica o exterminador de campo a utilizar. O exterminador de campo padrão é uma vírem ("**,**").

ROWTERMINATOR ='row_terminator''

Especifica o exterminador de linha a utilizar. Se o exterminador de linha não for especificado, um dos exterminadores padrão será utilizado. Os exterminadores predefinidos para PARSER_VERSION = '1.0' são \r\n, \n e \r. Os exterminadores predefinidos para PARSER_VERSION = '2.0' são \r\n e \n.

ESCAPE_CHAR = 'char'

Especifica o carácter no ficheiro que é usado para escapar a si próprio e todos os valores delimitadores no ficheiro. Se o personagem de fuga for seguido por um valor diferente de si mesmo, ou qualquer um dos valores delimitador, o personagem de fuga é abandonado ao ler o valor. 

O parâmetro ESCAPE_CHAR será aplicado independentemente de o FIELDQUOTE estar ou não ativado. Não será usado para escapar ao personagem citando. O personagem citando é escapado com duas citações em alinhamento com o comportamento do Excel CSV.

FIRSTROW = 'first_row' 

Especifica o número da primeira linha para carregar. A predefinição é 1. Isto indica a primeira linha no ficheiro de dados especificado. Os números das fileiras são determinados contando os exterminadores de fileiras. Firstrow é baseado em 1.

FIELDQUOTE = 'field_quote' 

Especifica um personagem que será usado como o personagem de citação no ficheiro CSV. Se não especificado, o caracteres de citação (") será utilizado. 

DATA_COMPRESSION = 'data_compression_method'

Especifica o método de compressão. É apoiado o seguinte método de compressão:

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = 'parser_version'

Especifica a versão parser a utilizar ao ler ficheiros. Atualmente, as versões de parser CSV suportadas são 1.0 e 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

A versão 1.0 do parser CSV é padrão e é rica em recursos, enquanto 2.0 é construído para o desempenho e não suporta todas as opções e codificações. 

CSV parser versão 2.0 especificações:

- Nem todos os tipos de dados são suportados.
- O limite máximo de tamanho da linha é de 8 MB.
- As seguintes opções não são apoiadas: DATA_COMPRESSION.
- A corda vazia citada (") é interpretada como uma corda vazia.

## <a name="examples"></a>Exemplos

O exemplo seguinte devolve apenas duas colunas com números de ordinal 1 e 4 dos ficheiros da população*.csv. Como não há uma linha de cabeçalho nos ficheiros, começa a ler a partir da primeira linha:

```sql
SELECT * 
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

O exemplo seguinte devolve todas as colunas da primeira linha do conjunto de dados de recenseamento no formato Parquet sem especificar nomes de colunas e tipos de dados: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

Se está a ter um erro a dizer que os ficheiros não podem ser listados, tem de permitir o acesso ao armazenamento público em Synapse SQL a pedido:
- Se estiver a utilizar um login SQL, precisa [de criar credenciais](develop-storage-files-storage-access-control.md#examples)com mira de servidor que permita o acesso ao armazenamento público .
- Se estiver a utilizar um diretor da AD Azure para aceder ao armazenamento público, terá de [criar credenciais](develop-storage-files-storage-access-control.md#examples) com âmbito de servidor que permita o acesso ao armazenamento público e desative a autenticação de passagem da [AD Azure.](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)

## <a name="next-steps"></a>Próximos passos

Para mais amostras, consulte o armazenamento de dados de [consulta rapidamente para](query-data-storage.md) aprender a usar os formatos de ficheiroS [CSV,](query-single-csv-file.md) [PARQUET](query-parquet-files.md)e [JSON.](query-json-files.md) Também pode aprender a guardar os resultados da sua consulta ao Armazenamento Azure utilizando o [CETAS](develop-tables-cetas.md).

---
title: Como utilizar o OPENROWSET no SQL on-demand (pré-visualização)
description: Este artigo descreve a sintaxe da OPENROWSET na SQL on-demand (pré-visualização) e explica como usar argumentos.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81680496"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Como utilizar o OPENROWSET com o SQL a pedido (pré-visualização)

A função OPENROWSET (BULK...) permite-lhe aceder a ficheiros no Armazenamento Azure. Dentro do recurso SQL on-demand (pré-visualização), o fornecedor de linha de remo a granel OPENROWSET é acedido ligando para a função OPENROWSET e especificando a opção BULK.  

A função OPENROWSET pode ser referenciada na cláusula FROM de uma consulta como se fosse um nome de mesa OPENROWSET. Suporta operações a granel através de um fornecedor bulk incorporado que permite que os dados de um ficheiro sejam lidos e devolvidos como um conjunto de linhas.

A OPENROWSET não é atualmente suportada na piscina SQL.

## <a name="syntax"></a>Sintaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argumentos

Tem duas opções para ficheiros de entrada que contêm os dados-alvo para consulta. Os valores válidos são:

- 'CSV' - Inclui qualquer ficheiro de texto delimitado com separadores de linha/coluna. Qualquer personagem pode ser usado como separador de campo, como TSV: FIELDTERMINATOR = separador.

- 'PARQUET' - Ficheiro binário em formato Parquet 

**'unstructured_data_path'**

O unstructured_data_path que estabelece um caminho para os dados é estruturado da seguinte forma:  
'prefixo\<>.\<storage_account_path\<>/ storage_path>'
 
 
 Abaixo encontrará os caminhos relevantes da conta de armazenamento que se ligarão à sua fonte de dados externa específica. 

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Armazenamento de Blobs do Azure         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

'storage_path\<>'

 Especifica um caminho dentro do seu armazenamento que aponta para a pasta ou ficheiro que pretende ler. Se o caminho apontar para um recipiente ou pasta, todos os ficheiros serão lidos a partir desse recipiente ou pasta em particular. Os ficheiros em subpastas não serão incluídos. 
 
 Pode utilizar wildcards para direcionar vários ficheiros ou pastas. É permitido o uso de vários wildcards não consecutivos.
Abaixo está um exemplo que lê todos os *ficheiros cSV* começando com a *população* de todas as pastas começando com */csv/população:*  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Se especificar que o unstructured_data_path ser uma pasta, uma consulta a pedido do SQL irá recuperar ficheiros dessa pasta. 

> [!NOTE]
> Ao contrário de Hadoop e PolyBase, a SQL a pedido não devolve subpastas. Além disso, ao contrário de Hadoop e PloyBase, a SQL a pedido devolve ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.).

No exemplo abaixo, se`https://mystorageaccount.dfs.core.windows.net/webdata/`o unstructured_data_path= , uma consulta a pedido da SQL devolverá as filas do mydata.txt e _hidden.txt. Não devolverá o mydata2.txt e o mydata3.txt porque estão localizados numa subpasta.

![Dados recursivos para tabelas externas](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A cláusula COM permite especificar colunas que pretende ler a partir de ficheiros.

- Para ficheiros de dados CSV, para ler todas as colunas, fornecer nomes de colunas e os seus tipos de dados. Se pretender um subconjunto de colunas, utilize números de ordinal para escolher as colunas dos ficheiros de dados originais por ordinal. As colunas ficarão ligadas pela designação ordinal. 

> [!IMPORTANT]
> A cláusula COM é obrigatória para ficheiros CSV.
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

Especifica o exterminador de linha a utilizar. O exterminador de linha padrão é um personagem de linha nova como \r\n.

ESCAPE_CHAR = 'char'

Especifica o carácter no ficheiro que é usado para escapar a si próprio e todos os valores delimitadores no ficheiro. Se o personagem de fuga for seguido por um valor diferente de si mesmo, ou qualquer um dos valores delimitador, o personagem de fuga é abandonado ao ler o valor. 

O parâmetro ESCAPE_CHAR será aplicado independentemente de o FIELDQUOTE estar ou não ativado. Não será usado para escapar ao personagem citando. O personagem citando é escapado com duas citações em alinhamento com o comportamento do Excel CSV.

FIRSTROW = 'first_row' 

Especifica o número da primeira linha para carregar. A predefinição é 1. Isto indica a primeira linha no ficheiro de dados especificado. Os números das fileiras são determinados contando os exterminadores de fileiras. Firstrow é baseado em 1.

FIELDQUOTE = 'field_quote' 

Especifica um personagem que será usado como o personagem de citação no ficheiro CSV. Se não especificado, o caracteres de citação (") será utilizado. 

## <a name="examples"></a>Exemplos

O exemplo seguinte devolve apenas duas colunas com números de ordinal 1 e 4 dos ficheiros da população*.csv. Como não há uma linha de cabeçalho nos ficheiros, começa a ler a partir da primeira linha:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

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
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Passos seguintes

Para mais amostras, vá a [quickstarts](query-data-storage.md) ou guarde os resultados da sua consulta ao Armazenamento Azure utilizando [o CETAS](develop-tables-cetas.md).

---
title: Ficheiros CSV de consulta utilizando SQL on demand (pré-visualização)
description: Neste artigo, você aprenderá a consultar ficheiros CSV únicos com diferentes formatos de ficheiro usando SQL on-demand (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d2f8a30503e14e647cbc9151ebcea7efa000ca07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288295"
---
# <a name="query-csv-files"></a>Consultar ficheiros CSV

Neste artigo, você aprenderá a consultar um único ficheiro CSV usando SQL on demand (pré-visualização) em Azure Synapse Analytics. Os ficheiros CSV podem ter formatos diferentes: 

- Com e sem uma linha de cabeçalho
- Valores de vírgula e delimitados
- Terminações de linha de estilo Windows e Unix
- Valores não citados e citados, e personagens escapando

Todas as variações acima serão cobertas abaixo.

## <a name="quickstart-example"></a>Exemplo de arranque rápido

`OPENROWSET` função permite-lhe ler o conteúdo do ficheiro CSV fornecendo o URL ao seu ficheiro.

### <a name="read-a-csv-file"></a>Leia um ficheiro csv

A maneira mais fácil de ver o conteúdo do seu ficheiro é fornecer URL de `CSV` ficheiro para `OPENROWSET` funcionar, especificar csv `FORMAT` , e 2.0 `PARSER_VERSION` . Se o ficheiro estiver disponível publicamente ou se a sua identidade AZure AD puder aceder a este ficheiro, deverá poder ver o conteúdo do ficheiro utilizando a consulta como a apresentada no seguinte exemplo:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

A opção `firstrow` é usada para saltar a primeira linha no ficheiro CSV que representa o cabeçalho neste caso. Certifique-se de que pode aceder a este ficheiro. Se o seu ficheiro estiver protegido com chave SAS ou identidade personalizada, terá de configurar a [credencial de nível do servidor para o início de sessão](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

### <a name="data-source-usage"></a>Utilização de fontes de dados

Exemplo anterior utiliza o caminho completo para o ficheiro. Como alternativa, pode criar uma fonte de dados externa com a localização que aponta para a pasta raiz do armazenamento:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Uma vez que crie uma fonte de dados, pode utilizar essa fonte de dados e o caminho relativo para o ficheiro em `OPENROWSET` função:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Se uma fonte de dados estiver protegida com chave SAS ou identidade personalizada, pode configurar [a fonte de dados com credencial](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)de área de dados .

### <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

`OPENROWSET` permite especificar explicitamente quais as colunas que pretende ler a partir do ficheiro usando a `WITH` cláusula:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Os números após um tipo de dados na cláusula representam o `WITH` índice de coluna no ficheiro CSV.

Nas secções seguintes pode ver como consultar vários tipos de ficheiros CSV.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** onde as tabelas serão criadas. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="windows-style-new-line"></a>Nova linha estilo Windows

A seguinte consulta mostra como ler um ficheiro CSV sem uma linha de cabeçalho, com uma nova linha estilo Windows e colunas delimitadas em vírgula.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV sem cabeçalho, nova linha estilo Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nova linha de estilo unix

A seguinte consulta mostra como ler um ficheiro sem uma linha de cabeçalho, com uma nova linha estilo Unix e colunas delimitadas em vírgula. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV sem cabeçalho e com Unix-Style nova linha.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Linha de cabeçalho

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha estilo Unix e colunas delimitadas em vírgula. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com Unix-Style nova linha.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Personagem de citação personalizada

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha unix, colunas delimitadas em vírgula e valores citados. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com Unix-Style nova linha e valores citados.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Esta consulta devolveria os mesmos resultados se omitisse o parâmetro FIELDQUOTE, uma vez que o valor padrão para FIELDQUOTE é uma cotação dupla.

## <a name="escape-characters"></a>Personagens de fuga

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha de estilo Unix, colunas delimitadas em vírgula, e um char de fuga usado para o delimiter de campo (vírgula) dentro de valores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com Unix-Style nova linha e carvão de fuga usado para delimiter de campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Esta consulta falharia se o ESCAPECHAR não fosse especificado, uma vez que a vírgula em "Slov,enia" seria tratada como delimiter de campo em vez de parte do nome país/região. "Slov,enia" seria tratado como duas colunas. Portanto, a linha em particular teria uma coluna mais do que as outras linhas, e uma coluna mais do que você definiu na cláusula WITH.

### <a name="escape-quoting-characters"></a>Escape citando personagens

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha unix, colunas delimitadas em vírgula, e um carvão de citação dupla escapado dentro de valores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha unix, colunas delimitadas em vírgula, e um carvão de citação dupla escapado dentro de valores.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> O personagem citando deve ser escapado com outro personagem citando. Citar o carácter só pode aparecer dentro do valor da coluna se o valor for encapsulado com caracteres citantes.

## <a name="tab-delimited-files"></a>Ficheiros delimitados por separadores

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha estilo Unix e colunas delimitadas por separadores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![As primeiras 10 linhas do ficheiro CSV com a linha do cabeçalho e com Unix-Style nova linha e delimiter de separador.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Devolva um subconjunto de colunas

Até agora, especificou o esquema de ficheiros CSV usando WITH e listando todas as colunas. Só pode especificar as colunas de que necessita na sua consulta utilizando um número ordinal para cada coluna necessária. Também omitirá colunas sem interesse.

A seguinte consulta devolve o número de nomes distintos de país/região num ficheiro, especificando apenas as colunas necessárias:

> [!NOTE]
> Veja a cláusula COM na consulta abaixo e note que há "2" (sem cotações) no final da linha onde define a coluna *[country_name].* Significa que a coluna *[country_name]* é a segunda coluna do ficheiro. A consulta ignorará todas as colunas do ficheiro exceto a segunda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Passos seguintes

Os próximos artigos irão mostrar-lhe como:

- [Consulta de ficheiros Parquet](query-parquet-files.md)
- [Consulta de pastas e vários ficheiros](query-folders-multiple-csv-files.md)

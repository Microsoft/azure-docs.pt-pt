---
title: Consulta dados DB da Azure Cosmos usando uma piscina SQL sem servidor em Azure Synapse Link
description: Neste artigo, você aprenderá a consultar Azure Cosmos DB usando uma piscina SQL sem servidor em Azure Synapse Link.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 64a112fd29ee9e3fbb82d9b54322415569b3ff85
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209541"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Consulta dados DB da Azure Cosmos com uma piscina SQL sem servidor em Azure Synapse Link

Um pool SQL sem servidor permite-lhe analisar dados nos seus contentores DB Azure Cosmos que são ativados com [Azure Synapse Link](../../cosmos-db/synapse-link.md) em quase tempo real sem afetar o desempenho das suas cargas de trabalho transacionais. Oferece uma sintaxe T-SQL familiar para consultar dados da [loja analítica](../../cosmos-db/analytical-store-introduction.md) e conectividade integrada a uma ampla gama de ferramentas de consulta de negócios (BI) e ad-hoc através da interface T-SQL.

Para consulta do Azure Cosmos DB, toda a área [de](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) superfície SELECT é suportada através da função [OPENROWSET,](develop-openrowset.md) que inclui a maioria das [funções e operadores SQL](overview-features.md). Também pode armazenar os resultados da consulta que lê os dados da Azure Cosmos DB juntamente com os dados em Azure Blob Storage ou Azure Data Lake Storage, utilizando [criar uma tabela externa como selecionado](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). Atualmente, não é possível armazenar resultados de consulta de piscina SQL sem servidor para a Azure Cosmos DB utilizando o CETAS.

Neste artigo, você aprenderá a escrever uma consulta com uma piscina SQL sem servidor que irá consultar dados de contentores DB Azure Cosmos que estão habilitados com Azure Synapse Link. Você pode então aprender mais sobre a construção de vistas de piscina SQL sem servidor sobre os recipientes DB Azure Cosmos e conectá-los aos modelos Power BI [neste tutorial](./tutorial-data-analyst.md). Este tutorial usa um recipiente com um [esquema bem definido da Azure Cosmos.](../../cosmos-db/analytical-store-introduction.md#schema-representation)

## <a name="overview"></a>Descrição Geral

O pool SQL sem servidor permite-lhe consultar o armazenamento analítico Azure Cosmos DB utilizando `OPENROWSET` a função. 
- `OPENROWSET` com chave em linha. Esta sintaxe pode ser usada para consultar coleções DB Azure Cosmos sem necessidade de preparar credenciais.
- `OPENROWSET` que a credencial referenciada que contém a chave da conta Cosmos DB. Esta sintaxe pode ser usada para criar vistas sobre coleções DB Azure Cosmos.

### <a name="openrowset-with-key"></a>[OPENROWSET com chave](#tab/openrowset-key)

Para suportar a consulta e análise de dados numa loja analítica Azure Cosmos DB, é utilizada uma piscina SQL sem servidor. A piscina SQL sem servidor utiliza a `OPENROWSET` sintaxe SQL, por isso deve primeiro converter a sua cadeia de ligação DB Azure Cosmos para este formato:

```sql
OPENROWSET( 
       'CosmosDB',
       '<SQL connection string for Azure Cosmos DB>',
       <Container name>
    )  [ < with clause > ] AS alias
```

A cadeia de conexão SQL para Azure Cosmos DB especifica o nome da conta DB Azure Cosmos, nome da base de dados, chave principal de conta de base de dados e um nome de região opcional para a `OPENROWSET` função. Algumas destas informações podem ser retiradas da cadeia de ligação padrão Azure Cosmos DB.

Conversão do formato padrão de cadeia de ligação Azure Cosmos DB:

```
AccountEndpoint=https://<database account name>.documents.azure.com:443/;AccountKey=<database account master key>;
```

A cadeia de ligação SQL tem o seguinte formato:

```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

A região é opcional. Se omitido, é utilizada a região primária do contentor.

O nome do recipiente DB Azure Cosmos é especificado sem aspas na `OPENROWSET` sintaxe. Se o nome do recipiente tiver caracteres especiais, por exemplo, um traço (-), o nome deve ser embrulhado dentro de suportes quadrados `[]` () na `OPENROWSET` sintaxe.

### <a name="openrowset-with-credential"></a>[OPENROWSET com credencial](#tab/openrowset-credential)

Pode utilizar `OPENROWSET` a sintaxe que referencia a credencial:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<SQL connection string for Azure Cosmos DB without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

A cadeia de ligação SQL para Azure Cosmos DB não contém uma chave neste caso. A cadeia de ligação tem o seguinte formato:

```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

A chave principal da conta de base de dados é colocada na credencial credencial de nível de servidor ou na base de dados. 

---

> [!IMPORTANT]
> Certifique-se de que está a utilizar alguma colagem de base de dados UTF-8, por exemplo, `Latin1_General_100_CI_AS_SC_UTF8` porque os valores de cadeia numa loja analítica Azure Cosmos DB estão codificados como texto UTF-8.
> Um desfasamento entre codificação de texto no ficheiro e colagem pode causar erros inesperados de conversão de texto.
> Pode alterar facilmente a colagem predefinida da base de dados atual utilizando a declaração T-SQL `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> Uma piscina SQL sem servidor não suporta consultar uma loja de transações Azure Cosmos DB.

## <a name="sample-dataset"></a>Conjunto de dados da amostra

Os exemplos deste artigo baseiam-se em dados do [Centro Europeu de Prevenção e Controlo de Doenças (ECDC) COVID-19 Casos](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [COVID-19 Open Research Dataset (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

Pode ver a licença e a estrutura de dados nestas páginas. Também pode transferir dados de amostra para os conjuntos de dados [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e [CORD-19.](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json)

Para acompanhar este artigo mostrando como consultar os dados DB da Azure Cosmos com uma piscina SQL sem servidor, certifique-se de que cria os seguintes recursos:

* Uma conta de base de dados DB da Azure Cosmos que está [ativada no Azure Synapse Link](../../cosmos-db/configure-synapse-link.md).
* Uma base de dados DB da Azure Cosmos chamada `covid` .
* Dois contentores DB Azure Cosmos nomeados `Ecdc` e `Cord19` carregados com os conjuntos de dados da amostra anteriores.

Pode utilizar o seguinte fio de ligação para efeitos de teste: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Note que esta ligação não garante o desempenho porque esta conta pode estar localizada em região remota em comparação com o seu ponto final Synapse SQL.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Explore os dados DB da Azure Cosmos com inferência automática de esquemas

A forma mais fácil de explorar dados em Azure Cosmos DB é utilizando a capacidade de inferência automática de esquemas. Ao omitir a `WITH` cláusula da `OPENROWSET` declaração, pode instruir a piscina SQL sem servidor para autodetectar (inferir) o esquema da loja analítica do contentor DB Azure Cosmos.

### <a name="openrowset-with-key"></a>[OPENROWSET com chave](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET com credencial](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

No exemplo anterior, instruímos a piscina SQL sem servidor para ligar à `covid` base de dados na conta DB Azure Cosmos `MyCosmosDbAccount` autenticada utilizando a tecla DB Azure Cosmos (o boneco no exemplo anterior). Acedemos então à `Ecdc` loja analítica do contentor na `West US 2` região. Uma vez que não há projeção de propriedades específicas, a `OPENROWSET` função irá devolver todas as propriedades dos itens DB do Azure Cosmos.

Assumindo que os itens no contentor Azure Cosmos DB `date_rep` `cases` têm, e `geo_id` propriedades, os resultados desta consulta são mostrados na tabela seguinte:

| date_rep | casos | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Se precisar de explorar dados do outro recipiente na mesma base de dados Azure Cosmos DB, pode utilizar a mesma cadeia de ligação e fazer referência ao recipiente necessário como o terceiro parâmetro:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

Embora a capacidade de inferência automática de `OPENROWSET` esquemas em fornecer uma consulta simples e fácil de usar, os seus cenários de negócio podem exigir que você especifique explicitamente o esquema para ler propriedades relevantes apenas a partir dos dados DB do Azure Cosmos.

A `OPENROWSET` função permite especificar explicitamente quais as propriedades que pretende ler a partir dos dados do recipiente e especificar os seus tipos de dados.

Imaginemos que importámos alguns dados do conjunto de dados do [ECDC COVID](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) com a seguinte estrutura para a Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Estes documentos JSON planos em Azure Cosmos DB podem ser representados como um conjunto de linhas e colunas em Synapse SQL. A `OPENROWSET` função permite especificar um subconjunto de propriedades que pretende ler e os tipos exatos de colunas na `WITH` cláusula:

### <a name="openrowset-with-key"></a>[OPENROWSET com chave](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

### <a name="openrowset-with-credential"></a>[OPENROWSET com credencial](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
   
```

---
O resultado desta consulta pode parecer a seguinte tabela:

| date_rep | casos | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Para obter mais informações sobre os tipos DE SQL que devem ser usados para valores DB Azure Cosmos, consulte as [regras para mapeamentos de tipo SQL](#azure-cosmos-db-to-sql-type-mappings) no final do artigo.

## <a name="create-view"></a>Criar vista

A criação de pontos de vista nas bases de dados principais ou predefinidas não é recomendada ou suportada. Por isso, é necessário criar uma base de dados de utilizadores para as suas opiniões.

Assim que identificar o esquema, pode preparar uma vista em cima dos seus dados DB Azure Cosmos. Deve colocar a sua chave de conta DB Azure Cosmos numa credencial separada e fazer referência a esta credencial da `OPENROWSET` função. Não guarde a chave da sua conta na definição de visualização.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Não utilize `OPENROWSET` sem esquemas explicitamente definidos porque pode ter impacto no seu desempenho. Certifique-se de que utiliza os tamanhos mais pequenos possíveis para as suas colunas (por exemplo VARCHAR(100) em vez de VARCHAR predefinido (8000)). Deve utilizar uma colagem UTF-8 como colagem de base de dados predefinida ou defini-la como uma combinação explícita de colunas para evitar a [emissão de conversão UTF-8](../troubleshoot/reading-utf8-text.md). A colisão `Latin1_General_100_BIN2_UTF8` proporciona o melhor desempenho quando os dados do filtro yu utilizam algumas colunas de cordas.

## <a name="query-nested-objects-and-arrays"></a>Consulta objetos aninhados e matrizes

Com a Azure Cosmos DB, pode representar modelos de dados mais complexos, compondo-os como objetos aninhados ou matrizes. A capacidade de autossíceno da Azure Synapse Link para Azure Cosmos DB gere a representação de esquemas na loja analítica fora da caixa, que inclui o manuseamento de tipos de dados aninhados que permitem uma consulta rica a partir da piscina SQL sem servidor.

Por exemplo, o conjunto de dados [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) tem documentos JSON que seguem esta estrutura:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Os objetos aninhados e matrizes em Azure Cosmos DB são representados como cordas JSON no resultado da consulta quando a `OPENROWSET` função os lê. Pode especificar os caminhos para valores aninhados nos objetos quando utilizar a `WITH` cláusula:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

O resultado desta consulta pode parecer a seguinte tabela:

| paper_id | título | do IdP | autores |
| --- | --- | --- | --- |
| bb11206963e831f... | Informação Complementar Um eco-epidemi... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | O Uso da Convalescente Sera em Imunitária-E... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (Marama) Tuber e B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Saiba mais sobre a análise de [tipos de dados complexos em Azure Synapse Link](../how-to-analyze-complex-schema.md) e estruturas [aninhadas numa piscina SQL sem servidor.](query-parquet-nested-types.md)

> [!IMPORTANT]
> Se vir caracteres inesperados no seu texto como `MÃƒÂ©lade` em vez de , `Mélade` então a sua colagem de base de dados não está definida para a colagem [UTF-8.](/sql/relational-databases/collations/collation-and-unicode-support#utf8)
> [Altere a colagem da base de dados](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para a colagem UTF-8 utilizando uma declaração SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Achatado conjuntos aninhados

Os dados DB da Azure Cosmos podem ter subarrays aninhados como a matriz do autor de um conjunto de dados [CORD-19:](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

Em alguns casos, poderá ser necessário "juntar" as propriedades do item superior (metadados) com todos os elementos da matriz (autores). Um pool SQL sem servidor permite-lhe aplainar estruturas aninhadas aplicando a `OPENJSON` função na matriz aninhada:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

O resultado desta consulta pode parecer a seguinte tabela:

| título | autores | primeiro | último | afiliação |
| --- | --- | --- | --- | --- |
| Informação Complementar Um eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Informação Complementar Um eco-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Informação Complementar Um eco-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Informação Complementar Um eco-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Se vir caracteres inesperados no seu texto como `MÃƒÂ©lade` em vez de , `Mélade` então a sua colagem de base de dados não está definida para a colagem [UTF-8.](/sql/relational-databases/collations/collation-and-unicode-support#utf8) [Altere a colagem da base de dados](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para a colagem UTF-8 utilizando uma declaração SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Mapeamentos do tipo Azure Cosmos DB para SQL

Embora a loja de transações DB da Azure Cosmos seja schema-agnóstica, a loja analítica é schematizada para otimizar para o desempenho da consulta analítica. Com a capacidade de autossícoma de Azure Synapse Link, a Azure Cosmos DB gere a representação de esquemas na loja analítica fora da caixa, que inclui o manuseamento de tipos de dados aninhados. Uma vez que um pool SQL sem servidor consulta a loja analítica, é importante entender como mapear os tipos de dados de entrada DB do Azure Cosmos para os tipos de dados SQL.

As contas DB da Azure Cosmos de SQL (Core) suportam tipos de propriedade JSON de número, string, boolean, nulo, objeto aninhado ou matriz. Você precisaria escolher tipos DE SQL que correspondam a estes tipos de JSON se você estiver usando a `WITH` cláusula em `OPENROWSET` . A tabela seguinte mostra os tipos de coluna SQL que devem ser usados para diferentes tipos de propriedade em Azure Cosmos DB.

| Tipo de propriedade Azure Cosmos DB | Tipo de coluna SQL |
| --- | --- |
| Booleano | bit |
| Número inteiro | bigint |
| Decimal | float |
| String | varchar (colagem de base de dados UTF-8) |
| Data (cadeia formatada pela ISO) | varchar(30) |
| Data (relógio UNIX) | bigint |
| Nulo | `any SQL type` 
| Objeto aninhado ou matriz | varchar(máx) (colagem de base de dados UTF-8), serializado como texto JSON |

## <a name="full-fidelity-schema"></a>Esquema de fidelidade total

O esquema de fidelidade completa Azure Cosmos DB regista os valores e os seus melhores tipos de correspondência para cada propriedade num recipiente. A `OPENROWSET` função num recipiente com esquema de fidelidade total fornece tanto o tipo como o valor real em cada célula. Vamos supor que a seguinte consulta lê os itens de um recipiente com esquema de fidelidade completa:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

O resultado desta consulta devolverá tipos e valores formatados como texto JSON:

| date_rep | casos | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

Por cada valor, pode ver o tipo identificado num item de contentor Azure Cosmos DB. A maioria dos valores para a `date_rep` propriedade contêm `date` valores, mas alguns deles são incorretamente armazenados como cordas em Azure Cosmos DB. O esquema de fidelidade total devolverá valores corretamente digitados `date` e `string` valores formatados incorretamente.
O número de casos é informação armazenada como `int32` um valor, mas há um valor que é introduzido como um número decimal. Este valor tem o `float64` tipo. Se houver alguns valores que excedam o maior `int32` número, serão armazenados como o `int64` tipo. Todos os `geo_id` valores neste exemplo são armazenados como `string` tipos.

> [!IMPORTANT]
> A `OPENROWSET` função sem `WITH` cláusula expõe ambos os valores com os tipos esperados e os valores com tipos introduzidos incorretamente. Esta função destina-se à exploração de dados e não à comunicação. Não analise os valores JSON devolvidos desta função para construir relatórios. Use uma [cláusula COM](#query-items-with-full-fidelity-schema) explícita para criar os seus relatórios. Deve limpar os valores que possuem tipos incorretos no recipiente DB Azure Cosmos para aplicar correções na loja analítica de fidelidade completa.

Se precisar de consultar as contas DB da Azure Cosmos do tipo Mongo DB API, pode saber mais sobre a representação total do esquema de fidelidade na loja analítica e sobre os nomes de propriedade estendida a serem usados na [Loja Azure Cosmos DB Analytical?](../../cosmos-db/analytical-store-introduction.md#analytical-schema)

### <a name="query-items-with-full-fidelity-schema"></a>Artigos de consulta com esquema de fidelidade completa

Ao consultar o esquema de fidelidade completo, precisa especificar explicitamente o tipo SQL e o tipo de propriedade Azure Cosmos DB esperado na `WITH` cláusula.

No exemplo seguinte, assumimos que `string` é o tipo correto para a propriedade e é o tipo correto para a `geo_id` `int32` `cases` propriedade:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Valores para `geo_id` e `cases` que têm outros tipos serão devolvidos como `NULL` valores. Esta consulta referenciará apenas o `cases` tipo especificado na expressão `cases.int32` ().

Se tiver valores com outros tipos `cases.int64` `cases.float64` (,) que não podem ser limpos num recipiente DB Azure Cosmos, terá de os referenciar explicitamente numa `WITH` cláusula e combinar os resultados. A seguinte consulta agrega ambos `int32` , e armazenado na `int64` `float64` `cases` coluna:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

Neste exemplo, o número de casos é armazenado quer como `int32` `int64` , ou `float64` valores. Todos os valores devem ser extraídos para calcular o número de casos por país.

## <a name="known-issues"></a>Problemas conhecidos

- Uma piscina SQL sem servidor devolverá um aviso de tempo de compilação se a colagem da `OPENROWSET` coluna não tiver codificação UTF-8. Pode alterar facilmente a colagem predefinida para todas as `OPENROWSET` funções em execução na base de dados atual utilizando a declaração T-SQL `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

Os possíveis erros e ações de resolução de problemas estão listados na tabela seguinte.

| Erro | Causa raiz |
| --- | --- |
| Erros de sintaxe:<br/> - Sintaxe incorreta perto `Openrowset`<br/> - `...` não é uma `BULK OPENROWSET` opção de fornecedor reconhecida.<br/> - Sintaxe incorreta perto `...` | Possíveis causas de raiz:<br/> - Não usar o CosmosDB como primeiro parâmetro.<br/> - Usando uma corda literal em vez de um identificador no terceiro parâmetro.<br/> - Não especificar o terceiro parâmetro (nome do recipiente). |
| Houve um erro na cadeia de ligação CosmosDB. | - A conta, base de dados ou chave não está especificada. <br/> - Há alguma opção numa cadeia de ligação que não é reconhecida.<br/> - Um ponto e vírgula `;` é colocado na extremidade de uma corda de ligação. |
| A resolução do caminho do CosmosDB falhou com o erro "Nome de conta incorreto" ou "Nome de base de dados incorreto". | O nome da conta especificada, o nome da base de dados ou o contentor não podem ser encontrados, ou o armazenamento analítico não foi ativado para a recolha especificada.|
| A resolução do caminho cosmosDB falhou com o erro "Valor secreto incorreto" ou "O segredo é nulo ou vazio". | A chave da conta não é válida ou está desaparecida. |
| Coluna `column name` do tipo não é `type name` compatível com o tipo de dados `type name` externo. | O tipo de coluna especificado na `WITH` cláusula não corresponde ao tipo no recipiente DB Azure Cosmos. Tente alterar o tipo de coluna tal como é descrito na secção [Azure Cosmos DB para mapeamentos do tipo SQL,](#azure-cosmos-db-to-sql-type-mappings)ou utilize o `VARCHAR` tipo. |
| A coluna contém `NULL` valores em todas as células. | Possivelmente um nome de coluna errado ou expressão de caminho na `WITH` cláusula. O nome da coluna (ou expressão de caminho após o tipo de coluna) na `WITH` cláusula deve corresponder a algum nome de propriedade na coleção DB Azure Cosmos. A comparação é *sensível a casos.* Por exemplo, `productCode` e `ProductCode` são propriedades diferentes. |

Pode relatar sugestões e problemas na página de feedback do [Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos:

- [Use power bi e piscina SQL sem servidor com Link Azure Synapse](../../cosmos-db/synapse-link-power-bi.md)
- [Criar e utilizar vistas numa piscina SQL sem servidor](create-use-views.md)
- [Tutorial sobre a construção de vistas de piscina SQL sem servidor sobre a Azure Cosmos DB e ligando-os aos modelos Power BI via DirectQuery](./tutorial-data-analyst.md)

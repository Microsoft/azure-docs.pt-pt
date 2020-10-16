---
title: Consulta dados DB da Azure Cosmos utilizando o sql sem servidor em Azure Synapse Link (pré-visualização)
description: Neste artigo, você aprenderá a consultar Azure Cosmos DB usando SQL on-demand em Azure Synapse Link (pré-visualização).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 0cc2c04208c4800a883848896a0f1659e8bf72e9
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097257"
---
# <a name="query-azure-cosmos-db-data-using-sql-serverless-in-azure-synapse-link-preview"></a>Consulta dados DB da Azure Cosmos utilizando o sql sem servidor em Azure Synapse Link (pré-visualização)

O synapse SQL sem servidor (anteriormente SQL on demand) permite-lhe analisar dados nos seus contentores DB Azure Cosmos que estão ativados com [Azure Synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) em quase tempo real sem afetar o desempenho das suas cargas de trabalho transacionais. Oferece uma sintaxe T-SQL familiar para consultar dados da [loja analítica](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e conectividade integrada a uma ampla gama de ferramentas de consulta bi e ad-hoc através da interface T-SQL.

Para consulta do Azure Cosmos DB, toda a área [de](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) superfície SELECT é suportada através da função [OPENROWSET,](develop-openrowset.md) incluindo a maioria das [funções e operadores SQL](overview-features.md). Também pode armazenar os resultados da consulta que lê os dados da Azure Cosmos DB juntamente com os dados em Azure Blob Storage ou Azure Data Lake Storage utilizando [criar uma tabela externa como selecionado.](develop-tables-cetas.md#cetas-in-sql-on-demand) Não é possível armazenar atualmente resultados de consulta sem servidor SQL para Azure Cosmos DB utilizando [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

Neste artigo, você aprenderá a escrever uma consulta usando o sql sem servidor que irá consultar dados de recipientes DB Azure Cosmos que estão ativados synapse Link. Você pode então aprender mais sobre a construção de vistas sem servidor SQL sobre os recipientes DB Azure Cosmos e conectá-los aos modelos Power BI [neste](./tutorial-data-analyst.md) tutorial. 

## <a name="overview"></a>Descrição geral

Para suportar a consulta e análise de dados na loja analítica Azure Cosmos DB, o SQL sem servidor utiliza a seguinte `OPENROWSET` sintaxe:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

A cadeia de conexão DB Azure Cosmos especifica o nome da conta DB Azure Cosmos, nome da base de dados, chave principal de conta de base de dados e um nome de região opcional para `OPENROWSET` funcionar. 

> [!IMPORTANT]
> Certifique-se de que usa pseudónimo depois `OPENROWSET` . Existe um [problema conhecido](#known-issues) que causa problema de ligação ao ponto final SQL sem servidor Synapse se não especificar o pseudónimo após a `OPENROWSET` função.

A cadeia de ligação tem o seguinte formato:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

O nome do recipiente DB Azure Cosmos é especificado sem citações na `OPENROWSET` sintaxe. Se o nome do recipiente tiver caracteres especiais (por exemplo, um traço '-'), o nome deve ser embrulhado dentro dos `[]` (suportes quadrados) na `OPENROWSET` sintaxe.

> [!NOTE]
> SQL sem servidor não suporta consulta da loja de transações Azure Cosmos DB.

## <a name="sample-data-set"></a>Conjunto de dados de exemplo

Os exemplos deste artigo baseiam-se em dados do [Centro Europeu de Prevenção e Controlo de Doenças (ECDC) COVID-19 Casos](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [covid-19 Open Research Dataset (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Pode ver a licença e a estrutura de dados nestas páginas e transferir dados de amostra para conjuntos de dados [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e [Cord19.](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json)

Para acompanhar este artigo mostrando como consultar os dados do Cosmos DB com o SQL sem servidor, certifique-se de que cria os seguintes recursos:
* Uma conta de base de dados DB Azure Cosmos que está [ativada no Synapse Link](../../cosmos-db/configure-synapse-link.md)
* Uma base de dados DB Azure Cosmos chamada `covid`
* Dois contentores DB Azure Cosmos nomeados `EcdcCases` e `Cord19` com conjuntos de dados de amostra acima carregados.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Explore os dados DB da Azure Cosmos com inferência automática de esquemas

A forma mais fácil de explorar dados em Azure Cosmos DB é aproveitando a capacidade de inferência automática do esquema. Ao omitir a `WITH` cláusula da `OPENROWSET` declaração, pode instruir o SQL sem servidor para detetar (inferir) automaticamente o esquema da loja analítica do contentor DB Azure Cosmos.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
No exemplo acima, estamos a instruir o SQL sem servidor para ligar à `covid` base de dados na conta DB Azure Cosmos `MyCosmosDbAccount` autenticada utilizando a tecla DB Azure Cosmos (manequim no exemplo acima). Então estamos a aceder à `EcdcCases` loja analítica do contentor na `West US 2` região. Uma vez que não há projeção de propriedades específicas, `OPENROWSET` a função devolverá todas as propriedades dos itens DB do Azure Cosmos.

Se precisar de explorar dados do outro recipiente na mesma base de dados Azure Cosmos DB, pode utilizar o mesmo recipiente de ligação e referência exigido como terceiro parâmetro:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

Embora a capacidade de inferência automática de `OPENROWSET` esquemas em fornecer uma consulta simples e fácil de usar, os seus cenários de negócio podem exigir que você especifique explicitamente o esquema para ler propriedades relevantes apenas a partir dos dados DB do Azure Cosmos.

`OPENROWSET` permite especificar explicitamente quais as propriedades que pretende ler a partir dos dados do recipiente e especificar os seus tipos de dados. Imaginemos que importámos alguns dados do conjunto de [dados do ECDC COVID](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) com a seguinte estrutura para a Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Estes documentos JSON planos em Azure Cosmos DB podem ser representados como um conjunto de linhas e colunas em Synapse SQL. `OPENROWSET` função permite especificar um subconjunto de propriedades que deseja ler e os tipos exatos de coluna na `WITH` cláusula:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

O resultado desta consulta pode parecer:

| date_rep | casos | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Reveja as [regras para mapeamentos do tipo SQL](#azure-cosmos-db-to-sql-type-mappings) no final do artigo para obter mais informações sobre os tipos DE SQL que devem ser usados para o valor DB do Azure Cosmos.

## <a name="querying-nested-objects-and-arrays"></a>Consulta de objetos e matrizes aninhados

O Azure Cosmos DB permite-lhe representar modelos de dados mais complexos, compondo-os como objetos aninhados ou matrizes. A capacidade de autossíceno da Synapse Link para Azure Cosmos DB gere a representação de esquemas na loja analítica fora da caixa, o que inclui o manuseamento de tipos de dados aninhados permitindo uma consulta rica a partir de servidor SQL sem servidor.

Por exemplo, o conjunto de dados [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) tem documentos JSON seguindo a seguinte estrutura:

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

Os objetos aninhados e matrizes em Azure Cosmos DB são representados como cordas JSON no resultado da consulta quando a `OPENROWSET` função os lê. Uma opção para ler os valores destes tipos complexos como colunas SQL é utilizar funções SQL JSON:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

O resultado desta consulta pode parecer:

| título | autores | first_autor_name |
| --- | --- | --- |
| Informação Complementar Um eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Como opção alternativa, também pode especificar os caminhos para valores aninhados nos objetos ao utilizar a `WITH` cláusula:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Saiba mais sobre a análise de [tipos de dados complexos em Synapse Link](../how-to-analyze-complex-schema.md) e estruturas [aninhadas em sql sem servidor.](query-parquet-nested-types.md)

> [!IMPORTANT]
> Se vir caracteres inesperados no seu texto como `MÃƒÂ©lade` em vez `Mélade` de, então a sua colagem de base de dados não está definida para a colagem [UTF8.](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 
> [Altere a colagem da base de dados](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para alguma colagem UTF8 utilizando alguma declaração SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .


## <a name="flattening-nested-arrays"></a>Achatamento de matrizes aninhadas

Os dados do Azure Cosmos DB podem ter sub-arrays aninhados como a matriz do autor do conjunto de dados [Cord19:](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)

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

Em alguns casos, poderá ser necessário "juntar" as propriedades do item superior (metadados) com todos os elementos da matriz (autores). Sem servidor SQL permite-lhe aplainar estruturas aninhadas aplicando `OPENJSON` função na matriz aninhada:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
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

O resultado desta consulta pode parecer:

| título | autores | primeiro | último | afiliação |
| --- | --- | --- | --- | --- |
| Informação Complementar Um eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Informação Complementar Um eco-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Informação Complementar Um eco-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Informação Complementar Um eco-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Se vir caracteres inesperados no seu texto como `MÃƒÂ©lade` em vez `Mélade` de, então a sua colagem de base de dados não está definida para a colagem [UTF8.](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 
> [Altere a colagem da base de dados](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para alguma colagem UTF8 utilizando alguma declaração SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Mapeamentos do tipo Azure Cosmos DB para SQL

É importante notar em primeiro lugar que enquanto a loja de transações Azure Cosmos DB é schema-agnóstica, a loja analítica é schematizada para otimizar para o desempenho de consulta analítica. Com a capacidade de autossínco da Synapse Link, a Azure Cosmos DB gere a representação de esquemas na loja analítica fora da caixa, o que inclui o manuseamento de tipos de dados aninhados. Uma vez que o SQL não tem servidores, a loja analítica é importante entender como mapear os tipos de dados de entrada DB do Azure Cosmos para os tipos de dados SQL.

As contas DB da Azure Cosmos de SQL (Core) suportam tipos de propriedade JSON de número, corda, boolean, nulo, objeto aninhado ou matriz. Você precisaria escolher tipos SQL que correspondam a estes tipos de JSON se estiver a usar `WITH` a cláusula em `OPENROWSET` . Veja abaixo os tipos de coluna SQL que devem ser usados para diferentes tipos de propriedade em Azure Cosmos DB.

| Tipo de propriedade Azure Cosmos DB | Tipo de coluna SQL |
| --- | --- |
| Booleano | bit |
| Número inteiro | bigint |
| Decimal | float |
| String | varchar (colagem de base de dados UTF8) |
| Data (cadeia formatada ISO) | varchar(30) |
| Data (semente de tempo unix) | bigint |
| Nulo | `any SQL type` 
| Objeto aninhado ou matriz | varchar(máx) (colagem de base de dados UTF8), serializado como texto JSON |

Para consultar as contas DB da Azure Cosmos do tipo Mongo DB API, você pode saber mais sobre a representação total do esquema de fidelidade na loja analítica e os nomes de propriedade estendidas a serem usados [aqui.](../../cosmos-db/analytical-store-introduction.md#analytical-schema)

## <a name="known-issues"></a>Problemas conhecidos

- O pseudónimo **DEVE** ser especificado após `OPENROWSET` a função (por exemplo, `OPENROWSET (...) AS function_alias` ). Omitir um pseudónimo pode causar problemas de ligação e o ponto final SQL sem servidor Synapse pode estar temporariamente indisponível. Esta questão será resolvida em novembro de 2020.
- O SQL sem servidor sinapse não suporta atualmente [o esquema de fidelidade completa da Azure Cosmos DB](../../cosmos-db/analytical-store-introduction.md#schema-representation). Utilize o SQL sem servidor Synapse apenas para aceder ao esquema bem definido da Cosmos DB.

A lista dos possíveis erros e ações de resolução de problemas consta do quadro seguinte:

| Erro | Causa raiz |
| --- | --- |
| Erros de sintaxe:<br/> - Sintaxe incorreta perto de 'Openrowset'<br/> - `...` não é uma opção reconhecida de fornecedor DE OPENROWSET A GRANEL.<br/> - Sintaxe incorreta perto `...` | Possíveis causas de raiz<br/> - Não usar o 'CosmosDB' como primeiro parâmetro,<br/> - Utilização literal de cordas em vez de identificador no terceiro parâmetro,<br/> - Não especificar o terceiro parâmetro (nome do recipiente) |
| Houve um erro na cadeia de ligação CosmosDB | - Conta, Base de Dados, Chave não é especificada <br/> - Existe alguma opção na cadeia de ligação que não é reconhecida.<br/> - O ponto de `;` esmicolon é colocado no fim da cadeia de ligação |
| A resolução do caminho do CosmosDB falhou com o erro 'Nome de conta/base de dados incorrecto' | O nome de conta especificado ou o nome da base de dados não podem ser encontrados. |
| A resolução do caminho do CosmosDB falhou com o erro 'Valor secreto incorrecto' 'Segredo é nulo ou vazio' | A chave da conta não é válida ou em falta. |

Pode relatar sugestões e problemas na [página de feedback do Azure Synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos:

- [Como criar e usar vistas em SQL on demand](create-use-views.md) 
- [Tutorial sobre a construção de vistas a pedido do SQL sobre a Azure Cosmos DB e ligá-las aos modelos Power BI via DirectQuery](./tutorial-data-analyst.md)

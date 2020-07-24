---
title: Analisar esquema com matrizes e estruturas aninhadas
description: Como analisar matrizes e estruturas aninhadas com Apache Spark e SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ad6761466cc958235557609e929e641a0311ee43
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999197"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analise tipos de dados complexos no Azure Synapse Analytics

Este artigo é relevante para ficheiros e contentores parquet em [Synapse Link para Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Explica como os utilizadores podem usar o Spark ou o SQL para ler ou transformar dados com esquemas complexos, como matrizes ou estruturas aninhadas. O exemplo a seguir é completado com um único documento, mas pode facilmente escalar para biliões de documentos com Spark ou SQL. O código incluído neste artigo utiliza o PySpark (Python).

## <a name="use-case"></a>Caso de Utilização

Os tipos de dados complexos são cada vez mais comuns e representam um desafio para os engenheiros de dados, uma vez que a análise de esquemas aninhados e matrizes tendem a incluir consultas de SQL demoradas e complexas. Além disso, pode ser difícil renomear ou lançar o tipo de dados de colunas aninhadas. Além disso, surgem problemas de desempenho ao trabalhar com objetos profundamente aninhados.

Os Engenheiros de Dados precisam entender como processar eficientemente tipos de dados complexos e torná-los facilmente acessíveis a todos.

No exemplo seguinte, a Faísca de Sinapse é usada para ler e transformar objetos numa estrutura plana através de quadros de dados. O sinapse SQL sem servidor é utilizado para consultar esses objetos diretamente e devolver esses resultados como uma tabela regular.

## <a name="what-are-arrays-and-nested-structures"></a>O que são matrizes e estruturas aninhadas?

O seguinte objeto vem do [App Insight.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) Neste objeto, existem estruturas aninhadas e matrizes que contêm estruturas aninhadas.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Exemplo de esquema de matrizes e estruturas aninhadas
Ao imprimir o esquema do quadro de dados do objeto (chamado **df)** com o `df.printschema` comando, vemos a seguinte representação:

* Cor amarela representa estrutura aninhada
* Cor verde representa uma matriz com dois elementos

[![Origem schema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_ts**, e **_etag** foram adicionados ao sistema à medida que o documento foi ingerido na loja transacional Azure Cosmos DB.

O quadro de dados acima conta para 5 colunas e apenas 1 linha. Após a transformação, o quadro de dados curado terá 13 colunas e 2 linhas em formato tabular.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Achate estruturas aninhadas e explodir matrizes com Faísca Apache

Com a Faísca De Sinapse, é fácil transformar estruturas aninhadas em colunas e elementos de matriz em múltiplas linhas. Os seguintes passos podem ser utilizados para a implementação.

[![Etapas de transformações de faíscas](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**Passo 1**: Definimos uma função para achatar o esquema aninhado. Esta função pode ser utilizada sem alterações. Criar uma célula num [caderno PySpark](quickstart-apache-spark-notebook.md) com a seguinte função:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Passo 2**: Utilize a função para aplainar o esquema aninhado do quadro de dados **(df)** num novo quadro de `df_flat` dados:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A função de visualização deve retornar 10 colunas e 1 linha. A matriz e os seus elementos aninhados ainda estão lá.

**Passo 3**: Transforme a matriz `context_custom_dimensions` no quadro de dados num novo `df_flat` dataframe `df_flat_explode` . No seguinte código, também definimos qual coluna selecionar:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A função de visualização deve retornar 10 colunas e 2 linhas. O próximo passo é aplainar os esquemas aninhados com a função definida no passo 1.

**Passo 4**: Utilize a função para aplainar o esquema aninhado da estrutura de dados `df_flat_explode` num novo quadro de dados: `df_flat_explode_flat`
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A função de visualização deve apresentar 13 colunas e 2 linhas.

A função `printSchema` do quadro de `df_flat_explode_flat` dados devolve o seguinte resultado:

[![Schema final](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Leia matrizes e estruturas aninhadas diretamente com o sql sem servidor

Consultar e criar vistas e tabelas sobre tais objetos é possível com SQL sem servidor.

Em primeiro lugar, dependendo da forma como os dados foram armazenados, os utilizadores devem utilizar a seguinte taxonomia. Tudo o que é mostrado em UPPER CASE é específico do seu caso de utilização:

| GRANEL              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |'Parquet' (ADLSg2)|
| N'endpoint= https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ;conta= ACCOUNTNAME;BASE DE DADOS=NOME DE BASEDÁTE;COLLECTIONNAME;REGIONTOQUERY, SECRET='YOURSECRET' |'CosmosDB' (Ligação Sinapse)|


> [!NOTE]
> SqL sem servidor suportará o Linked Service for Synapse Link for Azure Cosmos e AAD passthrough. A capacidade encontra-se atualmente em pré-visualização fechada para Synapse Link.

Substitua cada campo da seguinte forma:
* 'YOUR BULK ABOVE' = a cadeia de ligação da fonte de dados a que se conecta
* 'YOUR TYPE ABOVE' = o formato que utiliza para ligar à fonte

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Existem dois tipos diferentes de operações:

O primeiro tipo de operação é indicado na seguinte linha de código, que define a coluna chamada `contextdataeventTime` que se refere ao elemento aninhado: Context.Data.eventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Esta linha definirá a coluna chamada contextdataeventTime que se refere ao elemento ninho: Contexto>Dados>eventTime

O segundo tipo de operação utiliza `cross apply` para criar novas linhas para cada elemento sob a matriz e, em seguida, com define cada objeto aninhado semelhante ao primeiro ponto de bala: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Se a matriz tivesse 5 elementos com 4 estruturas aninhadas, o SQL sem servidor devolveria 5 linhas e 4 colunas. SQL sem servidor pode consultar no local, mapear a matriz em 2 linhas e, exibir todas as estruturas aninhadas em colunas.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como consultar a Synapse Link para Azure Cosmos DB com Faísca](./synapse-link/how-to-query-analytical-store-spark.md)
* [Tipos aninhados de parquet de consulta](./sql/query-parquet-nested-types.md) 
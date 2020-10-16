---
title: Analisar esquema com matrizes e estruturas aninhadas
description: Como analisar matrizes e estruturas aninhadas com Apache Spark e SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 43e4027c9d88b209ed0606e045dd5b4d47d3ff6e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088521"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analise tipos de dados complexos no Azure Synapse Analytics

Este artigo é relevante para ficheiros e contentores parquet em [Azure Synapse Link para Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Pode utilizar o Spark ou o SQL para ler ou transformar dados com esquemas complexos, tais como matrizes ou estruturas aninhadas. O exemplo a seguir é completado com um único documento, mas pode facilmente escalar para biliões de documentos com Spark ou SQL. O código incluído neste artigo utiliza o PySpark (Python).

## <a name="use-case"></a>Caso de utilização

Os tipos de dados complexos são cada vez mais comuns e representam um desafio para os engenheiros de dados. Analisar esquemas aninhados e matrizes pode envolver consultas de SQL demorados e complexas. Além disso, pode ser difícil renomear ou lançar o tipo de dados de colunas aninhadas. Além disso, quando se está a trabalhar com objetos profundamente aninhados, pode encontrar problemas de desempenho.

Os engenheiros de dados precisam entender como processar eficientemente tipos de dados complexos e torná-los facilmente acessíveis a todos. No exemplo seguinte, utiliza-se a Spark in Azure Synapse Analytics para ler e transformar objetos numa estrutura plana através de quadros de dados. Utiliza o modelo sem servidor do SQL em Azure Synapse Analytics para consultar esses objetos diretamente e devolver esses resultados como uma tabela regular.

## <a name="what-are-arrays-and-nested-structures"></a>O que são matrizes e estruturas aninhadas?

O seguinte objeto provém do [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Neste objeto, existem estruturas aninhadas e matrizes que contêm estruturas aninhadas.

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
Quando estiver a imprimir o esquema da estrutura de dados do objeto (chamado **df)** com o `df.printschema` comando, vê a seguinte representação:

* Amarelo representa estruturas aninhadas.
* Verde representa uma matriz com dois elementos.

[![Código com destaque amarelo e verde, mostrando origem do esquema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` e `_etag` foram adicionados ao sistema à medida que o documento foi ingerido na loja transacional Azure Cosmos DB.

O quadro de dados anteriores conta apenas com 5 colunas e 1 linha. Após a transformação, o quadro de dados curado terá 13 colunas e 2 linhas, em formato tabular.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Achate estruturas aninhadas e matrizes de explosão

Com a Spark in Azure Synapse Analytics, é fácil transformar estruturas aninhadas em colunas e elementos de matriz em múltiplas linhas. Utilize os seguintes passos para a implementação.

[![Flowchart mostrando passos para transformações de faíscas](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Defina uma função para achatar o esquema aninhado

Pode utilizar esta função sem alterações. Criar uma célula num [caderno PySpark](quickstart-apache-spark-notebook.md) com a seguinte função:

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

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Use a função para achatar o esquema aninhado

Neste passo, achata-se o esquema aninhado do quadro de dados **(df)** num novo quadro de dados `df_flat` ( ):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A função de visualização deve retornar 10 colunas e 1 linha. A matriz e os seus elementos aninhados ainda estão lá.

### <a name="transform-the-array"></a>Transforme a matriz

Aqui, transforma-se a matriz, `context_custom_dimensions` no quadro de `df_flat` dados, num novo quadro de `df_flat_explode` dados. No seguinte código, também define qual coluna selecionar:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A função de visualização deve retornar 10 colunas e 2 linhas. O próximo passo é aplainar os esquemas aninhados com a função definida no passo 1.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Use a função para achatar o esquema aninhado

Finalmente, utiliza a função para aplainar o esquema aninhado do quadro de `df_flat_explode` dados, num novo quadro de dados, `df_flat_explode_flat`
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A função de visualização deve apresentar 13 colunas e 2 linhas.

A função `printSchema` do quadro de `df_flat_explode_flat` dados devolve o seguinte resultado:

[![Código que mostra o esquema final](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Leia matrizes e estruturas aninhadas diretamente

Com o modelo sem servidor de SQL, pode consultar e criar vistas e tabelas sobre tais objetos.

Em primeiro lugar, dependendo da forma como os dados foram armazenados, os utilizadores devem utilizar a seguinte taxonomia. Tudo o que é mostrado em maiúsculas é específico do seu caso de utilização:

| Em massa | Formato |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |'Parquet' (ADLSg2)|
| N'endpoint= https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ;account=ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY', SECRET='YOURSECRET' |'CosmosDB' (Ligação Azure Synapse)|


Substitua cada campo da seguinte forma:
* 'YOUR BULK ABOVE' é a cadeia de ligação da fonte de dados a que se conecta.
* 'YOUR TYPE ABOVE' é o formato que utiliza para ligar à fonte.

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

- O primeiro tipo de operação é indicado na seguinte linha de código, que define a coluna chamada `contextdataeventTime` que se refere ao elemento aninhado, `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Esta linha define a coluna chamada `contextdataeventTime` que se refere ao elemento aninhado, `Context>Data>eventTime` .

- O segundo tipo de operação utiliza `cross apply` para criar novas linhas para cada elemento sob a matriz. Então define cada objeto aninhado. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Se a matriz tinha 5 elementos com 4 estruturas aninhadas, o modelo sem servidor de SQL devolve 5 linhas e 4 colunas. O modelo sem servidor de SQL pode consultar no lugar, mapear a matriz em 2 linhas e exibir todas as estruturas aninhadas em colunas.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como consultar a Synapse Link para Azure Cosmos DB com Faísca](./synapse-link/how-to-query-analytical-store-spark.md)
* [Consultar tipos aninhados de Parquet](./sql/query-parquet-nested-types.md) 

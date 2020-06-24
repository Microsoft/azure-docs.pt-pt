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
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908172"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Analisar tipos de dados complexos em Synapse

Este artigo é relevante para ficheiros e contentores parquet em **Azure Synapse Link para Azure Cosmos DB**. Explica como os utilizadores podem usar o Spark ou o SQL para ler ou transformar dados com esquemas complexos, como matrizes ou estruturas aninhadas. O exemplo abaixo é feito com um único documento, mas pode facilmente escalar para biliões de documentos com Spark ou SQL. O código abaixo utiliza O PySpark (Python).

## <a name="use-case"></a>Use estojo

Com os tipos de dados modernos, os tipos de dados complexos são muitas vezes comuns para lidar e representam um desafio para os engenheiros de dados. Analisar esquemas aninhados e matrizes apresentam alguns desafios:
* Complexo para escrever consultas SQL
* Difícil renomear/lançar o tipo de dados de colunas aninhadas
* Problemas de desempenho de sucesso com objetos profundamente aninhados

Os Engenheiros de Dados precisam entender como processar eficientemente esses tipos de dados e torná-los facilmente acessíveis por todos.

No exemplo abaixo, a Faísca de Sinapse será usada para ler e transformar objetos através de quadros de dados numa estrutura plana. O sinapse SQL sem servidor é utilizado para consultar diretamente tais objetos e devolver esses resultados como uma tabela regular.

## <a name="what-are-arrays-and-nested-structures"></a>O que são matrizes e estruturas aninhadas?

O seguinte objeto vem do App Insight. Neste objeto, existem estruturas aninhadas, mas também matrizes que contêm estruturas aninhadas também.

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
Ao imprimir o esquema do quadro de dados desse objeto (chamado **df**) com o comando **df.printschema,** vemos a seguinte representação:

* a cor amarela representa estrutura aninhada
* a cor verde representa uma matriz com dois elementos

[![Origem schema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts e _etag foram adicionados no sistema à medida que o documento foi ingerido na loja transacional Azure Cosmos DB.

O quadro de dados acima conta para 5 colunas e apenas 1 linha. Após a transformação, o quadro de dados curado terá 13 colunas e 2 linhas em formato tabular.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Achate estruturas aninhadas e explodir matrizes com Faísca Apache

Com a Faísca De Sinapse, transformar estruturas aninhadas em colunas e elementos de matriz em múltiplas linhas, é fácil. Os passos abaixo podem ser usados por todos para a sua própria implementação.

[![Etapas de transformações de faíscas](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Passo 1**: Definimos uma função para achatar o esquema aninhado. Esta função pode ser utilizada sem alterações. Crie uma célula num caderno Pyspark com esta função:

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

**Passo 2**: utilize a função para aplainar o esquema aninhado do quadro de dados **df** numa nova estrutura de dados **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A função de visualização deve retornar 10 colunas e 1 linha. A matriz e os seus elementos aninhados ainda estão lá.

**Passo 3**: agora transformamos a matriz **context_custom_dimensions** no quadro de dados **df_flat** num novo **df_flat_explode**de data. No código abaixo, também definimos qual a coluna que selecionamos:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A função de visualização deve retornar o seguinte resultado: 10 colunas e 2 linhas. O próximo passo é aplainar os esquemas aninhados com a função definida no passo 1.

**Passo 4**: utilize a função para aplainar o esquema aninhado da estrutura de dados **df_flat_explode** num novo quadro de dados **df_flat_explode_flat:**
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A função de visualização deve apresentar 13 colunas e 2 linhas:

A função printSchema do quadro de dados df_flat_explode_flat devolve o seguinte resultado:

[![Schema final](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Leia matrizes e estruturas aninhadas diretamente com o sql sem servidor

A consulta, a criação de vistas e tabelas sobre esses objetos é possível com o SQL sem servidor.

Em primeiro lugar, dependendo da forma como os dados foram armazenados, os utilizadores devem utilizar a seguinte taxonomia. Tudo o que o MAI É específico do seu caso de utilização:

| GRANEL              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |'Parquet' (ADLSg2)|
| N'endpoint= https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ;conta= ACCOUNTNAME;BASE DE DADOS=NOME DE BASEDÁTE;COLLECTIONNAME;REGIONTOQUERY, SECRET='YOURSECRET' |'CosmosDB' (Ligação Sinapse)|



**SqL sem servidor** suportará o Linked Service for Azure Synapse Link for Azure Cosmos DB e AAD passthrough. A capacidade encontra-se atualmente em pré-visualização fechada para Synapse Link.

Substitua abaixo:
* 'YOUR BULK ABOVE' pela cadeia de ligação da fonte de dados a que se conecta
* 'O SEU TIPO ACIMA' pelo formato que utiliza para ligar à fonte

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

Existem dois tipos diferentes de operações feitas:
* A linha de código abaixo definirá a coluna chamada contextdataeventTime que se refere ao elemento aninhado: Context.Data.eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Esta linha definirá a coluna chamada contextdataeventTime que se refere ao elemento ninho: Contexto>Dados>eventTime

* **a aplicação cruzada** é usada para criar novas linhas para cada elemento sob a matriz e, em seguida, com define cada objeto aninhado semelhante ao primeiro ponto de bala: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Se a matriz tivesse 5 elementos com 4 estruturas aninhadas, o SQL sem servidor devolveria 5 linhas e 4 colunas.

SQL sem servidor pode consultar no local, mapear a matriz em 2 linhas e, exibir todas as estruturas aninhadas em colunas.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como consultar a Azure Synapse Link para Azure Cosmos DB com Faísca](./synapse-link/how-to-query-analytical-store-spark.md)
* [Tipos aninhados de parquet de consulta](./sql/query-parquet-nested-types.md) 
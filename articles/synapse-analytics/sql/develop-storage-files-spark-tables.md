---
title: Mesas de faíscas de consulta utilizando SQL on-demand (pré-visualização)
description: Visão geral de como consultar tabelas Spark usando SQL on-demand (pré-visualização)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 210e0b240eefd2dd3f8d1ac45c781959e47ab893
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198482"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Mesas de Faísca synapse Azure usando SQL on-demand (pré-visualização)

O SQL on-demand (pré-visualização) pode sincronizar automaticamente os metadados de piscinas Spark dentro do espaço de trabalho Synapse (pré-visualização). Será criada uma base de dados on-demand SQL para cada base de dados existente em piscinas Spark (pré-visualização). Para cada tabela externa Spark baseada em Parquet e localizada no Armazenamento Azure, é criada uma tabela externa na base de dados on-demand da SQL. Como tal, pode desligar as suas piscinas Spark e ainda consultar mesas externas Spark a pedido da SQL.

Quando uma mesa é dividida em Spark, os ficheiros em armazenamento são organizados por pastas. A SQL on-demand utilizará metadados de partição e apenas direciona as pastas e ficheiros relevantes para a sua consulta.

A sincronização dos metadados é configurada automaticamente para cada piscina Spark aprovisionada no espaço de trabalho Azure Synapse. Pode começar a consultar as tabelas externas spark instantaneamente.

Cada tabela externa Spark parquet localizada no Armazenamento Azure está representada com uma tabela externa num esquema dbo que corresponde a uma base de dados on-demand SQL. Para consultas de mesa externas spark, execute uma consulta que visa um [spark_table] externo. Antes de executar o exemplo abaixo, certifique-se de que tem acesso correto [à conta](develop-storage-files-storage-access-control.md) de armazenamento onde os ficheiros estão localizados.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Tipos de dados de faíscas para mapeamento de tipos de dados SQL

| Tipo de dados de faísca | Tipo de dados SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Tipo Curto       | smallint                    |
| ItegerType     | int                         |
| LongType        | bigint                      |
| Tipo flutuante       | real                        |
| Tipo duplo      | float                       |
| DecimalType     | decimal                     |
| Carimbo de tempoTipo   | datetime2                   |
| Tipo de data        | data                        |
| Tipo de corda      | varchar (max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| Tipo de matriz       | varchar (max)* (em JSON)** |
| MapType         | varchar (max)* (em JSON)** |
| StructType      | varchar (max)* (em JSON)** |

\*A colagem usada é Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType e StructType estão representados como JSONs.



## <a name="next-steps"></a>Próximos passos

Avançar para o artigo controlo de acesso ao [armazenamento](develop-storage-files-storage-access-control.md) para saber mais sobre o controlo de acesso ao armazenamento.

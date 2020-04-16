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
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424049"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Mesas de Faísca synapse Azure usando SQL on-demand (pré-visualização)

O SQL on-demand (pré-visualização) pode sincronizar automaticamente os metadados de piscinas Spark dentro do espaço de trabalho Synapse (pré-visualização). Será criada uma base de dados on-demand SQL para cada base de dados existente em piscinas Spark (pré-visualização). Para cada tabela Spark baseada em Parquet ou CSV, é criada uma tabela externa na base de dados on-demand sQL. Como tal, pode fechar as suas piscinas Spark e ainda consultar mesas Spark da SQL a pedido.

Quando uma mesa é dividida em Spark, os ficheiros em armazenamento são organizados por pastas. A SQL on-demand utilizará metadados de partição e apenas direciona as pastas e ficheiros relevantes para a sua consulta.

A sincronização dos metadados é configurada automaticamente para cada piscina Spark aprovisionada no espaço de trabalho Azure Synapse. Pode começar a consultar as mesas spark instantaneamente.

Cada mesa Spark é representada com uma tabela externa num esquema dbo que corresponde a uma base de dados on-demand SQL. Para consultas de mesa Spark, execute uma consulta que visa um [spark_table] externo. Antes de executar o exemplo abaixo, certifique-se de que tem acesso correto [à conta](develop-storage-files-storage-access-control.md) de armazenamento onde os ficheiros estão localizados.

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
| Tipo de data        | date                        |
| Tipo de corda      | varchar (max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| Tipo de matriz       | varchar (max)* (em JSON)** |
| MapType         | varchar (max)* (em JSON)** |
| StructType      | varchar (max)* (em JSON)** |

\*A colagem usada é Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType e StructType estão representados como JSONs.



## <a name="next-steps"></a>Passos seguintes

Avançar para o artigo controlo de acesso ao [armazenamento](develop-storage-files-storage-access-control.md) para saber mais sobre o controlo de acesso ao armazenamento.

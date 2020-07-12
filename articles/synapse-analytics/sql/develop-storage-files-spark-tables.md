---
title: Sincronizar a Faísca Apache para as definições de tabela externa Azure Synapse em SQL on-demand (pré-visualização)
description: Visão geral de como consultar tabelas spark usando SQL on demand (pré-visualização)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: db6b2d95bf8f38495296885d14260b9900af1d51
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247052"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Sincronizar a Faísca Apache para as definições de tabela externa Azure Synapse em SQL on-demand (pré-visualização)

O SQL on demand (pré-visualização) pode sincronizar automaticamente metadados da Apache Spark para piscinas Azure Synapse. Será criada uma base de dados SQL a pedido para cada base de dados existente em piscinas Spark (pré-visualização). 

Para cada tabela externa Spark baseada no Parquet e localizada no Azure Storage, é criada uma tabela externa na base de dados sql on-demand. Como tal, pode fechar as suas piscinas Spark e ainda consultar mesas externas Spark a partir de SQL a pedido.

Quando uma mesa é dividida em Spark, os ficheiros no armazenamento são organizados por pastas. O SQL on-demand utilizará metadados de partição e apenas direcionará as pastas e ficheiros relevantes para a sua consulta.

A sincronização de metadados é configurada automaticamente para cada pool spark alojado no espaço de trabalho Azure Synapse. Pode começar a consultar as tabelas externas da Spark instantaneamente.

Cada mesa externa de parquet spark localizada no Azure Storage é representada com uma tabela externa num esquema dbo que corresponde a uma base de dados a pedido do SQL. 

Para consultas de tabela externa Spark, executar uma consulta que visa um [spark_table externo]. Antes de executar o exemplo abaixo, certifique-se de que tem acesso correto [à conta de armazenamento](develop-storage-files-storage-access-control.md) onde os ficheiros estão localizados.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Adicione, deixe cair ou altere a coluna de tabela externa Spark não será refletida em tabela externa em SQL on-demand.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Tipos de dados Apache Spark para mapeamento de tipos de dados SQL

| Tipo de dados de faísca | Tipo de dados SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| CurtoTip       | smallint                    |
| InteiroType     | int                         |
| LongType        | bigint                      |
| Boia DeTiputype       | real                        |
| DuploTipo      | float                       |
| DecimalType     | decimal                     |
| TimetampType   | datetime2                   |
| DataType        | date                        |
| Tipo de Corda      | varchar (máx)*               |
| BinárioTip      | varbinário                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(máx)* (em JSON)** |
| MapType         | varchar(máx)* (em JSON)** |
| StructType      | varchar(máx)* (em JSON)** |

\*A colagem usada é Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType e StructType são representados como JSONs.



## <a name="next-steps"></a>Passos seguintes

Avance para o artigo [do Controlo de Acesso](develop-storage-files-storage-access-control.md) ao Armazenamento para saber mais sobre o controlo de acesso ao armazenamento.

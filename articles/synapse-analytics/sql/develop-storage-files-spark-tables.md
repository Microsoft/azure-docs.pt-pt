---
title: Sincronizar o Apache Spark para definições de tabela externa na piscina SQL sem servidor
description: Visão geral de como consultar tabelas spark usando piscina SQL sem servidor
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 057a69881b8b407e5d75fa3510ca1c3eb1830bc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446493"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Sincronizar a Faísca Apache para as definições de tabela externa do Azure Synapse na piscina SQL sem servidor

O pool SQL sem servidor pode sincronizar automaticamente metadados a partir de Apache Spark. Será criada uma base de dados de piscinas SQL sem servidor para cada base de dados existente em piscinas Apache Spark sem servidor. 

Para cada mesa externa Spark baseada no Parquet e localizada no Azure Storage, uma tabela externa é criada numa base de dados de piscinas SQL sem servidor. Como tal, pode desligar as suas piscinas Spark e ainda consultar mesas externas Spark a partir da piscina SQL sem servidor.

Quando uma mesa é dividida em Spark, os ficheiros no armazenamento são organizados por pastas. O pool SQL sem servidor utilizará metadados de partição e apenas direciona-se para as pastas e ficheiros relevantes para a sua consulta.

A sincronização de metadados é configurada automaticamente para cada conjunto Apache Spark sem servidor alojado no espaço de trabalho Azure Synapse. Pode começar a consultar as tabelas externas da Spark instantaneamente.

Cada mesa externa de parquet spark localizada no Azure Storage é representada com uma tabela externa num esquema dbo que corresponde a uma base de dados de piscinas SQL sem servidor. 

Para consultas de tabela externa Spark, executar uma consulta que visa um [spark_table externo]. Antes de executar o exemplo seguinte, certifique-se de que tem acesso correto [à conta de armazenamento](develop-storage-files-storage-access-control.md) onde os ficheiros estão localizados.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Adicione, deixe cair ou altere os comandos de tabela externo spark para uma coluna não será refletido na tabela externa na piscina SQL sem servidor.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Tipos de dados Apache Spark para mapeamento de tipos de dados SQL

| Tipo de dados de faísca | Tipo de dados SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Tipo Curto      | smallint                    |
| InteiroType     | int                         |
| LongType        | bigint                      |
| Boia DeTiputype       | real                        |
| DuploTipo      | float                       |
| DecimalType     | decimal                     |
| TimetampType   | datetime2                   |
| DataType        | data                        |
| Tipo de Corda      | varchar(max)\*               |
| BinárioTip      | varbinário                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(máx) \* (em JSON)\** |
| MapType         | varchar(máx) \* (em JSON)\** |
| StructType      | varchar(máx) \* (em JSON)\** |

\* A colagem usada é Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType e StructType são representados como JSONs.



## <a name="next-steps"></a>Passos seguintes

Avance para o artigo [do Controlo de Acesso](develop-storage-files-storage-access-control.md) ao Armazenamento para saber mais sobre o controlo de acesso ao armazenamento.

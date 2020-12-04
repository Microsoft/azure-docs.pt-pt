---
title: 'Resolução de problemas: Leitura de texto UTF-8 a partir de ficheiros CSV ou PARQUET utilizando piscina SQL sem servidor'
description: Ler texto UTF-8 a partir de ficheiros CSV ou PARQUET utilizando piscina SQL sem servidor em Azure Synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576422"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Resolução de problemas leitura texto UTF-8 de ficheiros CSV ou Parquet usando piscina SQL sem servidor em Azure Synapse Analytics

Este artigo fornece etapas de resolução de problemas para a leitura de texto UTF-8 a partir de ficheiros CSV ou Parquet usando piscina SQL sem servidor em Azure Synapse Analytics.

Quando o texto UTF-8 é lido a partir de um ficheiro CSV ou PARQUET utilizando a piscina SQL sem servidor, alguns caracteres especiais como ü e ö são incorretamente convertidos se a consulta devolver colunas VARCHAR com colagens não-UTF8. Este é um problema conhecido no SQL Server e Azure SQL. A colagem não-UTF8 é o padrão em Synapse SQL para que as consultas ao cliente sejam afetadas. Os clientes que usam caracteres ingleses padrão e algum subconjunto de caracteres latinos estendidos podem não notar os erros de conversão. A conversão incorreta é explicada mais detalhadamente em [Sempre use colações UTF-8 para ler texto UTF-8 em piscina SQL sem servidor](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Solução

A solução para esta questão é utilizar sempre a colagem UTF-8 ao ler texto UTF-8 dos ficheiros CSV ou PARQUET.

- Em muitos casos, basta definir a colisão UTF8 na base de dados (operação de metadados).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- Pode definir explicitamente a colagem na coluna VARCHAR em openrowset ou tabela externa:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- Se não especificou a colagem utf8 em tabelas externas que lêem os dados utf8, precisa de recriar tabelas externas impactadas e definir a colagem UTF8 nas colunas VARCHAR (operação de metadados).


## <a name="next-steps"></a>Passos seguintes

* [Arquivos de Consulta Parquet com Sinapse SQL](../sql/query-parquet-files.md)
* [Arquivos CSV de consulta com Sinaapse SQL](../sql/query-single-csv-file.md)
* [CETAS com Sinaapse SQL](../sql/develop-tables-cetas.md)
* [Quickstart: Use a piscina SQL sem servidor](../quickstart-sql-on-demand.md)

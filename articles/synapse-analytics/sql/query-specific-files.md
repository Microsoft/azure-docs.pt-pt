---
title: Utilização de metadados de ficheiros em consultas
description: A função OPENROWSET fornece informações de ficheiros e caminhos sobre cada ficheiro utilizado na consulta para filtrar ou analisar dados baseados no nome do ficheiro e/ou no caminho da pasta.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e8d7301799bfb4af9a0f5a6f242be929e8253d7c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744207"
---
# <a name="using-file-metadata-in-queries"></a>Utilização de metadados de ficheiros em consultas

O serviço De consulta a pedido SQL pode endereçar vários ficheiros e pastas conforme descrito nas [pastas Da Query e no artigo de vários ficheiros.](query-folders-multiple-csv-files.md) Neste artigo, aprende-se a utilizar informações de metadados sobre ficheiros e nomes de pastas nas consultas.

Por vezes, pode ser necessário saber qual o ficheiro ou fonte de pasta que se correlaciona a uma linha específica no conjunto de resultados.

Pode utilizar a função e devolver os nomes dos `filepath` `filename` ficheiros e/ou o caminho no conjunto de resultados. Ou pode usá-los para filtrar dados com base no nome do ficheiro e/ou no caminho da pasta. Estas funções são descritas na [função](develop-storage-files-overview.md#filename-function) de nome de ficheiro da secção sintaxe e na [função de pathpath](develop-storage-files-overview.md#filepath-function). Abaixo encontrará pequenas descrições ao longo de amostras.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** com uma fonte de dados que referencia a conta de armazenamento. Em seguida, inicialize os objetos executando o script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="functions"></a>Funções

### <a name="filename"></a>Nome de ficheiro

Esta função devolve o nome de ficheiro de que a linha é originária.

A amostra seguinte lê os ficheiros de dados do Táxi Amarelo de NYC para os últimos três meses de 2017 e devolve o número de passeios por ficheiro. A parte OPENROWSET da consulta especifica quais os ficheiros que serão lidos.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

O exemplo que se segue mostra como o *nome de ficheiro ()* pode ser usado na cláusula WHERE para filtrar os ficheiros a ler. Acede a toda a pasta na parte OPENROWSET da consulta e filtra ficheiros na cláusula WHERE.

Os seus resultados serão os mesmos que o exemplo anterior.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Arquivo

A função de path derevolta um caminho completo ou parcial:

- Quando chamado sem parâmetro, devolve o caminho de arquivo completo de que a linha provém.
- Quando chamado com um parâmetro, devolve parte do caminho que corresponde ao wildcard na posição especificada no parâmetro. Por exemplo, o valor do parâmetro 1 devolveria parte do caminho que corresponde ao primeiro wildcard.

A amostra seguinte lê ficheiros de dados do Táxi Amarelo de NYC para os últimos três meses de 2017. Devolve o número de passeios por caminho de arquivo. A parte OPENROWSET da consulta especifica quais os ficheiros que serão lidos.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

O exemplo que se segue mostra como o *processo* pode ser utilizado na cláusula WHERE para filtrar os ficheiros a ler.

Pode utilizar os wildcards na parte OPENROWSET da consulta e filtrar os ficheiros na cláusula WHERE. Os seus resultados serão os mesmos que o exemplo anterior.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Passos seguintes

No próximo artigo, aprenderá a [consultar ficheiros Parquet.](query-parquet-files.md)

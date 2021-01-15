---
title: 'Tutorial: Começar a analisar dados com piscina SQL sem servidor'
description: Neste tutorial, você vai aprender a analisar dados com um pool SQL sem servidor usando dados localizados em bases de dados Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209411"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analise os dados com piscina SQL sem servidor no Azure Synapse Analytics

Neste tutorial, você vai aprender a analisar dados com pool SQL sem servidor usando dados localizados em bases de dados Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analise os dados do táxi nyc no armazenamento de blob usando a piscina SQL sem servidor

1. No centro de **dados** em **Linked**, clique à direita no **Azure Blob Storage > Sample Datasets > nyc_tlc_yellow**, **New SQL Script**, e selecione SELECT TOP **100 linhas**
1. Isto criará um novo script SQL com o seguinte código:

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Clique **em Executar**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Analise os dados do táxi de NYC em bases de dados spark usando piscina SQL sem servidor

As tabelas nas bases de dados Spark são automaticamente visíveis e podem ser consultadas por piscina SQL sem servidor.

1. No Synapse Studio, vá ao centro **De Desenvolvimento** e crie um novo script SQL.
1. **Desactor Connect à** piscina SQL sem servidor **incorporada.**
1. Cole o texto seguinte no script e execute o script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > A primeira vez que você executar uma consulta que usa piscina SQL sem servidor, leva cerca de 10 segundos para a piscina SQL sem servidor para recolher os recursos SQL necessários para executar suas consultas. As consultas subsequentes serão muito mais rápidas.
  


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar dados em Armazenamento](get-started-analyze-storage.md)

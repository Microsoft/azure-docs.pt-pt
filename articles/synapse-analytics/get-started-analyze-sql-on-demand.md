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
ms.date: 07/20/2020
ms.openlocfilehash: 4ca9ababbeb7843f1a014a4bd51a5e24a74acbae
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322938"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analise os dados com piscina SQL sem servidor no Azure Synapse Analytics

Neste tutorial, você vai aprender a analisar dados com pool SQL sem servidor usando dados localizados em bases de dados Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analise os dados do táxi nyc no armazenamento de blob usando a piscina SQL sem servidor

1. No centro de **dados** em **Linked** , clique à direita no **Azure Blob Storage > Sample Datasets > nyc_tlc_yellow** e selecione **SELECT TOP 100 linhas**
1. Isto criará um novo script SQL com o seguinte código:

    ```
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
1. **Desactor Connect to** **to serverless SQL pool**.
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

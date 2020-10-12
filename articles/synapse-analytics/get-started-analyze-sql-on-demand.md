---
title: 'Tutorial: Começar a analisar dados com servidores SQL'
description: Neste tutorial, você vai aprender a analisar dados com SQL on demand usando dados localizados em bases de dados Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 9c42d1d988bc280d5e62c24f109225d91cb446ce
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893497"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analisar dados com SQL a pedido

Neste tutorial, você vai aprender a analisar dados com SQL sem servidor usando um pool SQL on-demand usando dados localizados em bases de dados Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>Analise os dados do táxi nyc no armazenamento de blob usando o pool a pedido do SQL

1. No centro de **dados** em **Linked**, clique à direita no **Azure Blob Storage > Sample Datasets > nyc_tlc_yellow** e selecione **SELECT TOP 100 linhas**
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

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analise os dados do táxi da NYC em bases de dados spark usando SQL on demand

As tabelas nas bases de dados Spark são automaticamente visíveis e podem ser consultadas pela SQL a pedido.

1. No Synapse Studio, vá ao centro **De Desenvolvimento** e crie um novo script SQL.
1. Definir **Ligar a** **SQL a pedido**.
1. Cole o texto seguinte no script e execute o script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > A primeira vez que você faz uma consulta que usa SQL on-demand, leva cerca de 10 segundos para o SQL a pedido para recolher os recursos SQL necessários para executar suas consultas. As consultas subsequentes serão muito mais rápidas.
  


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar dados em Armazenamento](get-started-analyze-storage.md)

---
title: 'Tutorial: Começar a analisar dados com uma piscina SQL sem servidor'
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
ms.openlocfilehash: c8a0ad3eb578280ea29d94dfbb5e74d873568541
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259647"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analisar dados com uma piscina SQL sem servidor

Neste tutorial, você vai aprender a analisar dados com piscina SQL sem servidor. 

## <a name="the-built-in-serverless-sql-pool"></a>A piscina SQL sem servidor incorporada

As piscinas SQL sem servidor permitem-lhe utilizar o SQL sem ter de reservar capacidade. A faturação de um pool SQL sem servidor baseia-se na quantidade de dados processados para executar a consulta e não no número de nós usados para executar a consulta.

Cada espaço de trabalho vem com uma piscina SQL sem servidor pré-configurada chamada **Built-in**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analise os dados do táxi nyc no armazenamento de blob usando a piscina SQL sem servidor

Nesta secção, você usará um pool SQL sem servidor para analisar os dados do NYC Taxi numa conta de Armazenamento Azure Blob.

1. No Estúdio Synapse, vá ao centro **de Desenvolvimento**
1. Crie um novo script SQL.
1. Cole o seguinte código no script.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Clique **em Executar**

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise os dados com uma piscina de faíscas sem servidor](get-started-analyze-spark.md)

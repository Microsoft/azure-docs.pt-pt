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
ms.openlocfilehash: 5f0a7477df2e281748c053ea8c7e7d3e79626296
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588023"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analisar dados com uma piscina SQL sem servidor

Neste tutorial, você vai aprender a analisar dados com pool SQL sem servidor usando dados localizados em bases de dados Spark. 

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
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Clique **em Executar**

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise os dados com uma piscina de faíscas sem servidor](get-started-analyze-spark.md)

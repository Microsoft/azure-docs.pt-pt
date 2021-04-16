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
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567558"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analisar dados com uma piscina SQL sem servidor

Neste tutorial, você vai aprender a analisar dados com piscina SQL sem servidor. 

## <a name="the-built-in-serverless-sql-pool"></a>A piscina SQL sem servidor incorporada

As piscinas SQL sem servidor permitem-lhe utilizar o SQL sem ter de reservar capacidade. A faturação de um pool SQL sem servidor baseia-se na quantidade de dados processados para executar a consulta e não no número de nós usados para executar a consulta.

Cada espaço de trabalho vem com uma piscina SQL sem servidor pré-configurada chamada **Built-in**. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analise os dados do TÁXI NYC com uma piscina SQL sem servidor

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
1. Clique em **Run** (Executar). 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise os dados com uma piscina de faíscas sem servidor](get-started-analyze-spark.md)

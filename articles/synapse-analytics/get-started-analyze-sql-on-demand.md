---
title: 'Tutorial: Começar a analisar dados com SQL on demand'
description: Neste tutorial, você vai aprender a analisar dados com SQL on demand usando dados localizados em bases de dados Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101812"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analisar dados com SQL a pedido

Neste tutorial, você vai aprender a analisar dados com SQL on demand usando dados localizados em bases de dados Spark. 

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
> [Analisar usando faísca](get-started-analyze-spark.md)

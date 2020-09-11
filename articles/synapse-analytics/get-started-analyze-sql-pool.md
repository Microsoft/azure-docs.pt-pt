---
title: 'Tutorial: Começar a analisar dados com piscina SQL'
description: Neste tutorial, você usará os dados da amostra de táxi nyc para explorar as capacidades analíticas da piscina SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 363f2934bbeec266c16711572620e03e69785f94
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007201"
---
# <a name="analyze-data-with-sql-pools"></a>Analisar dados com piscinas SQL

O Azure Synapse Analytics fornece-lhe a capacidade de analisar dados com piscina SQL. Neste tutorial, você usará os dados da amostra de táxi nyc para explorar as capacidades analíticas da piscina SQL.

## <a name="link-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Ligue os dados da amostra do TÁXI NYC à base de dados SQLDB1

1. No Synapse Studio, navegue para o centro **de dados** à esquerda.
1. Clique **+** e, em seguida, **selecione amostras de navegação.** Isto abrirá o **centro de amostras** e abrirá o **separador Datasets.**
1. Selecione **NYC Taxi & Limousine Commission - registos amarelos da viagem de táxi**. Este conjunto de dados contém mais de 1,5 mil milhões de linhas.
1. Clique **em Adicionar conjunto de dados**
1. No centro de **dados** em **'Linked'** verá um novo conjunto de dados neste **local, a azure Blob Storage > Sample Datasets > nyc_tlc_yellow**   
1. Nos **dados**da amostra de consulta com rótulo do cartão, selecione a piscina SQL chamada **SQLDB1**.


## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explore os dados do Táxi nyc na piscina SQL

1. No Estúdio Synapse, vá ao centro **de dados.**
1. Vá às **tabelas SQLDB1**  >  **Tables**. Verá várias mesas carregadas.
1. Clique à direita no **dbo. Tabela** de trip e selecione **Novo Script SQL**  >  **Selecione TOP 100 Rows**.
1. Aguarde enquanto um novo script SQL é criado e executado.
1. Note que no topo do script SQL **Connect** é automaticamente definido para a piscina SQL chamada **SQLDB1**.
1. Substitua o texto do script SQL por este código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Esta consulta mostra como as distâncias totais de viagem e a distância média da viagem se relacionam com o número de passageiros.
1. Na janela de resultados do script SQL, altere a **Visualização** para **Gráfico** para ver uma visualização dos resultados como um gráfico de linha.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar usando faísca](get-started-analyze-spark.md)


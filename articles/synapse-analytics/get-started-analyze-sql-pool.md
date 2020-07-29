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
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325224"
---
# <a name="analyze-data-with-sql-pools"></a>Analisar dados com piscinas SQL

O Azure Synapse Analytics fornece-lhe a capacidade de analisar dados com piscina SQL. Neste tutorial, você usará os dados da amostra de táxi nyc para explorar as capacidades analíticas da piscina SQL.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregue os dados da amostra do TÁXI NYC na base de dados SQLDB1

1. No Synapse Studio, no menu azul mais azul, selecione o ícone do ponto de interrogação (**?**
1. **Selecione Começar**  >  **A começar a começar o hub.**
1. Nos **dados**da amostra de consulta com rótulo do cartão, selecione a piscina SQL chamada **SQLDB1**.
1. Selecione **dados de consulta**. Aparece brevemente uma notificação de "carregar dados de amostra". Uma barra de estado azul-claro perto do topo do Synapse Studio indica que os dados estão a ser carregados em SQLDB1.
1. Depois que a barra de estado ficar verde, descarte-a.

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


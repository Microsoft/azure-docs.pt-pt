---
title: 'Tutorial: Começar a analisar dados com piscinas SQL dedicadas'
description: Neste tutorial, você usará os dados da amostra de táxi nyc para explorar as capacidades analíticas da piscina SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3df0959003923a9a4ff2347330fd5a4f22e52245
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300127"
---
# <a name="analyze-data-with-sql-dedicated-pools"></a>Analisar dados com piscinas dedicadas SQL

O Azure Synapse Analytics fornece-lhe a capacidade de analisar dados com uma piscina DE SQL dedicada. Neste tutorial, você usará os dados do Táxi nyc para explorar as capacidades de uma piscina SQL desatada.

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>Carregue os dados do táxi de NYC em SQLDB1

1. No Synapse Studio, navegue para o centro **de Desenvolvimento** e, em seguida, crie um novo script SQL
1. Introduza o seguinte código:
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. Este guião levará cerca de 1 minuto para ser executado. Carrega 2 milhões de linhas de dados de táxi de NYC numa mesa chamada **DBO. Viagem**

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explore os dados do Táxi nyc na piscina dedicada SQL

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


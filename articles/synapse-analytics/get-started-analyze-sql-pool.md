---
title: 'Tutorial: Começar a analisar dados com piscinas SQL dedicadas'
description: Neste tutorial, você usará os dados da amostra de táxi nyc para explorar as capacidades analíticas da piscina SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 54b650d598cf19e061465b3a4fa18d50808e7f29
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426166"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analisar dados com piscinas SQL dedicadas

O Azure Synapse Analytics fornece-lhe a capacidade de analisar dados com uma piscina DE SQL dedicada. Neste tutorial, você usará os dados do TÁXI NYC para explorar as capacidades de uma piscina SQL dedicada.

## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Carregue os dados do táxi de NYC em SQLPOOL1

1. No Synapse Studio, navegue no centro **De Desenvolvimento,** clique no **+** botão para adicionar novo recurso e, em seguida, crie um novo script SQL.
1. Selecione a piscina 'SQLPOOL1' (piscina criada no [PASSO 1](./get-started-create-workspace.md) deste tutorial) na lista de descida 'Connect to' acima do script.
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
1. Clique no botão Executar para executar o script.
1. Este guião terminará em menos de 60 segundos. Carrega 2 milhões de linhas de dados de táxi de NYC numa mesa chamada **DBO. Viagem.**

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explore os dados do Táxi nyc na piscina dedicada SQL

1. No Estúdio Synapse, vá ao centro **de dados.**
1. Deve ver uma base de dados chamada **SQLPOOL1.** Se não o vir, clique em **Refresh**.
1. Vá às **Mesas SQLPOOL1.**  >   
3. Clique à direita no **dbo. Tabela** de trip e selecione **Novo Script SQL**  >  **Selecione TOP 100 Rows**.
4. Aguarde enquanto um novo script SQL é criado e executado.
5. Note que no topo do script SQL **Connect** é automaticamente definido para a piscina SQL chamada **SQLPOOL1**.
6. Substitua o texto do script SQL por este código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Esta consulta mostra como as distâncias totais de viagem e a distância média da viagem se relacionam com o número de passageiros.
1. Na janela de resultados do script SQL, altere a **Visualização** para **Gráfico** para ver uma visualização dos resultados como um gráfico de linha.
    
    > [!NOTE]
    > Um espaço de trabalho habilitado a piscina SQL (anteriormente SQL DW) pode ser identificado através da ponta da ferramenta no centro de dados.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar usando faísca](get-started-analyze-spark.md)

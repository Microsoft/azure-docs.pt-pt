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
ms.date: 03/24/2021
ms.openlocfilehash: 267dc7c7d89bbecfbed127f4a46adb7cd9044bc4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309378"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analisar dados com piscinas SQL dedicadas

Neste tutorial, você usará os dados do TÁXI NYC para explorar as capacidades de uma piscina SQL dedicada.

## <a name="create-a-dedicated-sql-pool"></a>Crie uma piscina SQL dedicada

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas SQL**.
1. Selecione **Novo**
1. Para **o nome da piscina SQL** selecione **SQLPOOL1**
1. Para **nível de desempenho** escolha **DW100C**
1. Selecione **Rever + criar** > **Criar**. A sua piscina SQL dedicada estará pronta em poucos minutos. 

A sua piscina SQL dedicada está associada a uma base de dados SQL que também se chama **SQLPOOL1.**
1. Navegue para o  >  **espaço de trabalho de dados.**
1. Deve ver uma base de dados chamada **SQLPOOL1.** Se não o vir, clique em **Refresh**.

Uma piscina SQL dedicada consome recursos faturados desde que esteja ativo. Pode fazer uma pausa na piscina mais tarde para reduzir custos.

> [!NOTE] 
> Ao criar uma nova piscina SQL dedicada (anteriormente SQL DW) no seu espaço de trabalho, a página dedicada de fornecimento de piscinas SQL abrirá. O provisionamento terá lugar no servidor lógico SQL.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Carregue os dados do táxi de NYC em SQLPOOL1

1. No Synapse Studio, navegue no centro **De Desenvolvimento,** clique no **+** botão para adicionar novo recurso e, em seguida, crie um novo script SQL.
1. Selecione a piscina 'SQLPOOL1' (piscina criada no [PASSO 1](./get-started-create-workspace.md) deste tutorial) na lista de descida 'Connect to' acima do script.
1. Introduza o seguinte código:
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    --Uncomment the 4 lines below to create a stored procedure for data pipeline orchestration
    --CREATE PROC bulk_load_NYCTaxiTripSmall
    --AS
    --BEGIN
    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5, PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Clique no botão Executar para executar o script.
1. Este guião terminará em menos de 60 segundos. Carrega 2 milhões de linhas de dados de táxi de NYC numa mesa chamada **DBO. Viagem.**

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explore os dados do Táxi nyc na piscina dedicada SQL

1. No Estúdio Synapse, vá ao centro **de dados.**
1. Vá às **Mesas SQLPOOL1.**  >   
3. Clique à direita no **dbo. NYCTaxiTripSmall** tabela e selecione **New SQL Script**  >  **Select TOP 100 Rows**.
4. Aguarde enquanto um novo script SQL é criado e executado.
5. Note que no topo do script SQL **Connect** é automaticamente definido para a piscina SQL chamada **SQLPOOL1**.
6. Substitua o texto do script SQL por este código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Esta consulta mostra como as distâncias totais de viagem e a distância média da viagem se relacionam com o número de passageiros.
1. Na janela de resultados do script SQL, altere a **Visualização** para **Gráfico** para ver uma visualização dos resultados como um gráfico de linha.
    
## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar dados numa conta de Armazenamento Azure](get-started-analyze-storage.md)

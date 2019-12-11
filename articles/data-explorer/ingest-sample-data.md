---
title: Ingerir dados de exemplo no Azure Data Explorer
description: Saiba mais sobre como ingerir (carregar) dados de exemplo relacionados ao clima no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975181"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Início rápido: ingestão de dados de exemplo no Azure Data Explorer

Este artigo mostra como ingerir (carregar) os dados de exemplo em um Azure Data Explorer Database. Há [várias maneiras de ingerir dados](ingest-data-overview.md); Este artigo se concentra em uma abordagem básica que é adequada para fins de teste.

> [!NOTE]
> Você já terá esses dados se tiver concluído [a ingestão de dados usando a biblioteca do Azure data Explorer Python](python-ingest-data.md).

## <a name="prerequisites"></a>Pré-requisitos

[Um cluster e uma base de dados de teste](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Ingerir dados

O conjunto de dados de exemplo **StormEvents** contém dados relacionados com Meteorologia dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

1. Inicie sessão em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Na parte superior esquerda da aplicação, selecione **Adicionar cluster**.

1. Na caixa de diálogo **Adicionar cluster** , insira a URL do cluster no formato `https://<ClusterName>.<Region>.kusto.windows.net/`e, em seguida, selecione **Adicionar**.

1. Cole o comando a seguir e selecione **executar** para criar uma tabela StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Cole o comando a seguir e selecione **executar** para ingerir dados na tabela StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Após a conclusão da ingestão, Cole a consulta a seguir, selecione a consulta na janela e selecione **executar**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    A consulta retorna os seguintes resultados dos dados de exemplo ingeridos.

    ![Resultados da consulta](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Passos seguintes

* O [Azure data Explorer ingestão de dados](ingest-data-overview.md) para saber mais sobre os métodos de ingestão.
* [Início rápido: consultar dados no Azure data Explorer](web-query-data.md) Interface do usuário da Web.
* [Escreva consultas](write-queries.md) com linguagem de consulta Kusto.

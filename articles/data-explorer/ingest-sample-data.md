---
title: Ingerir dados de amostra no Azure Data Explorer
description: Saiba como ingerir (carregar) dados de amostras relacionados com o tempo no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74975181"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Quickstart: Ingerir dados de amostra no Azure Data Explorer

Este artigo mostra-lhe como ingerir (carregar) dados de amostra numa base de dados do Azure Data Explorer. Existem [várias formas de ingerir dados;](ingest-data-overview.md) este artigo centra-se numa abordagem básica que é adequada para efeitos de teste.

> [!NOTE]
> Já tem estes dados se tiver completado [os dados da Ingest utilizando a biblioteca Python do Explorador de Dados Do Azure.](python-ingest-data.md)

## <a name="prerequisites"></a>Pré-requisitos

[Um cluster e uma base de dados de teste](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Ingerir dados

O conjunto de dados de exemplo **StormEvents** contém dados relacionados com Meteorologia dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

1. Inscreva-se [https://dataexplorer.azure.com](https://dataexplorer.azure.com)em .

1. Na parte superior esquerda da aplicação, selecione **Adicionar cluster**.

1. Na caixa de diálogo do **cluster Adicionar,** `https://<ClusterName>.<Region>.kusto.windows.net/`introduza o URL do cluster no formulário, em seguida, selecione **Adicionar**.

1. Colhe no comando seguinte e selecione **Run** para criar uma tabela StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Colá-cola no comando seguinte e selecione **Executar** para ingerir dados na tabela StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Depois de a ingestão terminar, cola na seguinte consulta, selecione a consulta na janela e selecione **Executar**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    A consulta devolve os seguintes resultados dos dados da amostra ingeridos.

    ![Resultados da consulta](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Passos seguintes

* [Ingestão](ingest-data-overview.md) de dados do Azure Data Explorer para saber mais sobre métodos de ingestão.
* [Quickstart: Dados de consulta no Azure Data Explorer](web-query-data.md) Web UI.
* [Escreva consultas](write-queries.md) com linguagem de consulta kusto.

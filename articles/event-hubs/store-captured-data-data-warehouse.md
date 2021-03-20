---
title: 'Tutorial: Migrar dados do evento para Azure Synapse Analytics - Azure Event Hubs'
description: Descreve como usar a grelha e funções de evento azure para migrar os Centros de Eventos capturados para Azure Synapse Analytics.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854239"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Tutorial: Migrar dados de Centros de Eventos capturados para Azure Synapse Analytics usando Grade de Eventos e Funções Azure
O Azure Event Hubs [Capture](./event-hubs-capture-overview.md) permite-lhe capturar automaticamente os dados de streaming em Centros de Eventos num armazenamento Azure Blob ou armazenamento do Lago de Dados Azure. Este tutorial mostra-lhe como migrar dados de Centros de Eventos capturados de Storage para Azure Synapse Analytics usando uma função Azure que é desencadeada pela Grade de [Eventos](../event-grid/overview.md).

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Passos seguintes 
Pode utilizar ferramentas de visualização de dados avançadas com o armazém de dados para obter informações acionáveis.

Este artigo mostra como usar [o Power BI com a Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
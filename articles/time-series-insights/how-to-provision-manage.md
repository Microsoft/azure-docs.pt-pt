---
title: Gerir um ambiente Gen 2 - Azure Time Series | Microsoft Docs
description: Aprenda a gerir um ambiente Azure Time Series Insights Gen 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461900"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Gerir a Azure Time Series Insights Gen2

Depois de ter criado o seu ambiente Azure Time Series Insights Gen2 utilizando [o Azure CLI](./how-to-create-environment-using-cli.md) ou [o portal Azure,](./how-to-create-environment-using-portal.md)pode modificar as suas políticas de acesso e outros atributos ambientais de acordo com as suas necessidades empresariais.

## <a name="manage-the-environment"></a>Gerir o ambiente

Pode gerir o seu ambiente Azure Time Series Insights Gen2 utilizando o [portal Azure](https://portal.azure.com/). Existem algumas diferenças fundamentais entre um ambiente Gen2 e os ambientes Gen1 S1 ou Gen1 S2 a ter em conta quando gere o seu ambiente através do portal Azure:

* O painel de **visão geral** do portal Azure Gen2 tem as seguintes alterações:

  * A capacidade é removida porque não se aplica aos ambientes da Gen2.
  * A propriedade **de ID da série Time** é adicionada. Determina como os seus dados são divididos.
  * Os conjuntos de dados de referência são removidos.
  * O URL apresentado direciona-o para o [Azure Time Series Insights Explorer](./concepts-ux-panels.md).
  * O nome da sua conta Azure Storage está listado.

* O painel **de configuração** do portal Azure é removido porque as unidades de escala não se aplicam aos ambientes Azure Time Series Insights Gen2. No entanto, pode utilizar a **Configuração de Armazenamento** para configurar a loja quente recentemente introduzida.

* O painel de **dados** de referência do portal Azure é removido no Azure Time Series Insights Gen2 porque o conceito de dados de referência foi substituído pelo [Modelo de Séries de Tempo (TSM)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights Gen2 ambiente no portal Azure":::

## <a name="next-steps"></a>Passos seguintes

* Reveja a lista de [boas práticas de streaming](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)
* Entenda como [diagnosticar e resolver problemas](./how-to-diagnose-troubleshoot.md)

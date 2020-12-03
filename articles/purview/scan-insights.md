---
title: Digitalize informações sobre os seus dados em Azure Purview
description: Este guia de como ver e usar a verificação do Purview Insights nos seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ea12bf8a8d93f14c5364864b97d1173fe8602765
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554428"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Digitalize informações sobre os seus dados em Azure Purview

Este guia de como aceder, visualizar e filtrar relatórios de insight de verificação do Azure Purview para os seus dados.

Neste guia de como guiar, aprenderá a:

* Veja as informações da sua conta Desídua.
* Veja os seus exames.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar com as introspeções de Competência, certifique-se de que completou os seguintes passos:

* Crie os seus recursos Azure e povoe a conta com dados.
* Configurar e completar uma verificação na fonte de dados.

Para obter mais informações, consulte [Gerir fontes de dados em Azure Purview (Preview)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Use insights de digitalização de competências

No Azure Purview, pode registar e digitalizar tipos de origem. Pode ver o estado da digitalização ao longo do tempo em Insights de Digitalização. Os insights dizem-lhe quantos exames falharam, conseguiram ou foram cancelados num determinado período de tempo.

### <a name="view-scan-insights"></a>Ver insights de digitalização

1. Vá ao ecrã de instância **do Azure Purview** no portal Azure e selecione a sua conta 'Purview'.

1. Na página **'Vista Geral',** na secção **Get Started,** selecione o azulejo **open purview Studio.**

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Lançar Purview a partir do portal Azure":::

1. Na página **Inicial** do Tesouro, selecione o azulejo **'Ver insights'** para aceder à sua área **de** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: Insights.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Veja as suas ideias no portal Azure":::

1. Na área **de Insights,** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: selecione **Scans** para exibir o relatório de insights de verificação de **competências.**

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Ver KPI's de alto nível para mostrar a contagem de digitalizações por estado
 
Os KPI's de alto nível mostram que os exames totais são executados dentro de um período. O período de tempo é incumprido no final de 30 dias. No entanto, também pode selecionar os últimos sete dias. Com base no filtro de tempo, os valores do KPI refletem a contagem de digitalizações adequadamente.


Com base no valor do filtro de tempo selecionado, pode ver a distribuição de digitalizações bem sucedidas, falhadas e canceladas por semana ou durante o dia no gráfico.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Ver Insights de digitalização":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os relatórios de insight da Azure Purview com [a Asset Insights](./asset-insights.md)

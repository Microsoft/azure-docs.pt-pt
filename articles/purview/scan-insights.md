---
title: Digitalize informações sobre os seus dados em Azure Purview
description: Este guia de como ver e usar a verificação do Purview Insights nos seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548696"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Digitalize informações sobre os seus dados em Azure Purview

Este guia de como aceder, visualizar e filtrar relatórios de insight de verificação do Azure Purview para os seus dados.

Neste guia de como guiar, aprenderá a:

> [!div class="checklist"]
> * Veja as informações da sua conta Desídua.
> * Veja os seus exames.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar com as introspeções de Competência, certifique-se de que completou os seguintes passos:

* Crie os seus recursos Azure e povoe a conta com dados.
* Configurar e completar uma verificação na fonte de dados.

Para obter mais informações, consulte [Gerir fontes de dados em Azure Purview (Preview)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Use insights de digitalização de competências

No Azure Purview, pode registar e digitalizar tipos de origem. Pode ver o estado da digitalização ao longo do tempo em Insights de Digitalização. Os insights dizem-lhe quantos exames falharam, conseguiram ou foram cancelados num determinado período de tempo.

### <a name="view-scan-insights"></a>Ver informações das análises

1. Vá ao ecrã de instância **do Azure Purview** no portal Azure e selecione a sua conta 'Purview'.

1. Na página **'Vista Geral',** na secção **Get Started,** selecione o azulejo **open purview Studio.**

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Lançar Purview a partir do portal Azure":::

1. Na página **Inicial** do Tesouro, selecione o azulejo **'Ver insights'** para aceder à sua área **de** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: Insights.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Veja as suas ideias no portal Azure":::

1. Na área **de Insights,** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: selecione **Scans** para exibir o relatório de insights de verificação de **competências.**

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Ver KPI's de alto nível para mostrar a contagem de digitalizações por estado e mergulho profundo em cada digitalização
 
1. Os KPI's de alto nível mostram que os exames totais são executados dentro de um período. O período de tempo é incumprido no final de 30 dias. No entanto, também pode selecionar os últimos sete dias. Com base no filtro de tempo, os valores do KPI refletem a contagem de digitalizações adequadamente.


1. Com base no valor do filtro de tempo selecionado, pode ver a distribuição de digitalizações bem sucedidas, falhadas e canceladas por semana ou durante o dia no gráfico.

1. Na parte inferior do gráfico, existe uma ligação **para ver mais** para explorar mais. O link abre a página  **'Scan Status'** dentro da experiência Scan Insights. Aqui pode ver um nome de digitalização e o número de vezes que conseguiu, falhou ou foi cancelado nos últimos 30 dias.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Ver Estado de digitalização ao longo do tempo":::

4. Pode explorar uma digitalização específica, clicando no nome da **digitalização** que o ligará ao histórico de digitalização dentro da experiência **Sources** do Azure Purview. A partir da página de história da execução, você pode obter a identificação de execução que ajudará em mais investigação de falhas.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Ver detalhes da digitalização":::

5. Finalmente, pode voltar à página **de Estado do Scan Insights,** seguindo as migalhas de pão no canto superior esquerdo da página de histórico de execução.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Veja o histórico de análises"::: 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a Azure Purview **Insights** com [Data Insights](./concept-insights.md)

* Saiba mais sobre a experiência **Azure** Purview's Sources com [Gerir fontes de dados](./manage-data-sources.md)
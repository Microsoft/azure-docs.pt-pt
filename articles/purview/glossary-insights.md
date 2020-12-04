---
title: Relatório glossário sobre os seus dados utilizando a Purview Insights
description: Este guia de como ver e usar o glossário do Purview Insights nos seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576779"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Insights glossários sobre os seus dados em Azure Purview

Este guia de como aceder, visualizar e filtrar relatórios de insight glossário de Purview para os seus dados.

Neste guia de como guiar, aprenderá a:

> [!div class="checklist"]
> - Vá a Insights a partir da sua conta Desemorça
> - Obtenha uma visão dos seus dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar com as introspeções de Competência, certifique-se de que completou os seguintes passos:

- Crie os seus recursos Azure e povoe a conta com dados

- Configurar e completar uma verificação no tipo de origem

- Criar um glossário e anexar ativos a termos glossários

Para obter mais informações, consulte [Gerir fontes de dados em Azure Purview (Preview)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Use insights glossários de alças

Em Azure Purview, pode criar termos glossários e anexá-los a ativos. Mais tarde, pode ver a distribuição glossária em Glossary Insights. Isto diz-lhe o estado do seu glossário por termos ligados aos bens. Também lhe indica termos por estado e distribuição de funções por número de utilizadores.

**Para ver Glossário Insights:**

1. Vá ao ecrã de instância **do Azure Purview** [no portal Azure](https://aka.ms/purviewportal) e selecione a sua conta 'Purview'.

1. Na página **'Vista Geral',** na secção **Iniciar,** selecione o azulejo da conta **Launch Purview.**

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Lançar Purview a partir do portal Azure":::

1. Na página **Inicial** do Tesouro, selecione o azulejo **'Ver insights'** para aceder à sua área **de** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: Insights.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Veja as suas ideias no portal Azure":::

1. Na **Insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: área insights, selecione **Glossary** para exibir o relatório **de insights glossários** de Purview.

A página **Glossary Insights** apresenta as seguintes áreas:
1. **KPI's de alto nível** para mostrar termos glossários e utilizadores de catálogo

2. **Os termos brilhantes de topo e a contagem de ativos** mostram os top 5 termos glossários com ativos ligados a eles. Todos os outros ativos são contabilizados na categoria "Outros" no gráfico.

3. **Termos glossários por termo** mostram distribuição de termos glossários por estatuto como "Projeto", "Aprovado", "Alerta" e "Expirado". 

1. Passe ou clique na fatia do gráfico com um estado e note a contagem de termos com esse estado.

1. **A distribuição de funções por número de utilizadores** mostra a distribuição de funções por número de utilizadores por função na Purview.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Ver insights brilhantes":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os relatórios de insight do Azure Purview através da [Asset Insights](./asset-insights.md)
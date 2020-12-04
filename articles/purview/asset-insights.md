---
title: Insights de ativos sobre os seus dados em Azure Purview (pré-visualização)
description: Este guia de como ver e usar o ativo Purview Insights reportando os seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ac93d835b8dbdd5a12d031825dcb879160df5e95
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575351"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Informações sobre os seus dados em Azure Purview

Este guia de como aceder, visualizar e filtrar relatórios de insight do Purview Asset para os seus dados.

Neste guia de como guiar, aprenderá a:

> [!div class="checklist"]
> * Veja as informações da sua conta Desídua.
> * Veja os seus dados.
> * Faça mais detalhes sobre a contagem de ativos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar com as introspeções de Competência, certifique-se de que completou os seguintes passos:

* Crie os seus recursos Azure e povoe a conta com dados.

* Configurar e completar uma verificação no tipo de origem.

Para obter mais informações, consulte [Gerir fontes de dados em Azure Purview (Preview)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Use insights de ativos de competência

No Azure Purview, pode registar e digitalizar tipos de origem. Uma vez concluída a digitalização, pode ver a distribuição do ativo em Asset Insights, que lhe indica o estado do seu espólio de dados por classificação e conjuntos de recursos. Também lhe diz se há alguma alteração no tamanho dos dados.

> [!NOTE]
> Depois de ter digitalizado os seus tipos de origem, dê ao Asset Insights até uma hora para refletir os novos ativos.

1. Navegue para o seu recurso Azure Purview no portal Azure.

1. Na página **'Vista Geral',** na secção **Get Started,** selecione o azulejo **open purview Studio.**

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Lançar Purview a partir do portal Azure":::

1. Na página **Inicial** do Tesouro, selecione o azulejo **'Ver insights'** para aceder à sua área **de** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: Insights.

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Veja as suas ideias no portal Azure":::

1. Na **Insights** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: área insights, selecione **Ativos** para exibir o relatório de insights de ativos de **purview.**

### <a name="view-asset-insights"></a>Ver Insights de Ativos

1. A página principal **do Asset Insights** apresenta as seguintes áreas:

2. KPI's de alto nível para mostrar tipos de origem, ativos classificados e ativos descobertos
 
3. O primeiro gráfico mostra **ativos por tipo de origem.**

4. Veja a distribuição do seu ativo por tipo de fonte. Escolha uma classificação ou uma categoria de classificação inteira para ver a distribuição do ativo por tipo de origem. 
 
5. Para ver mais, **selecione Ver mais,** que exibe uma forma tabular dos tipos de origem e contagens de ativos. Os filtros de classificação são transportados para esta página.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Ver KPI's e gráfico em Insights de Ativos":::
 
6. Selecione uma fonte específica para a qual gostaria de ver as pastas de topo com contagens de ativos. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Selecione tipo de origem":::
 
7. Selecione o total de ativos contra a pasta superior dentro do tipo de origem acima selecionado.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Ver caminhos de arquivo":::

8. Veja a lista de ficheiros dentro da pasta. Navegue de volta para Insights usando as migalhas de pão.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Ver lista de ativos":::  

### <a name="file-based-source-types"></a>Tipos de origem baseados em ficheiros
O próximo par de gráficos em Asset Insights mostra uma distribuição de tipos de origem baseados em ficheiros. O primeiro gráfico, chamado **Tendência tamanho (GB) do tipo de ficheiro dentro dos tipos de origem,** mostra a tendência do tamanho do tipo de ficheiro superior nos últimos 30 dias. 
 
1. Escolha o seu tipo de origem para visualizar o tipo de ficheiro dentro da fonte. 
 
1. Selecione **Ver mais** para ver o tamanho atual dos dados, alterar o tamanho, contagem de ativos atual e alterar a contagem de ativos.
 
   > [!NOTE]
   > Se a varredura tiver sido executada apenas uma vez nos últimos 30 dias ou qualquer alteração de catálogo como adição/remoção de classificação ocorreu apenas uma vez em 30 dias, então a informação de alteração acima aparece em branco.

1. Consulte as pastas superiores com a troca de contagens de ativos quando clicar no tipo de origem.

1. Selecione o caminho para ver a lista de ativos.

O segundo gráfico nos tipos de origem baseados em ficheiros é **_Ficheiros não associados a um conjunto de recursos_**. Se espera que todos os ficheiros sejam enrolados num conjunto de recursos, este gráfico pode ajudá-lo a entender quais os ativos que não foram arregaçados. Os ativos em falta podem ser uma indicação do padrão de ficheiro errado na pasta. Siga os mesmos passos que em outros gráficos para ver mais detalhes nos ficheiros.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Ver ativos baseados em ficheiros":::  

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os relatórios de insight da Azure Purview com [a Scan Insights](./scan-insights.md)

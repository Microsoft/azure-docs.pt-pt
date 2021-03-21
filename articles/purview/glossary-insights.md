---
title: Relatório glossário sobre os seus dados utilizando a Purview Insights
description: Este guia de como ver e usar o glossário do Purview Insights nos seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095855"
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

1. Na  :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: área insights, selecione **Glossary** para exibir o relatório **de insights glossários** de Purview.

**A Glossary Insights** fornece-lhe como utilizador de negócios, informações valiosas para manter um glossário bem definido para a sua organização.

1. O relatório começa com **KPI's de alto nível** que mostra **_termos totais_*_ na sua conta Purview, _* Termos _aprovados sem ativos_ _ e *_*_termos expirados com ativos_**. Cada um destes valores irá ajudá-lo a identificar a saúde do seu Glossário.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Ver insights glossários KPI"::: 


2. **A** secção de termos (apresentada acima) mostra o seu termo como **_Rascunho_*_, _*_Aprovado_*_, _*_Alerta_*_, e _*_Expirado_** para termos com ativos e termos sem ativos.

3. Clique em **Ver mais** para ver os nomes do termo com vários estados e mais detalhes sobre **_Stewards_*_ e _*_Experts_**. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Instantâneo de termos com e sem ativos":::  

4. Quando clicar em "Ver mais" para ***Termos aprovados com ativos** _, Insights permitem-lhe navegar para a página de detalhes do termo _ *Glossário** a partir de onde pode navegar mais para a lista de ativos com os termos anexados. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Insights para glossário"::: 

4. Na página de insights glossários, consulte uma distribuição de **termos incompletos** por tipo de informação em falta. O gráfico mostra a contagem de termos com **_definição em falta_*_, _* Perito _desaparecido_*_, _* Administrador _desaparecido_ _ e *_*_Faltando vários_** campos.

1. Clique em ***Ver mais** _ de termos incompletos**, para ver os termos que têm informações em falta. Pode navegar para a página de detalhes do termo Glossário para inserir as informações em falta e garantir que o termo glossário está completo.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como criar um termo glossário através [do Glossário](./how-to-create-import-export-glossary.md)
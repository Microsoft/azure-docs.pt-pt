---
title: Visão geral da aplicação Azure Insights [ Dashboard] Microsoft Docs
description: Monitorize aplicações com insights de aplicação Azure e funcionalidade de painel de visão geral.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669901"
---
# <a name="application-insights-overview-dashboard"></a>Painel de visão geral de insights de aplicação

Application Insights sempre forneceu um painel de visão geral resumo para permitir uma avaliação rápida e rápida da saúde e desempenho da sua aplicação. O novo painel de visão geral proporciona uma experiência mais rápida e flexível.

## <a name="how-do-i-test-out-the-new-experience"></a>Como posso testar a nova experiência?

O novo painel de visão geral é agora lançado por defeito:

![Painel de pré-visualização de visão geral](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Melhor desempenho

A seleção do intervalo de tempo foi simplificada para uma simples interface de um clique.

![Intervalo de tempo](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

O desempenho global foi muito aumentado. Tem acesso a um clique para funcionalidades populares como **Search** e **Analytics.** Cada azulejo KPI de atualização dinâmica por padrão fornece uma visão sobre as características correspondentes do Application Insights. Para saber mais sobre pedidos falhados, selecione **Falhas** sob o cabeçalho **investigar:**

![Falhas](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Dashboard de aplicações

O painel de instrumentos de aplicação aproveita a tecnologia existente do dashboard dentro do Azure para fornecer uma visão única do painel de instrumentos da sua aplicação.

Para aceder ao dashboard predefinido, selecione Application _Dashboard_ no canto superior esquerdo.

![Vista do Dashboard](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Se esta for a primeira vez que acede ao painel de instrumentos, lançará uma visão predefinida:

![Vista do Dashboard](./media/overview-dashboard/0001-dashboard.png)

Pode manter a visão padrão, se quiser. Ou também pode adicionar e apagar do painel de instrumentos para melhor se adaptar às necessidades da sua equipa.

> [!NOTE]
> Todos os utilizadores com acesso ao recurso Application Insights partilham a mesma experiência do dashboard da Aplicação. As alterações efetuadas por um utilizador modificarão a vista para todos os utilizadores.

Para navegar de volta para a experiência de visão geral basta selecionar:

![Botão de visão geral](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Resolução de problemas

Se selecionar configurações de **azulejos configurar** e definir um intervalo de tempo personalizado superior a 31 dias o seu painel de instrumentos não irá apresentar além de 31 dias de dados, mesmo com a retenção de dados predefinido de 90 dias. Atualmente, não há sobra para este comportamento.

## <a name="next-steps"></a>Passos seguintes

- [Funis](../../azure-monitor/app/usage-funnels.md)
- [Retenção](../../azure-monitor/app/usage-retention.md)
- [Fluxos do Utilizador](../../azure-monitor/app/usage-flows.md)

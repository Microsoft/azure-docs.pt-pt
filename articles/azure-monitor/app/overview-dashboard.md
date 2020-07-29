---
title: Azure Application Insights Overview Dashboard [ Dashboard ] Microsoft Docs
description: Monitorize as aplicações com insights de aplicação Azure e funcionalidade do Painel de Controlo Geral.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8c8aa4460a545f032eaa60560de00efff27ae1ef
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322538"
---
# <a name="application-insights-overview-dashboard"></a>Painel de visão geral de insights de aplicação

A Application Insights sempre forneceu um painel de visão geral resumo para permitir uma avaliação rápida e geral da saúde e desempenho da sua aplicação. O novo painel de visão geral proporciona uma experiência mais flexível.

## <a name="how-do-i-test-out-the-new-experience"></a>Como posso testar a nova experiência?

O novo painel de visão geral agora é lançado por padrão:

![Painel de pré-visualização geral](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Melhor desempenho

A seleção do intervalo de tempo foi simplificada para uma interface simples de um clique.

![Intervalo de tempo](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

O desempenho global foi muito aumentado. Tem acesso a funcionalidades populares como **Search** e **Analytics.** Cada padrão de atualização dinâmica do azulejo KPI fornece informações sobre as funcionalidades correspondentes de Insights de Aplicação. Para saber mais sobre pedidos falhados, selecione **Falhas** no **cabeçalho Investigação:**

![Falhas](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Dashboard de aplicações

O painel de aplicações aproveita a tecnologia de painel existente dentro do Azure para proporcionar uma visão única e personalizável da saúde e desempenho da sua aplicação.

Para aceder ao painel de instrumentos predefinido, _selecione Painel de Aplicação_ no canto superior esquerdo.

![Vista do Dashboard](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Se esta for a primeira vez que aceda ao painel de instrumentos, lançará uma visão padrão:

![Vista do Dashboard](./media/overview-dashboard/0001-dashboard.png)

Pode manter a vista padrão se quiser. Ou também pode adicionar, e apagar do painel de instrumentos para melhor se adequar às necessidades da sua equipa.

> [!NOTE]
> Todos os utilizadores com acesso ao recurso Application Insights partilham a mesma experiência do painel de aplicações. As alterações efetuadas por um utilizador modificarão a vista para todos os utilizadores.

Para navegar de volta para a experiência geral basta selecionar:

![Botão de visão geral](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Resolução de problemas

Se selecionar configurações de **azulejos configurados** e definir um intervalo de tempo personalizado superior a 31 dias, o seu dashboard não apresentará mais de 31 dias de dados, mesmo com a retenção de dados predefinida de 90 dias. Atualmente, não há solução para este comportamento.

## <a name="next-steps"></a>Passos seguintes

- [Funis](./usage-funnels.md)
- [Retenção](./usage-retention.md)
- [Fluxos do Utilizador](./usage-flows.md)


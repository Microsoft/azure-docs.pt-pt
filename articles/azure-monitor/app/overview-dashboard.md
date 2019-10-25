---
title: Painel de visão geral do Aplicativo Azure insights | Microsoft Docs
description: Monitore aplicativos com informações de Aplicativo Azure e funcionalidade de painel Visão geral.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/03/2019
ms.openlocfilehash: 218ae158512326bcad8efd6c598f68d296fe9370
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819151"
---
# <a name="application-insights-overview-dashboard"></a>Painel de visão geral do Application Insights

A Application Insights sempre forneceu um painel de visão geral resumida para permitir uma avaliação rápida e instantânea da integridade e do desempenho do seu aplicativo. O novo painel de visão geral fornece uma experiência mais rápida e mais flexível.

## <a name="how-do-i-test-out-the-new-experience"></a>Como fazer testar a nova experiência?

O novo painel de visão geral agora é iniciado por padrão:

![Painel de visualização visão geral](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Melhor desempenho

A seleção de intervalo de tempo foi simplificada para uma interface simples de um clique.

![Intervalo de tempo](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

O desempenho geral aumentou muito. Você tem acesso com um clique a recursos populares, como **pesquisa** e **análise**. Cada bloco de KPI de atualização dinâmica padrão fornece informações sobre os recursos de Application Insights correspondentes. Para saber mais sobre as solicitações com falha, selecione **falhas** no cabeçalho de **investigação** :

![Falhas](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Dashboard de aplicações

O painel de aplicativos aproveita a tecnologia de painel existente no Azure para fornecer uma exibição de painel único totalmente personalizável da integridade e do desempenho do seu aplicativo.

Para acessar o painel padrão, selecione _painel do aplicativo_ no canto superior esquerdo.

![Vista do Dashboard](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Se esta for a primeira vez que você acessa o painel, ele abrirá uma exibição padrão:

![Vista do Dashboard](./media/overview-dashboard/0001-dashboard.png)

Você pode manter a exibição padrão se desejar. Ou você também pode adicionar e excluir do painel para atender melhor às necessidades de sua equipe.

> [!NOTE]
> Todos os usuários com acesso ao recurso de Application Insights compartilham a mesma experiência de painel de aplicativos. As alterações feitas por um usuário modificarão a exibição de todos os usuários.

Para navegar de volta para a experiência de visão geral, basta selecionar:

![Botão visão geral](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Resolução de problemas

Se você selecionar **definir configurações de bloco** e definir um intervalo de tempo personalizado acima de 31 dias, seu painel não será exibido além de 31 dias de dados, mesmo com a retenção de dados padrão de 90 dias. Atualmente, não há nenhuma solução alternativa para esse comportamento.

## <a name="next-steps"></a>Passos seguintes

- [Funis](../../azure-monitor/app/usage-funnels.md)
- [Retenção](../../azure-monitor/app/usage-retention.md)
- [Fluxos do Utilizador](../../azure-monitor/app/usage-flows.md)
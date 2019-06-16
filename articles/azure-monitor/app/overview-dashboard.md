---
title: Painel de descrição geral do Azure Application Insights | Documentos da Microsoft
description: Monitorizar aplicações com o Azure Application Insights e o Dashboard de descrição geral da funcionalidade.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: d1823779f8a8070149811e2349fc9f4281072d38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497158"
---
# <a name="application-insights-overview-dashboard"></a>Dashboard de descrição geral de informações de aplicações

O Application Insights sempre forneceu um painel de descrição geral de resumo para permitir a avaliação rápida em rápida do Estado de funcionamento e desempenho da sua aplicação. O novo dashboard de descrição geral fornece uma experiência mais flexível mais rápida.

## <a name="how-do-i-test-out-the-new-experience"></a>Como testar a nova experiência?

O novo dashboard de descrição geral agora é iniciado por predefinição:

![Painel de visualização de descrição geral](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Um melhor desempenho

Seleção do intervalo de tempo foi simplificada para uma interface de um clique simples.

![Intervalo de tempo](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Em geral desempenho tem sido aumentou bastante. Tem acesso de um clique a funcionalidades populares, como **pesquisa** e **Analytics**. Cada padrão dinamicamente a atualizar o mosaico KPI fornece informações sobre os recursos correspondentes do Application Insights. Para saber mais sobre a falha selecione pedidos **falhas** sob a **investigar** cabeçalho:

![Falhas](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Dashboard de aplicações

Dashboard de aplicação tira partido da tecnologia de dashboard existente do Azure para fornecer uma vista de painel único totalmente personalizável de seu estado de funcionamento do aplicativo e o desempenho.

Para aceder a dashboard predefinido, selecione _Dashboard de aplicações_ no canto superior esquerdo.

![Vista do Dashboard](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Se esta for a primeira vez que aceder ao dashboard, ele iniciará uma vista predefinida:

![Vista do Dashboard](./media/overview-dashboard/0001-dashboard.png)

Se gosta, pode manter a vista predefinida. Ou, também pode adicionar e eliminar a partir do dashboard para melhor se adequa às necessidades da sua equipa.

> [!NOTE]
> Todos os utilizadores com acesso ao recurso do Application Insights partilham a mesma experiência de dashboard de aplicações. As alterações feitas por um utilizador irão modificar a vista para todos os utilizadores.

Para navegar de volta para a experiência de descrição geral, basta selecionar:

![Botão de descrição geral](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Resolução de problemas

Se selecionou **Configure as definições do mosaico** e defina um intervalo de tempo personalizado em excesso de 31 dias, o dashboard não serão apresentadas para além dos 31 dias de dados, mesmo com o período de retenção de dados predefinido de 90 dias. Atualmente não é uma solução para esse comportamento.

## <a name="next-steps"></a>Passos Seguintes

- [Funis](../../azure-monitor/app/usage-funnels.md)
- [Retenção](../../azure-monitor/app/usage-retention.md)
- [Fluxos do Utilizador](../../azure-monitor/app/usage-flows.md)
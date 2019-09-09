---
title: Use um painel de recursos para executar uma revisão de acesso em PIM-Azure Active Directory | Microsoft Docs
description: Descreve como usar um painel de recursos para executar uma revisão de acesso no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804124"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Usar um painel de recursos para executar uma revisão de acesso no PIM

Você pode usar um painel de recursos para executar uma revisão de acesso no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). O painel do modo de exibição do administrador tem três componentes principais:

- Uma representação gráfica das ativações de função de recurso.
- Dois gráficos que exibem a distribuição de atribuições de função por tipo de atribuição.
- Uma área de dados pertencente a novas atribuições de função.

![Captura de tela do painel do modo de exibição de administrador, mostrando gráficos](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Captura de tela do painel de exibição do administrador, mostrando listas de dados](media/pim-resource-roles-overview-dashboards/role-settings.png)

A representação gráfica das ativações de função de recurso abrange os últimos sete dias. Esses dados têm o escopo definido para o recurso selecionado e exibem ativações para as funções mais comuns (proprietário, colaborador, administrador de acesso do usuário) e para todas as funções combinadas.

À direita do grafo de ativações, dois gráficos exibem a distribuição de atribuições de função por tipo de atribuição, para usuários e grupos. Você pode alterar o valor para uma porcentagem (ou vice-versa), selecionando uma fatia do gráfico.

Abaixo dos gráficos, você verá o número de usuários e grupos com novas atribuições de função nos últimos 30 dias e uma lista de funções classificadas pelo total de atribuições (em ordem decrescente).

## <a name="next-steps"></a>Passos Seguintes

- [Inicie uma revisão de acesso das funções de recursos no PIM](pim-resource-roles-start-access-review.md) 

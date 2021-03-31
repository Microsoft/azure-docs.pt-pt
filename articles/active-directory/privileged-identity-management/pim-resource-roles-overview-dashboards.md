---
title: Dashboards de recursos para avaliações de acesso em PIM - Azure AD | Microsoft Docs
description: Descreve como usar um dashboard de recursos para realizar uma revisão de acesso na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95eaa5b0e86a7470fc48edc23b2dbfb47e4b10c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743733"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Utilize um painel de recursos para realizar uma revisão de acesso na Gestão de Identidade Privilegiada

Pode utilizar um dashboard de recursos para realizar uma revisão de acesso na Gestão de Identidade Privilegiada (PIM). O painel Admin View no Azure Ative Directory (Azure AD) tem três componentes primários:

- Uma representação gráfica das ativações de funções de recursos
- Gráficos que exibem a distribuição de atribuições de funções por tipo de atribuição
- Uma área de dados contendo informações para novas atribuições de funções

![Screenshot do painel de instrumentos Admin View, mostrando gráficos e gráficos](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Screenshot do painel de instrumentos Admin View, mostrando listas de dados](media/pim-resource-roles-overview-dashboards/role-settings.png)

A representação gráfica das ativações de funções de recursos abrange os últimos sete dias. Estes dados são traçados para o recurso selecionado e exibem ativações para as funções mais comuns (proprietário, contribuinte, administrador de acesso ao utilizador) e para todas as funções combinadas.

De um lado do gráfico de ativações, dois gráficos exibem a distribuição de atribuições de funções por tipo de atribuição, tanto para utilizadores como para grupos. Pode alterar o valor para uma percentagem (ou vice-versa), selecionando uma fatia do gráfico.

Abaixo dos gráficos estão listados o número de utilizadores e grupos com novas atribuições de funções ao longo dos últimos 30 dias, e funções ordenadas por atribuições totais em ordem descendente.

## <a name="next-steps"></a>Passos seguintes

- [Inicie uma revisão de acesso para funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-start-access-review.md)

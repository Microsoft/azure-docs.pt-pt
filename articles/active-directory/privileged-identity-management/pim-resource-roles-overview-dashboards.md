---
title: Utilizar um dashboard de recursos para realizar uma revisão de acesso no PIM - Azure Active Directory | Documentos da Microsoft
description: Descreve como utilizar um dashboard de recursos para realizar uma revisão de acesso no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5404d1b85821458aedef64b72ae635ea49aa1ff
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602496"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Utilizar um dashboard de recursos para realizar uma revisão de acesso no PIM

Pode utilizar um dashboard de recursos para realizar uma revisão de acesso no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). O dashboard de vista de administração tem três componentes principais:

- Uma representação gráfica de ativações de função de recursos.
- Dois gráficos que exibem a distribuição de atribuições de funções por tipo de atribuição.
- Uma área de dados relativos a novas atribuições de função.

![Captura de ecrã do dashboard vista de administração, com gráficos](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Captura de ecrã do dashboard vista de administração, que mostra as listas de dados](media/pim-resource-roles-overview-dashboards/role-settings.png)

A representação gráfica de ativações de função de recursos aborda os últimos sete dias. Estes dados tem um âmbito para o recurso selecionado e exibe ativações para as funções mais comuns (proprietário, Contribuidor, administrador de acesso de utilizador) e para todas as funções combinadas.

À direita do gráfico ativações, dois gráficos apresentam a distribuição de atribuições de funções por tipo de atribuição, para utilizadores e grupos. Pode alterar o valor para uma percentagem (ou vice-versa), selecionando um setor do gráfico.

Abaixo dos gráficos, verá o número de utilizadores e grupos com novas atribuições de função ao longo dos últimos 30 dias e uma lista de funções ordenado por total de atribuições (em ordem decrescente).

## <a name="next-steps"></a>Passos Seguintes

- [Inicie uma revisão de acesso das funções de recursos no PIM](pim-resource-roles-start-access-review.md) 

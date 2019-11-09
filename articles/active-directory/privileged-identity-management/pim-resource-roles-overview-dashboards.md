---
title: Painéis de recursos para revisões de acesso no PIM – Azure AD | Microsoft Docs
description: Descreve como usar um painel de recursos para executar uma revisão de acesso no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847033"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Use um painel de recursos para executar uma revisão de acesso no Privileged Identity Management

Você pode usar um painel de recursos para executar uma revisão de acesso no Privileged Identity Management (PIM). O painel de exibição do administrador no Azure Active Directory (Azure AD) tem três componentes principais:

- Uma representação gráfica de ativações de função de recurso
- Gráficos que exibem a distribuição de atribuições de função por tipo de atribuição
- Uma área de dados que contém informações para novas atribuições de função

![Captura de tela do painel do modo de exibição de administrador, mostrando gráficos](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Captura de tela do painel de exibição do administrador, mostrando listas de dados](media/pim-resource-roles-overview-dashboards/role-settings.png)

A representação gráfica das ativações de função de recurso abrange os últimos sete dias. Esses dados têm o escopo definido para o recurso selecionado e exibem ativações para as funções mais comuns (proprietário, colaborador, administrador de acesso do usuário) e para todas as funções combinadas.

Em um lado do grafo ativações, dois gráficos exibem a distribuição de atribuições de função por tipo de atribuição, para usuários e grupos. Você pode alterar o valor para uma porcentagem (ou vice-versa), selecionando uma fatia do gráfico.

Abaixo dos gráficos estão listados o número de usuários e grupos com novas atribuições de função nos últimos 30 dias e as funções classificadas por atribuições totais em ordem decrescente.

## <a name="next-steps"></a>Passos seguintes

- [Iniciar uma revisão de acesso para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-start-access-review.md)

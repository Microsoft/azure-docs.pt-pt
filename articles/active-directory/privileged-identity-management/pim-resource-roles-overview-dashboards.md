---
title: 'Descrição geral: Realizar uma revisão de acesso no Privileged Identity Management para recursos do Azure | Documentos da Microsoft'
description: Este documento descreve como realizar uma revisão de acesso no PIM para recursos do Azure.
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
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9e9053c62f2ead3b6ae7d4ca3c6c38fd1063b8da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441512"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Utilizar um dashboard de recursos para realizar uma revisão de acesso

Pode utilizar um dashboard de recursos para realizar uma revisão de acesso no Privileged Identity Management (PIM) para recursos do Azure. O dashboard de vista de administração tem três componentes principais:

- Uma representação gráfica de ativações de função de recursos.
- Dois gráficos que exibem a distribuição de atribuições de funções por tipo de atribuição.
- Uma área de dados relativos a novas atribuições de função.

![Captura de ecrã do dashboard vista de administração, com gráficos](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Captura de ecrã do dashboard vista de administração, que mostra as listas de dados](media/azure-pim-resource-rbac/role-settings.png)

A representação gráfica de ativações de função de recursos aborda os últimos sete dias. Estes dados tem um âmbito para o recurso selecionado e exibe ativações para as funções mais comuns (proprietário, Contribuidor, administrador de acesso de utilizador) e para todas as funções combinadas.

À direita do gráfico ativações, dois gráficos apresentam a distribuição de atribuições de funções por tipo de atribuição, para utilizadores e grupos. Pode alterar o valor para uma percentagem (ou vice-versa), selecionando um setor do gráfico.

Abaixo dos gráficos, verá o número de utilizadores e grupos com novas atribuições de função ao longo dos últimos 30 dias e uma lista de funções ordenado por total de atribuições (em ordem decrescente).



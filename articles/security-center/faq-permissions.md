---
title: Azure Security Center FAQ - perguntas sobre permissões
description: Esta FAQ responde a perguntas sobre permissões no Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87824618"
---
# <a name="permissions"></a>Permissões

## <a name="how-do-permissions-work-in-azure-security-center"></a>Como funcionam as permissões no Centro de Segurança Azure?

O Azure Security Center utiliza [o controlo de acesso baseado em funções Azure (Azure RBAC),](../role-based-access-control/role-assignments-portal.md)que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços em Azure.

O Centro de Segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Security Center, só vê informações relacionadas com um recurso quando lhe é atribuída a função de Proprietário, Colaborador ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso.

Consulte [permissões no Centro de Segurança Azure](security-center-permissions.md) para saber mais sobre funções e ações permitidas no Centro de Segurança.



## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?

Para modificar uma política de segurança, tem de ser um Administrador de Segurança ou um Proprietário ou Contribuinte dessa subscrição.

Para aprender a configurar uma política de segurança, consulte [definição de políticas de segurança no Centro de Segurança Azure](tutorial-security-policy.md).
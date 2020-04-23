---
title: Azure Security Center FAQ - perguntas sobre permissões
description: Este FAQ responde a perguntas sobre permissões no Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças.
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
ms.openlocfilehash: 89b3233966ac53d6c384ca6bd5ac30322fbb0b02
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106598"
---
# <a name="permissions"></a>Permissões

## <a name="how-do-permissions-work-in-azure-security-center"></a>Como funcionam as permissões no Centro de Segurança Azure?

O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

O Security Center avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de Segurança, só se vê informações relacionadas com um recurso quando lhe é atribuído o papel de Proprietário, Colaborador ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso.

Consulte [permissões no Centro](security-center-permissions.md) de Segurança Azure para saber mais sobre papéis e permitiu ações no Centro de Segurança.



## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?

Para modificar uma política de segurança, deve ser um Administrador de Segurança ou um Proprietário ou Colaborador dessa subscrição.

Para aprender a configurar uma política de segurança, consulte definições de políticas de [segurança no Azure Security Center](tutorial-security-policy.md).
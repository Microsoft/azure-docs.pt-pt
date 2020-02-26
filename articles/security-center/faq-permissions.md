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
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599395"
---
# <a name="permissions"></a>Permissões

## <a name="how-do-permissions-work-in-azure-security-center"></a>Como funcionam as permissões no Centro de Segurança Azure?

O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, apenas verá informações relacionadas com a um recurso quando for atribuída a função de proprietário, contribuinte ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

Consulte [permissões no Centro](security-center-permissions.md) de Segurança Azure para saber mais sobre papéis e permitiu ações no Centro de Segurança.



## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?

Para modificar uma política de segurança, tem de ser um administrador de segurança ou um proprietário ou contribuinte dessa subscrição.

Para aprender a configurar uma política de segurança, consulte definições de políticas de [segurança no Azure Security Center](tutorial-security-policy.md).
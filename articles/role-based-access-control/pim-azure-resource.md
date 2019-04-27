---
title: Gerir o acesso aos recursos do Azure com o Azure AD Privileged Identity Management (PIM)
description: Saiba mais sobre a gestão de acesso a recursos do Azure através do Azure Active Directory Privileged Identity Management (PIM) e o controlo de acesso baseado em funções (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344619"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Gerir o acesso aos recursos do Azure com o Azure AD Privileged Identity Management

Para proteger contas com privilégios de ataques de cibersegurança maliciosos, pode utilizar o Azure Active Directory Privileged Identity Management (PIM) para reduzir o tempo de exposição dos privilégios e aumentar a visibilidade sobre seu uso por meio de relatórios e alertas. PIM faz isso ao limitar os utilizadores apenas levando em seus privilégios "just-in-time" (JIT), ou ao atribuir privilégios para uma duração abreviada após o qual os privilégios são revogados automaticamente. 

Agora pode utilizar o PIM com controlo de acesso baseado em função do Azure (RBAC) para gerir, controlar e monitorizar o acesso aos recursos do Azure. PIM pode gerir a associação de funções incorporadas e personalizadas para ajudá-lo a: 

- Ativar a pedido, "just-in-time" acesso aos recursos do Azure
- Expirar acesso a recursos automaticamente para utilizadores e grupos atribuídos
- Atribuir acesso temporário aos recursos do Azure para tarefas rápidas ou na chamada agendas
- Obtenha alertas quando os novos utilizadores ou grupos são atribuídos acesso a recursos e quando ativam atribuições elegíveis

Para obter mais informações, consulte [o que é o Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).
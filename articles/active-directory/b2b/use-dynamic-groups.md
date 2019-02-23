---
title: Grupos dinâmicos e de colaboração do B2B - Azure Active Directory | Documentos da Microsoft
description: Mostra como utilizar os grupos dinâmicos do Azure AD com a colaboração do Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066c3cc0a722ea96ff98aba613b493e2be77df0e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668481"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e de colaboração do Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
Configuração dinâmica de associação de grupo de segurança do Azure Active Directory (Azure AD) está disponível no [do portal do Azure](https://portal.azure.com). Os administradores podem definir regras para preencher os grupos que são criados no Azure AD com base em atributos de utilizador (por exemplo, userType, departamento ou país). Os membros podem ser automaticamente adicionados ou removidos de um grupo de segurança com base nos respetivos atributos. Estes grupos podem fornecer acesso a aplicações ou recursos na cloud (sites do SharePoint, documentos) e para atribuir licenças aos membros. Leia mais sobre os grupos dinâmicos no [dedicado a grupos no Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

O adequado [licenciamento do Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) é necessário para criar e utilizar grupos dinâmicos. Saiba mais no artigo [criar regras baseadas em atributos para associação a grupos dinâmicos no Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Quais são os grupos dinâmicos internos?
O **todos os utilizadores** grupo dinâmico permite que os administradores de inquilinos criar um grupo que contém todos os utilizadores no inquilino com um único clique. Por predefinição, o **todos os utilizadores** grupo inclui todos os utilizadores no diretório, incluindo membros e convidados.
No novo portal de administração do Azure Active Directory, pode optar por ativar os **todos os utilizadores** grupo na vista de definições de grupo.

![Permitir que mostra o grupo de todos os utilizadores definido como Yes](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Sistema de proteção de todos os grupo de utilizadores dinâmico
Por predefinição, o **todos os utilizadores** grupo contém os utilizadores de colaboração (convidado) B2B também. Pode proteger ainda mais sua **todos os utilizadores** grupo utilizando uma regra para remover os utilizadores convidados. A ilustração a seguir mostra a **todos os utilizadores** grupo modificado para excluir convidados.

![Regra de mostra em que tipo de utilizador não é igual a convidado](media/use-dynamic-groups/exclude-guest-users.png)

Poderá também considerar útil para criar um grupo dinâmico novo que contém apenas os utilizadores convidados, para que pode aplicar políticas (por exemplo, políticas de acesso condicional do Azure AD) para eles.
Um grupo como esse possível aparência:

![Regra de mostra onde o tipo de utilizador for igual a convidado](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Passos Seguintes

- [Propriedades do utilizador de colaboração B2B](user-properties.md)
- [A adição de um utilizador de colaboração do B2B a uma função](add-guest-to-role.md)
- [Acesso condicional para utilizadores de colaboração B2B](conditional-access.md)


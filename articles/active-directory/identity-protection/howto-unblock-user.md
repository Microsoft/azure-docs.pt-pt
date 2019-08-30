---
title: Como desbloquear usuários com Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como desbloquear usuários que foram bloqueados por uma política de Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e3756435703c4e8c887a4e7b9d4f75a6701d840
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126255"
---
# <a name="how-to-unblock-users"></a>Como: Desbloquear utilizadores

Com Azure Active Directory Identity Protection, você pode configurar políticas para bloquear os usuários se as condições configuradas forem satisfeitas. Normalmente, um usuário bloqueado entra em contato com o suporte técnico para tornar-se desbloqueado. Este artigo explica as etapas que você pode executar para desbloquear um usuário bloqueado.

## <a name="determine-the-reason-for-blocking"></a>Determinar o motivo do bloqueio

Como uma primeira etapa para desbloquear um usuário, você precisa determinar o tipo de política que bloqueou o usuário, pois suas próximas etapas estão dependendo dela.
Com o Azure Active Directory Identity Protection, um usuário pode ser bloqueado por uma política de risco de entrada ou uma política de risco do usuário.

Você pode obter o tipo de política que bloqueou um usuário do cabeçalho na caixa de diálogo que foi apresentada ao usuário durante uma tentativa de entrada:

| Política | Caixa de diálogo usuário |
| --- | --- |
| Risco de entrada |![Entrada bloqueada](./media/howto-unblock-user/02.png) |
| Risco de utilizador |![Conta bloqueada](./media/howto-unblock-user/104.png) |

Um usuário que está bloqueado pelo:

* Uma política de risco de entrada também é conhecida como entrada suspeita
* Uma política de risco do usuário também é conhecida como uma conta em risco

## <a name="unblocking-suspicious-sign-ins"></a>Desbloqueando entradas suspeitas

Para desbloquear uma entrada suspeita, você tem as seguintes opções:

1. **Entrar de um local ou dispositivo familiar** – um motivo comum para entradas suspeitas bloqueadas são tentativas de entrada de locais ou dispositivos desconhecidos. Os usuários podem determinar rapidamente se esse é o motivo do bloqueio tentando entrar de um dispositivo ou local familiar.
2. **Excluir da política** -se você considerar que a configuração atual da sua política de entrada está causando problemas para usuários específicos, poderá excluir os usuários dele. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Desabilitar política** – se você considerar que sua configuração de política está causando problemas para todos os seus usuários, você pode desabilitar a política. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Desbloqueando contas em risco

Para desbloquear uma conta em risco, você tem as seguintes opções:

1. **Redefinir senha** – você pode redefinir a senha do usuário. 
2. **Ignorar todas as detecções de risco** – a política de risco do usuário bloqueia um usuário se o nível de risco do usuário configurado para bloquear o acesso tiver sido atingido. Você pode reduzir o nível de risco de um usuário fechando manualmente as detecções de risco relatadas. 
3. **Excluir da política** -se você considerar que a configuração atual da sua política de entrada está causando problemas para usuários específicos, poderá excluir os usuários dele. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Desabilitar política** – se você considerar que sua configuração de política está causando problemas para todos os seus usuários, você pode desabilitar a política. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Passos Seguintes
 
Deseja saber mais sobre Azure AD Identity Protection? Confira [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

---
title: Como desbloquear utilizadores com o Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba como desbloquear utilizadores que foram bloqueados por uma política do Azure Active Directory Identity Protection.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, desbloquear utilizador
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d22fa7fd3964f99c426e8e21d34dcfdea6d1b36
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516914"
---
# <a name="how-to-unblock-users"></a>Como: Desbloquear utilizadores

Com o Azure Active Directory Identity Protection, pode configurar políticas para impedir que os utilizadores se as condições configuradas são cumpridas. Normalmente, um utilizador bloqueado contactos suporte técnico para se tornar desbloqueado. Este artigo explica os passos que pode efetuar para desbloquear um utilizador bloqueado.

## <a name="determine-the-reason-for-blocking"></a>Determinar a razão para bloquear
Como primeiro passo para desbloquear um utilizador, terá de determinar o tipo de política que bloqueou o utilizador porque os passos seguintes são depender.
Com o Azure Active Directory Identity Protection, um utilizador pode optar por bloqueado por uma política de risco de início de sessão ou uma política de risco do utilizador.

Pode obter o tipo de política que bloqueou a um utilizador a partir do cabeçalho na caixa de diálogo que foi apresentado ao utilizador durante uma tentativa de início de sessão:

| Política | Caixa de diálogo de utilizador |
| --- | --- |
| Risco de início de sessão |![O início de sessão bloqueado](./media/howto-unblock-user/02.png) |
| Risco de utilizador |![Conta bloqueada](./media/howto-unblock-user/104.png) |

Um utilizador que está bloqueado por:

* Uma política de risco de início de sessão é também conhecida como o início de sessão suspeito
* Uma política de risco do utilizador também é conhecido como uma conta em risco

## <a name="unblocking-suspicious-sign-ins"></a>A desbloquear suspeitos inícios de sessão
Para desbloquear um suspeito início de sessão, tem as seguintes opções:

1. **Iniciar sessão a partir de um dispositivo ou localização familiar** -um motivo comum para bloqueado suspeitos inícios de sessão são tentativas de início de sessão de localizações desconhecidas ou dispositivos. Os utilizadores podem determinar rapidamente se este é o motivo do bloqueio tentando inícios de sessão de um dispositivo ou localização familiar.
2. **Excluir da política** - se considera que a configuração atual da sua política de início de sessão está a causar problemas para utilizadores específicos, pode excluir os utilizadores do mesmo. Para obter mais informações, consulte [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Desativar política** - se considera que a configuração de política está a causar problemas para todos os seus utilizadores, pode desativar a política. Para obter mais informações, consulte [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>A desbloquear contas em risco
Para desbloquear uma conta em risco, tem as seguintes opções:

1. **Repor palavra-passe** -pode redefinir a senha do usuário. 
2. **Dispensar todos os eventos de risco** - os blocos de política de risco de utilizador um utilizador se o nível de bloqueio de acesso de risco do usuário configurado for atingido. Pode reduzir um utilizador do nível de risco fechando manualmente reportou eventos de risco. 
3. **Excluir da política** - se considera que a configuração atual da sua política de início de sessão está a causar problemas para utilizadores específicos, pode excluir os utilizadores do mesmo. Para obter mais informações, consulte [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Desativar política** - se considera que a configuração de política está a causar problemas para todos os seus utilizadores, pode desativar a política. Para obter mais informações, consulte [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Passos Seguintes
 
Pretende saber mais sobre o Azure AD Identity Protection? Confira [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

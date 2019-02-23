---
title: Delegar convites para colaboração B2B - Azure Active Directory | Documentos da Microsoft
description: Propriedades de utilizador de colaboração do Azure Active Directory B2B são configuráveis
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d40397f30b471699f42878a38c88efebcc6305
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674532"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegar convites para colaboração do Azure Active Directory B2B

Com a colaboração de empresa-empresa (B2B) do Azure Active Directory (Azure AD), não tem de ser um administrador global para enviar convites. Em vez disso, pode utilizar as políticas e delegar convites para os usuários cujas funções de permitir que os mesmos enviar convites. É uma forma nova importante para delegar convites de utilizadores através da função de utilizador que convida convidados.

## <a name="guest-inviter-role"></a>Função de utilizador que convida convidados
Podemos atribuir o utilizador à função de utilizador que convida convidados para enviar convites. Não tem de ser membro da função de administrador global para enviar convites. Por predefinição, os utilizadores regulares também podem invocar a API de convite, a menos que um administrador global desativada convites para usuários regulares. Um utilizador pode também invocar a API com o portal do Azure ou o PowerShell.

Eis um exemplo que mostra como utilizar o PowerShell para adicionar um utilizador à função de utilizador que convida convidados:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Controlar quem pode convidar

No Azure Active Directory, selecione **definições de utilizador**. Sob **utilizadores externos**, selecione **gerir definições de colaboração externa**.

> [!NOTE]
> O **definições de colaboração externa** também estão disponíveis a partir do **organizacionais relações** página. No Azure Active Directory, em **gerir**, aceda à **organizacionais relações** > **definições**.

![Definições de colaboração externa](./media/delegate-invitations/control-who-to-invite.png)

Com a colaboração B2B do Azure AD, o administrador de inquilinos pode definir as seguintes políticas de convite:

- Desativar a convites
- Apenas administradores e utilizadores na função de utilizador que convida convidados podem convidar
- Os administradores, a função de utilizador que convida convidados e os membros podem convidar
- Todos os usuários, inclusive convidados, podem convidar

Por predefinição, os inquilinos são definidos para #4. (Todos os usuários, inclusive convidados, podem convidar utilizadores B2B).

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicionar utilizadores de convidados de colaboração B2B sem convite](add-user-without-invite.md)
- [A adição de um utilizador de colaboração do B2B a uma função](add-guest-to-role.md)



---
title: Habilitar configurações de colaboração externa B2B-Azure AD
description: Saiba como habilitar a colaboração externa B2B Active Directory e gerenciar quem pode convidar usuários convidados. Use a função emissor de convite para delegar convites.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272907"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Habilitar a colaboração externa B2B e gerenciar quem pode convidar convidados

Este artigo descreve como habilitar a colaboração B2B do Azure Active Directory (Azure AD) e determinar quem pode convidar convidados. Por padrão, todos os usuários e convidados em seu diretório podem convidar convidados mesmo que não estejam atribuídos a uma função de administrador. As configurações de colaboração externas permitem ativar ou desativar os convites de convidado para diferentes tipos de usuários em sua organização. Você também pode delegar convites a usuários individuais atribuindo funções que lhes permitem convidar convidados.

## <a name="configure-b2b-external-collaboration-settings"></a>Definir configurações de colaboração externa B2B

Com a colaboração B2B do Azure AD, um administrador de locatários pode definir as seguintes políticas de convite:

- Desativar convites
- Somente administradores e usuários na função de convite do convidado podem convidar
- Administradores, a função emissor de convite para convidado e membros podem convidar
- Todos os usuários, incluindo convidados, podem convidar

Por padrão, todos os usuários, incluindo convidados, podem convidar usuários convidados.

### <a name="to-configure-external-collaboration-settings"></a>Para definir configurações de colaboração externas:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de locatários.
2. Selecione **Azure Active Directory** > **usuários** > **configurações do usuário**.
3. Em **usuários externos**, selecione **gerenciar configurações de colaboração externas**.
   > [!NOTE]
   > As **configurações de colaboração externa** também estão disponíveis na página **relações organizacionais** . Em Azure Active Directory, em **gerenciar**, vá para **relações organizacionais** > **configurações**.
4. Na página **configurações de colaboração externas** , escolha as políticas que você deseja habilitar.

   ![Configurações de colaboração externas](./media/delegate-invitations/control-who-to-invite.png)

  - **As permissões de usuários convidados são limitadas**: essa política determina as permissões para convidados em seu diretório. Selecione **Sim** para bloquear convidados de determinadas tarefas de diretório, como enumerar usuários, grupos ou outros recursos de diretório. Selecione **não** para dar aos convidados o mesmo acesso aos dados do diretório como usuários comuns em seu diretório.
   - **Administradores e usuários na função de emissor do convite para convidados podem convidar**: para permitir que administradores e usuários na função "emissor do convidado" convidem convidados, defina essa política como **Sim**.
   - **Os membros podem convidar**: para permitir que membros não administradores do seu diretório convidem convidados, defina essa política como **Sim**.
   - Os **convidados podem convidar**: para permitir que convidados convidem outros convidados, defina essa política como **Sim**.
   - **Habilitar a senha de uso único de email para convidados (versão prévia)** : para obter mais informações sobre o recurso de senha de uso único, consulte [autenticação de senha de uso único de email (versão prévia)](one-time-passcode.md).
   - **Restrições de colaboração**: para obter mais informações sobre como permitir ou bloquear convites para domínios específicos, consulte [permitir ou bloquear convites para usuários B2B de organizações específicas](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Atribuir a função de convite do convidado a um usuário

Com a função emissor de convite para convidado, você pode dar aos usuários individuais a capacidade de convidar convidados sem atribuir a eles um administrador global ou outra função de administrador. Atribua a função de convite do convidado a indivíduos. Em seguida, verifique se você definiu **Administradores e usuários na função emissor de convite de convidado pode convidar** para **Sim**.

Aqui está um exemplo que mostra como usar o PowerShell para adicionar um usuário à função de convite do convidado:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicionar usuários convidados de colaboração B2B sem um convite](add-user-without-invite.md)
- [Adicionando um usuário de colaboração B2B a uma função](add-guest-to-role.md)



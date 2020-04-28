---
title: Ativar as definições de colaboração externa B2B - Azure AD
description: Saiba como permitir a colaboração externa do Ative Directory B2B e gerequem quem pode convidar os utilizadores convidados. Use o papel de Convidado convidado para delegar convites.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272907"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Ativar a colaboração externa B2B e gerir quem pode convidar os hóspedes

Este artigo descreve como permitir a colaboração do Azure Ative Directory (Azure AD) B2B e determinar quem pode convidar os hóspedes. Por predefinição, todos os utilizadores e hóspedes do seu diretório podem convidar os hóspedes mesmo que não sejam designados para um papel de administrador. As definições de colaboração externa permitem-lhe ativar ou desligar os convites dos hóspedes para diferentes tipos de utilizadores da sua organização. Também pode delegar convites a utilizadores individuais, atribuindo funções que lhes permitam convidar os hóspedes.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurar as definições de colaboração externa B2B

Com a colaboração Azure AD B2B, um administrador inquilino pode definir as seguintes políticas de convite:

- Desligue os convites
- Apenas administradores e utilizadores no papel de Convidado convidado podem convidar
- Administradores, o papel de Convidado convidado, e membros podem convidar
- Todos os utilizadores, incluindo os hóspedes, podem convidar

Por predefinição, todos os utilizadores, incluindo os hóspedes, podem convidar os utilizadores convidados.

### <a name="to-configure-external-collaboration-settings"></a>Para configurar as definições de colaboração externa:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de inquilinos.
2. Selecione**as definições**de**utilizador** >  **do Diretório** > Ativo Azure .
3. Em **utilizadores externos,** selecione **Gerir as definições**de colaboração externa .
   > [!NOTE]
   > As definições de **colaboração externa** também estão disponíveis na página de **relações organizacionais.** No Diretório Ativo Azure, no âmbito **do Manage,** vá para as**Definições**de **Relacionamento** > Organizacional.
4. Na página de definições de **colaboração Externa,** escolha as políticas que pretende ativar.

   ![Definições de colaboração externa](./media/delegate-invitations/control-who-to-invite.png)

  - **As permissões dos utilizadores convidados são limitadas**: Esta política determina permissões para os hóspedes no seu diretório. Selecione **Sim** para bloquear os hóspedes de certas tarefas de diretório, como enumerar utilizadores, grupos ou outros recursos de diretório. Selecione **Não** para dar aos hóspedes o mesmo acesso aos dados de diretório que os utilizadores regulares no seu diretório.
   - **Os administradores e utilizadores no papel**de convidado podem convidar : Para permitir que os administradores e utilizadores na função "Convidado convidado" convidem os hóspedes, defino esta política para **Sim**.
   - **Os membros podem convidar**: Para permitir que membros não administradores do seu diretório convidem convidados, estabeleça esta política para **Sim**.
   - **Os hóspedes podem convidar**: Para permitir que os hóspedes convidem outros hóspedes, detete esta política para **Sim.**
   - Ativar o código de acesso de **uma vez por e-mail para os hóspedes (Pré-visualização)**: Para obter mais informações sobre a funcionalidade de senha única, consulte a autenticação de código de [acesso de uma vez por e-mail (pré-visualização)](one-time-passcode.md).
   - **Restrições**de colaboração : Para obter mais informações sobre permitir ou bloquear convites para domínios específicos, consulte [permitir ou bloquear convites a utilizadores B2B de organizações específicas](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Atribuir o papel de Convidado convidado a um utilizador

Com o papel de Convidado convidado, você pode dar aos utilizadores individuais a capacidade de convidar os hóspedes sem atribuir-lhes um administrador global ou outra função de administrador. Atribuir o papel de convidado convidado a indivíduos. Em seguida, certifique-se de que você define **Administradores e utilizadores no papel** de convidado convidado pode convidar para **Sim**.

Aqui está um exemplo que mostra como usar o PowerShell para adicionar um utilizador ao papel de Convidado convidado:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicione utilizadores convidados de colaboração B2B sem convite](add-user-without-invite.md)
- [Adicionar um utilizador de colaboração B2B a um papel](add-guest-to-role.md)



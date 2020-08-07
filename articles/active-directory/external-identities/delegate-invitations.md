---
title: Ativar as definições de colaboração externa B2B - Azure AD
description: Saiba como ativar a colaboração externa do Ative Directory B2B e gerir quem pode convidar utilizadores convidados. Use o papel de Convidado convidado para delegar convites.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6a2c1a9b908503ee5afc2687ebef473ffed626a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909545"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Ativar a colaboração externa B2B e gerir quem pode convidar convidados

Este artigo descreve como ativar a colaboração B2B do Azure Ative Directory (Azure AD) e determinar quem pode convidar os hóspedes. Por predefinição, todos os utilizadores e hóspedes do seu diretório podem convidar os hóspedes mesmo que não estejam designados para um papel de administração. As configurações de colaboração externa permitem-lhe ligar ou desligar os convites dos hóspedes para diferentes tipos de utilizadores na sua organização. Também pode delegar convites a utilizadores individuais, atribuindo funções que lhes permitam convidar os hóspedes.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurar definições de colaboração externa B2B

Com a colaboração Azure AD B2B, um administrador inquilino pode definir as seguintes políticas de convite:

- Desligue os convites
- Só os administradores e utilizadores no papel de Convidado convidado podem convidar
- Administradores, o papel de Convidado Convidado, e membros podem convidar
- Todos os utilizadores, incluindo convidados, podem convidar

Por padrão, todos os utilizadores, incluindo os hóspedes, podem convidar os utilizadores convidados.

### <a name="to-configure-external-collaboration-settings"></a>Para configurar definições de colaboração externa:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de inquilinos.
2. Selecione **Azure Active Directory**.
3. Selecione **identidades externas**  >  **Definições de colaboração externa .**
6. Na página **de definições de colaboração externa,** escolha as políticas que pretende ativar.

   ![Definições de colaboração externa](./media/delegate-invitations/control-who-to-invite.png)

  - **As permissões dos utilizadores convidados são limitadas:** Esta política determina permissões para os hóspedes do seu diretório. Selecione **Sim** para bloquear os hóspedes de certas tarefas do diretório, como enumerar utilizadores, grupos ou outros recursos de diretório. Selecione **Não** para dar aos hóspedes o mesmo acesso aos dados do diretório que os utilizadores regulares no seu diretório.
   - **Os administradores e utilizadores no papel de convidado convidado podem convidar**: Para permitir que os administradores e utilizadores no papel de "Convidado Convidado" convidem os hóspedes, dediminem esta política para **Sim**.
   - **Os membros podem convidar:** Para permitir que os membros não administrativos do seu diretório convidem convidados, desemomine esta política para **Sim**.
   - **Os hóspedes podem convidar**: Para permitir que os hóspedes convidem outros hóspedes, desemote esta política para **Sim.**
   - **Ativar o Email One-Time Passcode para os hóspedes (Pré-visualização)**: Para obter mais informações sobre a função de código de acesso único, consulte [a autenticação de código de acesso de email (pré-visualização)](one-time-passcode.md).
   - **Restrições de colaboração**: Para obter mais informações sobre permitir ou bloquear convites para domínios específicos, consulte [Convites de Permitir ou bloquear convites a utilizadores B2B de organizações específicas.](allow-deny-list.md)
   
   > [!NOTE]
   > Se **os Membros puderem convidar** está definido para **O** e **Admins e os utilizadores no papel de convidado convidado podem convidar** está definido para **Sim**, os utilizadores no papel de **Convidado Convidado** ainda poderão convidar os convidados.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Atribuir o papel de Convidado Convidado a um utilizador

Com o papel de Convidado Convidado, pode dar aos utilizadores individuais a capacidade de convidar os hóspedes sem lhes atribuir um papel de administrador global ou outro papel de administração. Atribua o papel de convidado convidado a indivíduos. Em seguida, certifique-se de que define **Os Administradores e os utilizadores no papel de convidado convidado pode convidar** para **Sim**.

Aqui está um exemplo que mostra como usar o PowerShell para adicionar um utilizador ao papel de Convidado Convidado:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicione utilizadores convidados de colaboração B2B sem convite](add-user-without-invite.md)
- [Adicionar um utilizador de colaboração B2B a uma função](add-guest-to-role.md)


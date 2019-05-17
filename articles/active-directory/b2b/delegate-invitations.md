---
title: Ativar as definições de colaboração externa B2B - Azure Active Directory | Documentos da Microsoft
description: Saiba como ativar a colaboração externa do Active Directory B2B e gerir quem pode convidar utilizadores convidados. Utilize a função de utilizador que convida convidados para delegar convites.
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
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812680"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Permita uma colaboração B2B externa e gerir quem pode convidar convidados

Este artigo descreve como ativar a colaboração B2B do Azure Active Directory (Azure AD) e determinar quem pode convidar convidados. Por predefinição, todos os utilizadores e convidados no seu diretório podem convidar convidados, mesmo que eles não estão atribuídos a uma função de administrador. Definições de colaboração externa permitem-lhe ativar convites de convidado ativada ou desativada para diferentes tipos de utilizadores na sua organização. Também pode delegar convites para utilizadores individuais através da atribuição de funções de quais eles podem convidar convidados.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurar as definições de colaboração externa de B2B

Com a colaboração B2B do Azure AD, o administrador de inquilinos pode definir as seguintes políticas de convite:

- Desativar a convites
- Apenas administradores e utilizadores na função de utilizador que convida convidados podem convidar
- Os administradores, a função de utilizador que convida convidados e os membros podem convidar
- Todos os usuários, inclusive convidados, podem convidar

Por predefinição, todos os usuários, inclusive convidados, podem convidar utilizadores convidados.

### <a name="to-configure-external-collaboration-settings"></a>Para configurar definições de colaboração externa:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) enquanto administrador inquilino.
2. Selecione **do Azure Active Directory** > **utilizadores** > **as definições de utilizador**.
3. Sob **utilizadores externos**, selecione **gerir definições de colaboração externa**.
   > [!NOTE]
   > O **definições de colaboração externa** também estão disponíveis a partir do **organizacionais relações** página. No Azure Active Directory, em **gerir**, aceda à **organizacionais relações** > **definições**.
4. Sobre o **definições de colaboração externa** página, selecione as políticas que pretende ativar.

   ![Definições de colaboração externa](./media/delegate-invitations/control-who-to-invite.png)

  - **Permissões dos utilizadores convidados são limitadas**: Esta política determina as permissões para convidados no seu diretório. Selecione **Sim** para convidados de bloco de determinadas tarefas de diretório, como enumerar utilizadores, grupos ou outros recursos de diretório. Selecione **não** para dar o convidados o mesmo acesso aos dados do diretório usuários regulares no seu diretório.
   - **Os administradores e utilizadores na função de autor do convite de convidado podem convidar**: Para permitir que os administradores e utilizadores na função de "Autor de convite" convidar participantes, definir esta política como **Sim**.
   - **Os membros podem convidar**: Para permitir que os membros de não-administrador do seu diretório convidar convidados, definir esta política como **Sim**.
   - **Os convidados podem convidar**: Para permitir que os convidados convidar outros convidados, defina esta política como **Sim**.
   - **Ativar o código de acesso de uso individual de E-Mail para convidados (pré-visualização)**: Para obter mais informações sobre a funcionalidade de código de acesso único, consulte [autenticação de código de acesso único por E-Mail (pré-visualização)](one-time-passcode.md).
   - **Restrições de colaboração**: Para obter mais informações sobre como permitir ou bloquear convites a domínios específicos, consulte [permitir ou bloquear convites aos utilizadores B2B de organizações específicos](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Atribuir a função de utilizador que convida convidados a um utilizador

Com a função de utilizador que convida convidados, pode conceder a utilizadores individuais a capacidade de convidar convidados sem lhes atribuir um administrador global ou de outra função de administrador. Atribua a função de autor do convite de convidado para indivíduos. Em seguida, certifique-se de que defina **administradores e utilizadores na função de autor podem convidar** ao **Sim**.

Eis um exemplo que mostra como utilizar o PowerShell para adicionar um utilizador à função de utilizador que convida convidados:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicionar utilizadores de convidados de colaboração B2B sem convite](add-user-without-invite.md)
- [A adição de um utilizador de colaboração do B2B a uma função](add-guest-to-role.md)



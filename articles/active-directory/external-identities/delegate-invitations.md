---
title: Ativar as definições de colaboração externa B2B - Azure AD
description: Saiba como ativar a colaboração externa do Ative Directory B2B e gerir quem pode convidar utilizadores convidados. Use o papel de Convidado convidado para delegar convites.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68ed67afc949b3a67936f20a098bca8b6906a598
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365553"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Ativar a colaboração externa B2B e gerir quem pode convidar convidados

Este artigo descreve como ativar a colaboração B2B do Azure Ative Directory (Azure AD), designar quem pode convidar os hóspedes e determinar as permissões que os utilizadores convidados têm no seu AD Azure. 

Por predefinição, todos os utilizadores e hóspedes do seu diretório podem convidar os hóspedes mesmo que não estejam designados para um papel de administração. As configurações de colaboração externa permitem-lhe ligar ou desligar os convites dos hóspedes para diferentes tipos de utilizadores na sua organização. Também pode delegar convites a utilizadores individuais, atribuindo funções que lhes permitam convidar os hóspedes.

O Azure AD permite-lhe restringir o que os utilizadores externos podem ver no seu diretório AD Azure. Por padrão, os utilizadores de hóspedes são definidos para um nível de permissão limitado que os bloqueia de enumerar utilizadores, grupos ou outros recursos de diretório, mas permite-lhes ver a adesão a grupos não escondidos. Uma nova definição de pré-visualização permite-lhe restringir ainda mais o acesso dos hóspedes, para que os hóspedes só possam ver as suas próprias informações de perfil. Para mais detalhes, consulte [permissões de acesso ao hóspede (pré-visualização)](../enterprise-users/users-restrict-guest-permissions.md).

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

4. Sob **as restrições de acesso ao utilizador do Hóspede (Pré-visualização)**, escolha o nível de acesso que pretende que os utilizadores dos hóspedes tenham:
  
   - **Os utilizadores convidados têm o mesmo acesso que os membros (mais inclusivos)**: Esta opção dá aos hóspedes o mesmo acesso aos recursos AD da Azure e dados de diretórios que os utilizadores membros.

   - **Os utilizadores convidados têm acesso limitado a propriedades e membros de objetos de diretório**: (Padrão) Esta definição bloqueia os hóspedes de determinadas tarefas de diretório, como enumerar utilizadores, grupos ou outros recursos de diretório. Os hóspedes podem ver a adesão de todos os grupos não escondidos.

   - **O acesso do utilizador do hóspede está restrito a propriedades e membros dos seus próprios objetos de diretório (mais restritivos)**: Com esta configuração, os hóspedes só podem aceder aos seus próprios perfis. Os hóspedes não estão autorizados a ver perfis, grupos ou membros do grupo de outros utilizadores.


5. Nas **definições do convite do hóspede,** escolha as definições apropriadas:

    ![Configurações de convite para convidados](./media/delegate-invitations/guest-invite-settings.png)

   - **Os administradores e utilizadores no papel de convidado convidado podem convidar**: Para permitir que os administradores e utilizadores no papel de "Convidado Convidado" convidem os hóspedes, dediminem esta política para **Sim**.

   - **Os membros podem convidar:** Para permitir que os membros não administrativos do seu diretório convidem convidados, desemomine esta política para **Sim**.

   - **Os hóspedes podem convidar**: Para permitir que os hóspedes convidem outros hóspedes, desemote esta política para **Sim.**

   > [!NOTE]
   > Se **os Membros puderem convidar** está definido para **O** e **Admins e os utilizadores no papel de convidado convidado podem convidar** está definido para **Sim**, os utilizadores no papel de **Convidado Convidado** ainda poderão convidar os convidados.

6. Por **e-mail código de acesso único para os hóspedes,** escolha as definições apropriadas (para mais informações, consulte [a autenticação de código de acesso de email uma vez):](one-time-passcode.md)

   - **Ativar automaticamente a senha de e-mail para os hóspedes em outubro de 2021**. (Predefinição) Se a funcionalidade de código de acesso de e-mail não estiver já ativada para o seu inquilino, será automaticamente ativada em outubro de 2021. Não é necessária mais nenhuma ação se pretender que a funcionalidade seja ativada nesse momento. Se já tiver ativado ou desativado a funcionalidade, esta opção não estará disponível.

   - **Ativar a senha de e-mail para os hóspedes com efeito agora**. Liga o recurso de senha de e-mail para o seu inquilino.

   - **Desativar a senha de e-mail uma vez para os hóspedes.** Desliga a funcionalidade de código de acesso de uma vez por e-mail para o seu inquilino e impede que a funcionalidade se apia em outubro de 2021.

   > [!NOTE]
   > Em vez das opções acima, verá o seguinte alternar se ativou ou desativou esta funcionalidade ou se já optou pela pré-visualização:
   >
   >![Ativar a senha de e-mail uma vez optada em](media/delegate-invitations/enable-email-otp-opted-in.png)

7. In **Enable guest self-service inscreva-se através de fluxos de utilizador (Preview)**, selecione **Sim** se pretender criar fluxos de utilizador que permitam que os utilizadores se inscrevam para apps. Para obter mais informações sobre esta definição, consulte [adicionar um fluxo de utilizador de inscrição de autosserviço a uma aplicação (Preview)](self-service-sign-up-user-flow.md).

    ![Autosserviço inscreva-se através da definição de fluxos de utilizador](./media/delegate-invitations/self-service-sign-up-setting.png)

7. Sob **restrições de Colaboração,** escolha se permite ou nega convites para os domínios especificados. Para obter mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas.](allow-deny-list.md)

    ![Definições de restrições de colaboração](./media/delegate-invitations/collaboration-restrictions.png)
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


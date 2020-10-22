---
title: Atribuir funções de Azure AD em PIM - Azure Ative Directory / Microsoft Docs
description: Saiba como atribuir funções de Azure AD na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/16/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69884b9e07172e9b25f4c14884be8713da23cbdb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369833"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Atribuir funções de Azure AD em Gestão de Identidade Privilegiada

Com o Azure Ative Directory (Azure AD), um administrador global pode fazer atribuições **permanentes** de funções de administrador aZure AD. Estas atribuições de funções podem ser criadas utilizando o [portal Azure](../roles/permissions-reference.md) ou utilizando [comandos PowerShell](/powershell/module/azuread#directory_roles).

O serviço Azure AD Privileged Identity Management (PIM) também permite que os administradores de funções privilegiadas façam atribuições de funções de administração permanentes. Além disso, os administradores de funções privilegiados podem tornar os utilizadores **elegíveis** para funções de administração Azure AD. Um administrador elegível pode ativar o papel quando precisar, e as suas permissões expiram assim que terminarem.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão de PIM

A partir de novembro de 2019, a parte de funções da AZure AD da Gestão de Identidade Privilegiada está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão de Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos deste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja na [função de administrador](../roles/permissions-reference.md#privileged-role-administrator) privilegiado.
1. Open **Azure AD Gestão de Identidade Privilegiada.** Se tiver um banner no topo da página geral, siga as instruções no separador **Nova versão** deste artigo. Caso contrário, siga as instruções no separador **versão anterior.**

  [![Selecione Azure AD > Gestão de Identidade Privilegiada.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="assign-a-role"></a>Atribuir um papel

Siga estes passos para tornar um utilizador elegível para um papel de administrador AZure AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que é membro da função de administrador de [função Privileged.](../roles/permissions-reference.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso à gestão da Gestão de Identidade Privilegiada, consulte [o Acesso de Concessão a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **Funções** para ver a lista de funções para permissões Azure AD.

    ![Screenshot da página "Roles" com a ação "Adicionar atribuições" selecionada.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **Selecione Adicionar atribuições** para abrir a página **de atribuições adicionar.**

1. **Selecione selecionar uma função** para abrir a página **De função.**

    ![Novo painel de atribuição](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecione uma função que pretende atribuir, selecione um membro a quem pretende atribuir para o papel e, em seguida, selecione **Next**.

1. Na lista de **tipos de atribuição** no painel de **definições de adesão,** selecione **Elegível** ou **Ativo**.

    - As atribuições **elegíveis** requerem que o membro do papel execute uma ação para usar o papel. As ações podem incluir a realização de uma verificação de autenticação multi-factor (MFA), a justificação do negócio ou a solicitação de aprovação dos aprovadores designados.

    - As atribuições **ativas** não requerem que o membro execute qualquer ação para usar o papel. Os membros designados como ativos têm sempre os privilégios atribuídos ao papel.

1. Para especificar uma duração específica da atribuição, adicione uma data de início e fim e caixas de hora. Quando terminar, **selecione Atribuir** para criar a nova atribuição de funções.

    ![Definições de membros - data e hora](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Após a atribuição da função, é apresentada uma notificação do estado de atribuição.

    ![Nova atribuição - Notificação](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Atribuir uma função com âmbito restrito

Para determinadas funções, o âmbito das permissões concedidas pode ser restringido a uma única unidade de administração, ao principal serviço ou à aplicação. Este procedimento é um exemplo se atribuir uma função que tenha o âmbito de uma unidade administrativa. Para obter uma lista de funções que suportam o âmbito através de uma unidade administrativa, consulte [atribuir funções a uma unidade administrativa](../roles/admin-units-assign-roles.md). Esta funcionalidade está neste momento a ser lançada para organizações Azure AD.

1. Inscreva-se no centro de administração do [Azure Ative Com](https://aad.portal.azure.com) permissões privilegiadas de Administrador de Função.

1. Selecione **Azure Ative Directory**  >  **Roles and administrators**.

1. Selecione o **Administrador do Utilizador.**

    ![O comando de atribuição Add está disponível quando abre uma função no portal](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Selecione **Adicionar atribuições**.

    ![Quando uma função suporta o âmbito, pode selecionar um âmbito](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Na página **'Adicionar atribuições',** pode:

   - Selecione um utilizador ou grupo para ser atribuído à função
   - Selecione o âmbito de função (neste caso, unidades administrativas)
   - Selecione uma unidade administrativa para o âmbito

Para obter mais informações sobre a criação de unidades administrativas, consulte [Adicionar e remover unidades administrativas.](../roles/admin-units-manage.md)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estes passos para atualizar ou remover uma atribuição de função existente.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **Roles** para ver a lista de papéis para Azure AD.

1. Selecione a função que pretende atualizar ou remover.

1. Encontre a atribuição de funções nos **separadores de funções elegíveis** ou **de funções ativas.**

    ![Atualizar ou remover a atribuição de funções](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Selecione **Update** ou **Remove** para atualizar ou remover a atribuição de funções.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Tornar um utilizador elegível para um papel

Siga estes passos para tornar um utilizador elegível para um papel de administrador AZure AD.

1. Selecione **Funções** ou **Membros**.

    ![abrir funções AD AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **Selecione Adicionar membro** para abrir Adicionar **membros geridos**.

1. **Selecione uma função,** selecione uma função que pretende gerir e, em seguida, **selecione Select**.

    ![Selecionar uma função](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **Selecione Selecione os membros,** selecione os utilizadores que pretende atribuir à função e, em seguida, **selecione Select**.

    ![Selecione um utilizador ou grupo para atribuir](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Nos **membros geridos Add**, selecione **OK** para adicionar o utilizador à função.

1. Na lista de funções, selecione o papel que acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o utilizador selecionado aparecerá na lista de membros como **Elegível** para o papel.

    ![Utilizador elegível para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o utilizador é elegível para o papel, informe-os de que podem ativá-lo de acordo com as instruções em Ativar as [minhas funções de AD Azure em Gestão de Identidade Privilegiada.](pim-how-to-activate-role.md)

    Os administradores elegíveis são convidados a registar-se para autenticação multi-factor Azure durante a ativação. Se um utilizador não puder registar-se no MFA, ou estiver a utilizar uma conta Microsoft (por @outlook.com exemplo), tem de os tornar permanentes em todas as suas funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de papel permanente

Por padrão, os novos utilizadores só são *elegíveis* para uma função de administração Azure AD. Siga estes passos se quiser tornar uma tarefa de função permanente.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma função **elegível** que pretende tornar permanente.

1. Selecione **Mais** e, em seguida, selecione **Fazer perm**.

    ![Tornar a atribuição de funções permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    O papel está agora listado como **permanente.**

    ![Lista de membros com alteração permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um utilizador de uma função

Pode remover os utilizadores das atribuições de funções, mas certifique-se de que há sempre pelo menos um utilizador que é um administrador global permanente. Se não tiver a certeza de quais os utilizadores que ainda precisam das suas atribuições de funções, pode [iniciar uma revisão de acesso para o papel](pim-how-to-start-security-review.md).

Siga estes passos para remover um utilizador específico de uma função de administração Azure AD.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma tarefa de função que pretende remover.

1. Selecione **Mais** e, em seguida, selecione **Remover**.

    ![Remover um papel](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que lhe pede para confirmar, selecione **Sim**.

    ![Confirme a remoção](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A tarefa é removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização na atribuição de funções

Se recentemente ativou a Gestão de Identidade Privilegiada para uma subscrição e obtém um erro de autorização quando tenta tornar um utilizador elegível para uma função de administrador Azure AD, pode ser porque o diretor de serviço MS-PIM ainda não tem as permissões apropriadas. O diretor de serviço MS-PIM deve ter a função [de Administrador de Acesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) ao Utilizador para atribuir funções a outros. Em vez de esperar até que a MS-PIM seja atribuída a função de Administrador de Acesso ao Utilizador, pode atribuí-la manualmente.

Siga estes passos para atribuir a função de Administrador de Acesso ao Utilizador ao principal do serviço MS-PIM para uma subscrição.

1. Inscreva-se no portal Azure como Administrador Global.

1. Escolha **todos os serviços** e, em seguida, **subscrições.**

1. Escolha a sua subscrição.

1. Escolha **Controlo de acesso (IAM)**.

1. Escolha **atribuições de funções** para ver a lista atual de atribuições de funções no âmbito de subscrição.

   ![Lâmina de controlo de acesso (IAM) para uma subscrição](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se o principal do serviço **MS-PIM** é atribuído à função de Administrador de Acesso ao **Utilizador.**

1. Caso contrário, escolha **Adicionar atribuição de função** para abrir o painel de atribuição de **funções Add.**

1. Na **Role** lista de drop-down role, selecione a função de Administrador de Acesso ao **Utilizador.**

1. Na lista **Select,** encontre e selecione o principal de serviço **MS-PIM.**

   ![Adicionar painel de atribuição de funções - Adicionar permissões para o principal de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Guardar** para atribuir o papel.

   Após alguns momentos, o diretor de serviço MS-PIM é atribuído à função de Administrador de Acesso ao Utilizador no âmbito de subscrição.

   ![Página de controlo de acesso mostrando atribuição de função de administrador de acesso do utilizador para o principal do serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Passos seguintes

- [Configurar configurações de função de administrador a Ad AD em Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
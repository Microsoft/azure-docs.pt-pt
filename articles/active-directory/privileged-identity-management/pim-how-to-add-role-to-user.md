---
title: Atribuir funções de AD Azure na PIM - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Saiba como atribuir funções de AD Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498876"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Atribuir funções de AD Azure na Gestão de Identidade Privilegiada

Com o Azure Ative Directory (Azure AD), um administrador global pode fazer atribuições **permanentes** de administração da Azure AD. Estas atribuições de funções podem ser criadas utilizando o [portal Azure](../users-groups-roles/directory-assign-admin-roles.md) ou utilizando [comandos PowerShell](/powershell/module/azuread#directory_roles).

O serviço azure AD Privileged Identity Management (PIM) também permite que administradores privilegiados façam atribuições permanentes de funções de administrador. Além disso, os administradores privilegiados podem tornar os utilizadores **elegíveis** para funções de administração da AD Azure. Um administrador elegível pode ativar o papel quando precisar, e então as suas permissões expiram assim que terminarem.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão do PIM

A partir de novembro de 2019, a parte da AD Azure da Privileged Identity Management está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão da Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos neste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja no papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Open **Azure AD Privileged Identity Management.** Se tiver um banner no topo da página de visão geral, siga as instruções no separador **versão Nova** deste artigo. Caso contrário, siga as instruções no separador versão **anterior.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="assign-a-role"></a>Atribuir um papel

Siga estes passos para tornar um utilizador elegível para um papel de administrador da AD Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que seja membro do papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso para gerir a Gestão de Identidade Privilegiada, consulte o [acesso de Grant a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Funções** para ver a lista de funções para permissões azure AD.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selecione **Adicionar membro** para abrir a nova página de **atribuição.**

1. **Selecione Selecione uma função** para abrir a página De funções.

    ![Novo painel de atribuição](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecione uma função que pretende atribuir e, em seguida, clique em **Selecionar**.

1. Selecione um membro a quem deseja atribuir à função e, em seguida, **selecione Select**.

1. Na lista do **tipo de atribuição** no painel de definições de **Membro,** selecione **Elegível** ou **Ativo**.

    - As atribuições **elegíveis** exigem que o membro do papel execute uma ação para utilizar o papel. As ações podem incluir a realização de um controlo de autenticação multifactor (MFA), o fornecimento de uma justificação de negócio, ou o pedido de aprovação dos aprovadores designados.

    - As atribuições **ativas** não requerem que o membro execute qualquer ação para usar o papel. Os membros designados como ativos têm sempre os privilégios atribuídos ao papel.

1. Se a atribuição for permanente (permanentemente elegível ou permanentemente atribuída), selecione a caixa de verificação **Permanente.**

    Dependendo das definições de função, a caixa de verificação pode não aparecer ou pode ser inmodificável.

1. Para especificar uma duração específica da atribuição, limpe a caixa de verificação e modifique as caixas de data e hora de início e/ou fim. Quando terminar, selecione **Done**.

    ![Definições de membros - data e hora](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Para criar a nova atribuição de funções, selecione **Adicionar**. É apresentada uma notificação do estado.

    ![Nova atribuição - Notificação](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de funções existente

Siga estes passos para atualizar ou remover uma atribuição de funções existente.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Funções** para ver a lista de funções para Azure AD.

1. Selecione a função que pretende atualizar ou remover.

1. Encontre a atribuição de funções nas **funções elegíveis** ou nos separadores de **funções ativas.**

    ![Atualizar ou remover a atribuição de funções](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de funções.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Tornar um utilizador elegível para um papel

Siga estes passos para tornar um utilizador elegível para um papel de administrador da AD Azure.

1. Selecione **Funções** ou **Membros**.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **Selecione Adicionar membro** para abrir Adicionar **membros geridos**.

1. **Selecione uma função,** selecione uma função que pretende gerir e, em seguida, selecione **Selecione**.

    ![Selecione um papel](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecione **os membros,** selecione os utilizadores que pretende atribuir à função e, em seguida, selecione **Selecione**.

    ![Selecione um papel](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em **Adicionar membros geridos,** selecione **OK** para adicionar o utilizador à função.

1. Na lista de funções, selecione o papel que acaba de atribuir para ver a lista de membros.

     Quando a função for atribuída, o utilizador selecionado aparecerá na lista de membros como **Elegível** para o papel.

    ![Utilizador elegível para um papel](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o utilizador é elegível para o papel, informe-os de que podem ativá-lo de acordo com as instruções em Ativar as [minhas funções de AD Azure na Gestão de Identidade Privilegiada.](pim-how-to-activate-role.md)

    Os administradores elegíveis são convidados a registar-se para a Autenticação Multi-Factor (MFA) do Azure durante a ativação. Se um utilizador não puder registar-se para o MFA, ou estiver a utilizar uma conta Microsoft (como @outlook.com), tem de os tornar permanentes em todas as suas funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de funções permanente

Por padrão, os novos utilizadores só são *elegíveis* para uma função de administrador ad-ad Azure. Siga estes passos se quiser tornar uma atribuição de funções permanente.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma função **Elegível** que pretende tornar permanente.

1. Selecione **Mais** e, em seguida, selecione **Faça perm**.

    ![Tornar a atribuição de funções permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    O papel está agora listado como **permanente.**

    ![Lista de membros com mudança permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remova um utilizador de uma função

Pode remover os utilizadores das atribuições de funções, mas certifique-se de que há sempre pelo menos um utilizador que é um administrador global permanente. Se não tiver a certeza de quais os utilizadores que ainda precisam das suas atribuições de funções, pode iniciar uma revisão de [acesso para o papel](pim-how-to-start-security-review.md).

Siga estes passos para remover um utilizador específico de uma função de administrador ad ad azure.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma atribuição de funções que pretende remover.

1. Selecione **Mais** e, em seguida, selecione **Remover**.

    ![Remover um papel](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que lhe pede para confirmar, selecione **Sim**.

    ![Remover um papel](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição do papel é removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se recentemente ativou a Gestão de Identidade Privilegiada para uma subscrição e obtém um erro de autorização quando tenta tornar um utilizador elegível para uma função de administrador ad ad azure, pode ser porque o diretor de serviço mS-PIM ainda não tem as permissões adequadas. O diretor de serviço mS-PIM deve ter a função de Administrador de [Acesso ao Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) para atribuir funções a outros. Em vez de esperar até que a MS-PIM seja atribuída a função de Administrador de Acesso ao Utilizador, pode atribuí-la manualmente.

Siga estes passos para atribuir a função de Administrador de Acesso ao Utilizador ao principal de serviço MS-PIM para uma subscrição.

1. Assine no portal Azure como Administrador Global.

1. Escolha **todos os serviços** e, em seguida, **Subscrições.**

1. Escolha a sua subscrição.

1. Escolha **Controlo de acesso (IAM)** .

1. Escolha **atribuições** de role para ver a lista atual de atribuições de papéis no âmbito da subscrição.

   ![Lâmina de controlo de acesso (IAM) para uma subscrição](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se o diretor de serviço **MS-PIM** tem a função de Administrador de **Acesso ao Utilizador.**

1. Caso contrário, escolha Adicionar a atribuição de **funções** para abrir o painel de atribuição de **funções Add.**

1. Na lista de abandono de **funções,** selecione a função de Administrador de Acesso ao **Utilizador.**

1. Na lista **Select,** encontre e selecione o diretor de serviço **MS-PIM.**

   ![Adicionar painel de atribuição de funções - Adicionar permissões para o diretor de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Guardar** para atribuir o papel.

   Após alguns momentos, o diretor de serviço MS-PIM é atribuído ao cargo de Administrador de Acesso ao Utilizador no âmbito da subscrição.

   ![Página de controlo de acesso mostrando a atribuição de função de administração de acesso ao utilizador para o principal de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Passos Seguintes

- [Configure Azure AD dispor de definições de funções na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
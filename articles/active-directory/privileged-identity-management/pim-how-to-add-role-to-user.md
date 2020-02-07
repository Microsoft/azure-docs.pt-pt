---
title: Atribuir funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/05/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bec73ee7286ea831920fc20bd46cf43c2874081
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047092"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Atribuir funções do Azure AD no Privileged Identity Management

Com o Azure Ative Directory (Azure AD), um administrador global pode fazer atribuições **permanentes** de administração da Azure AD. Estas atribuições de funções podem ser criadas utilizando o [portal Azure](../users-groups-roles/directory-assign-admin-roles.md) ou utilizando [comandos PowerShell](/powershell/module/azuread#directory_roles).

O serviço azure AD Privileged Identity Management (PIM) também permite que administradores privilegiados façam atribuições permanentes de funções de administrador. Além disso, os administradores privilegiados podem tornar os utilizadores **elegíveis** para funções de administração da AD Azure. Um administrador qualificado pode ativar a função quando precisa dela e suas permissões expiram quando são feitas.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja no papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Open **Azure AD Privileged Identity Management.** Se tiver um banner no topo da página de visão geral, siga as instruções no separador **versão Nova** deste artigo. Caso contrário, siga as instruções no separador versão **anterior.**

    ![Nova versão das funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="new-versiontabnew"></a>[Nova versão](#tab/new)

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do Azure AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que seja membro do papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso para gerir a Gestão de Identidade Privilegiada, consulte o [acesso de Grant a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Funções** para ver a lista de funções para permissões azure AD.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selecione **Adicionar membro** para abrir a nova página de **atribuição.**

1. **Selecione Selecione uma função** para abrir a página De funções.

    ![Painel de nova atribuição](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecione uma função que pretende atribuir e, em seguida, clique em **Selecionar**.

1. Selecione um membro a quem deseja atribuir à função e, em seguida, **selecione Select**.

1. Na lista do **tipo de atribuição** no painel de definições de **Membro,** selecione **Elegível** ou **Ativo**.

    - As atribuições **elegíveis** exigem que o membro do papel execute uma ação para utilizar o papel. As ações podem incluir a execução de uma verificação de MFA (autenticação multifator), o fornecimento de uma justificativa de negócios ou a solicitação de aprovação de aprovadores designados.

    - As atribuições **ativas** não requerem que o membro execute qualquer ação para usar o papel. Os membros atribuídos como ativos têm os privilégios atribuídos à função em todos os momentos.

1. Se a atribuição for permanente (permanentemente elegível ou permanentemente atribuída), selecione a caixa de verificação **Permanente.**

    Dependendo das configurações de função, a caixa de seleção pode não aparecer ou pode não ser modificável.

1. Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término. Quando terminar, selecione **Done**.

    ![Configurações de associações-data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Para criar a nova atribuição de funções, selecione **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição-notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **recursos Azure**.

1. Selecione o recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

1. Em Manage, selecione **Roles** para ver a lista de funções para os recursos Azure.

1. Selecione a função que você deseja atualizar ou remover.

1. Encontre a atribuição de funções nas **funções elegíveis** ou nos separadores de **funções ativas.**

    ![Atualizar ou remover atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de funções.

    Para obter informações sobre o alargamento de uma atribuição de funções, consulte Extend ou renovar funções de [recurso Azure na Gestão de Identidade Privilegiada.](pim-resource-roles-renew-extend.md)

# <a name="previous-versiontabprevious"></a>[Versão anterior](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Tornar um usuário qualificado para uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do Azure AD.

1. Selecione **Funções** ou **Membros**.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **Selecione Adicionar membro** para abrir Adicionar **membros geridos**.

1. **Selecione uma função,** selecione uma função que pretende gerir e, em seguida, selecione **Selecione**.

    ![Selecione um papel](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecione **os membros,** selecione os utilizadores que pretende atribuir à função e, em seguida, selecione **Selecione**.

    ![Selecione um papel](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em **Adicionar membros geridos,** selecione **OK** para adicionar o utilizador à função.

1. Na lista de funções, selecione a função que você acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o utilizador selecionado aparecerá na lista de membros como **Elegível** para o papel.

    ![Usuário qualificado para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o utilizador é elegível para o papel, informe-os de que podem ativá-lo de acordo com as instruções em Ativar as [minhas funções de AD Azure na Gestão de Identidade Privilegiada.](pim-how-to-activate-role.md)

    Os administradores qualificados são solicitados a se registrar na MFA (autenticação multifator) do Azure durante a ativação. Se um utilizador não puder registar-se para o MFA, ou estiver a utilizar uma conta Microsoft (como @outlook.com), tem de os tornar permanentes em todas as suas funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, os novos utilizadores só são *elegíveis* para uma função de administrador ad-ad Azure. Siga estas etapas se desejar tornar uma atribuição de função permanente.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma função **Elegível** que pretende tornar permanente.

1. Selecione **Mais** e, em seguida, selecione **Faça perm**.

    ![Tornar a atribuição de função permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    O papel está agora listado como **permanente.**

    ![Lista de membros com alteração permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

Você pode remover usuários das atribuições de função, mas certifique-se de que sempre haja pelo menos um usuário que seja um administrador global permanente. Se não tiver a certeza de quais os utilizadores que ainda precisam das suas atribuições de funções, pode iniciar uma revisão de [acesso para o papel](pim-how-to-start-security-review.md).

Siga estas etapas para remover um usuário específico de uma função de administrador do Azure AD.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma atribuição de função que você deseja remover.

1. Selecione **Mais** e, em seguida, selecione **Remover**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que lhe pede para confirmar, selecione **Sim**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função é removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se você tiver habilitado recentemente Privileged Identity Management para uma assinatura e receber um erro de autorização ao tentar tornar um usuário qualificado para uma função de administrador do Azure AD, pode ser porque a entidade de serviço MS-PIM ainda não tem as permissões apropriadas. O diretor de serviço mS-PIM deve ter a função de Administrador de [Acesso ao Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) para atribuir funções a outros. Em vez de aguardar até que o MS-PIM receba a função de administrador de acesso do usuário, você pode atribuí-lo manualmente.

Siga estas etapas para atribuir a função de administrador de acesso de usuário à entidade de serviço do MS-PIM para uma assinatura.

1. Entre no portal do Azure como um administrador global.

1. Escolha **todos os serviços** e, em seguida, **Subscrições.**

1. Escolha a sua subscrição.

1. Escolha **Controlo de acesso (IAM)** .

1. Escolha **atribuições** de role para ver a lista atual de atribuições de papéis no âmbito da subscrição.

   ![Folha de controle de acesso (IAM) para uma assinatura](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se o diretor de serviço **MS-PIM** tem a função de Administrador de **Acesso ao Utilizador.**

1. Caso contrário, escolha Adicionar a atribuição de **funções** para abrir o painel de atribuição de **funções Add.**

1. Na lista de abandono de **funções,** selecione a função de Administrador de Acesso ao **Utilizador.**

1. Na lista **Select,** encontre e selecione o diretor de serviço **MS-PIM.**

   ![Adicionar painel de atribuição de função-adicionar permissões para entidade de serviço do Microsoft PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Guardar** para atribuir o papel.

   Após alguns instantes, a entidade de serviço MS-PIM recebe a função de administrador de acesso do usuário no escopo da assinatura.

   ![Página controle de acesso mostrando a atribuição de função de administrador de acesso do usuário para a entidade de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Passos seguintes

- [Configure Azure AD dispor de definições de funções na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
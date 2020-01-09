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
ms.date: 09/17/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7dc54eccd7a5f01d8f3dd98144e0c4bf6269a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429848"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Atribuir funções do Azure AD no Privileged Identity Management

Com o Azure Active Directory (AD do Azure), um administrador global pode fazer atribuições de função de administrador do Azure AD **permanentes** . Essas atribuições de função podem ser criadas usando o [portal do Azure](../users-groups-roles/directory-assign-admin-roles.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço PIM (Azure AD Privileged Identity Management) também permite que administradores de função com privilégios façam atribuições de função de administrador permanentes. Além disso, os administradores de função com privilégios podem tornar os usuários **qualificados** para as funções de administrador do Azure AD. Um administrador qualificado pode ativar a função quando precisa dela e suas permissões expiram quando são feitas.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

    ![Nova versão das funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="previous-versiontabprevious"></a>[Versão anterior](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Tornar um usuário qualificado para uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do Azure AD.

1. Selecione **funções** ou **Membros**.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selecione **Adicionar membro** para abrir **adicionar membros gerenciados**.

1. Selecione **selecionar uma função**, selecione uma função que você deseja gerenciar e selecione **selecionar**.

    ![Selecionar uma função](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecione **selecionar Membros**, selecione os usuários que você deseja atribuir à função e, em seguida, selecione **selecionar**.

    ![Selecionar uma função](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em **adicionar membros gerenciados**, selecione **OK** para adicionar o usuário à função.

1. Na lista de funções, selecione a função que você acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o usuário selecionado aparecerá na lista de membros como **qualificado** para a função.

    ![Usuário qualificado para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o usuário está qualificado para a função, avise-o de que ele pode ativá-lo de acordo com as instruções em [Ativar minhas funções do Azure AD no Privileged Identity Management](pim-how-to-activate-role.md).

    Os administradores qualificados são solicitados a se registrar na MFA (autenticação multifator) do Azure durante a ativação. Se um usuário não puder se registrar para MFA ou estiver usando um conta Microsoft (como @outlook.com), você precisará torná-los permanentes em todas as suas funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, novos usuários só estão *qualificados* para uma função de administrador do Azure AD. Siga estas etapas se desejar tornar uma atribuição de função permanente.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma função **qualificada** que você deseja tornar permanente.

1. Selecione **mais** e, em seguida, selecione **tornar Perm**.

    ![Tornar a atribuição de função permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A função agora está listada como **permanente**.

    ![Lista de membros com alteração permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

Você pode remover usuários das atribuições de função, mas certifique-se de que sempre haja pelo menos um usuário que seja um administrador global permanente. Se você não tiver certeza de quais usuários ainda precisam de suas atribuições de função, você pode [iniciar uma revisão de acesso para a função](pim-how-to-start-security-review.md).

Siga estas etapas para remover um usuário específico de uma função de administrador do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma atribuição de função que você deseja remover.

1. Selecione **mais** e, em seguida, selecione **remover**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que solicita que você confirme, selecione **Sim**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função é removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se você tiver habilitado recentemente Privileged Identity Management para uma assinatura e receber um erro de autorização ao tentar tornar um usuário qualificado para uma função de administrador do Azure AD, pode ser porque a entidade de serviço MS-PIM ainda não tem as permissões apropriadas. A entidade de serviço do MS-PIM deve ter a função de [administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) para atribuir funções a outras pessoas. Em vez de aguardar até que o MS-PIM receba a função de administrador de acesso do usuário, você pode atribuí-lo manualmente.

Siga estas etapas para atribuir a função de administrador de acesso de usuário à entidade de serviço do MS-PIM para uma assinatura.

1. Entre no portal do Azure como um administrador global.

1. Escolha **todos os serviços** e, em seguida, **assinaturas**.

1. Escolha a sua subscrição.

1. Escolha **Controlo de acesso (IAM)** .

1. Escolha **atribuições de função** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha de controle de acesso (IAM) para uma assinatura](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se a entidade de serviço **MS-PIM** está atribuída à função **administrador de acesso do usuário** .

1. Caso contrário, escolha **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função** .

1. Na lista suspensa **função** , selecione a função Administrador de **acesso do usuário** .

1. Na lista de **seleção** , localize e selecione a entidade de serviço **MS-PIM** .

   ![Adicionar painel de atribuição de função-adicionar permissões para entidade de serviço do Microsoft PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **salvar** para atribuir a função.

   Após alguns instantes, a entidade de serviço MS-PIM recebe a função de administrador de acesso do usuário no escopo da assinatura.

   ![Página controle de acesso mostrando a atribuição de função de administrador de acesso do usuário para a entidade de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

# <a name="new-versiontabnew"></a>[Nova versão](#tab/new)

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar Privileged Identity Management, consulte [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **funções** para ver a lista de funções para permissões do Azure AD.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selecione **Adicionar membro** para abrir a página **nova atribuição** .

1. Selecione **selecionar uma função** para abrir a página Selecionar uma função.

    ![Painel de nova atribuição](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecione uma função que você deseja atribuir e clique em **selecionar**.

    A página **selecionar um membro ou grupo** é aberta.

1. Selecione um membro ou grupo que você deseja atribuir à função e, em seguida, selecione **selecionar**.

    ![Selecionar um membro ou um painel de grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    O painel configurações de associação é aberto.

1. Na lista **tipo de atribuição** , selecione **qualificado** ou **ativo**.

    ![Painel de configurações de associações](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management para recursos do Azure fornece dois tipos distintos de atribuição:

    - Atribuições **qualificadas** exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MFA (autenticação multifator), o fornecimento de uma justificativa de negócios ou a solicitação de aprovação de aprovadores designados.

    - As atribuições **ativas** não exigem que o membro execute qualquer ação para usar a função. Os membros atribuídos como ativos têm os privilégios atribuídos à função em todos os momentos.

1. Se a atribuição deve ser permanente (permanentemente qualificada ou permanentemente atribuída), marque a caixa de seleção **permanentemente** .

    Dependendo das configurações de função, a caixa de seleção pode não aparecer ou pode não ser modificável.

1. Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término.

    ![Configurações de associações-data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quando terminar, selecione **concluído**.

    ![Nova atribuição-adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de função, selecione **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição-notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **recursos do Azure**.

1. Selecione o recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

1. Em gerenciar, selecione **funções** para ver a lista de funções para recursos do Azure.

1. Selecione a função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **funções qualificadas** ou **funções ativas** .

    ![Atualizar ou remover atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecione **Atualizar** ou **remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre como estender uma atribuição de função, consulte [estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md).

 ---

## <a name="next-steps"></a>Passos seguintes

- [Definir as configurações de função de administrador do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
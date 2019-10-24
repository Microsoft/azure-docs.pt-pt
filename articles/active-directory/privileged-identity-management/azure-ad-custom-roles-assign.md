---
title: Atribuir uma função personalizada do Azure AD no Privileged Identity Management (PIM) | Microsoft Docs
description: Como atribuir uma função personalizada do Azure AD para o Privileged Identity Management de atribuição (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c4963e6563db166566cff41d381295a8a9e576b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756364"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Atribuir uma função personalizada do Azure AD no Privileged Identity Management

Este artigo mostra como usar o PIM (Privileged Identity Management) para criar atribuições just-in-time e Bound-time a funções personalizadas criadas para gerenciar aplicativos na experiência administrativa do Azure Active Directory (AD do Azure).

- Para obter mais informações sobre como criar funções personalizadas para delegar o gerenciamento de aplicativos no Azure AD, consulte [funções de administrador personalizadas no Azure Active Directory (versão prévia)](../users-groups-roles/roles-custom-overview.md).
- Se você ainda não usou o Privileged Identity Management, obtenha mais informações em [começar a usar o Privileged Identity Management](pim-getting-started.md).
- Para obter informações sobre como conceder a outro administrador acesso para gerenciar Privileged Identity Management, consulte [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> As funções personalizadas do Azure AD não são integradas às funções de diretório internas durante a visualização. Assim que o recurso estiver disponível, o gerenciamento de função ocorrerá na experiência de funções internas.

## <a name="assign-a-role"></a>Atribuir uma função

Privileged Identity Management pode gerenciar funções personalizadas que você pode criar no gerenciamento de aplicativo do Azure Active Directory (AD do Azure).  As etapas a seguir fazem uma atribuição qualificada a uma função de diretório personalizada.

1. Entre em [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal do Azure com uma conta de usuário atribuída à função de administrador de função com privilégios.
1. Selecione **funções personalizadas do Azure AD (versão prévia)** .

    ![Selecione a visualização de funções personalizadas do Azure AD para ver as atribuições de função qualificadas](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **funções** para ver uma lista de funções personalizadas para aplicativos do Azure AD.

    ![Selecione funções ver a lista de atribuições de função qualificadas](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Selecione **Adicionar membro** para abrir a página atribuição.
1. Para restringir o escopo da atribuição de função a um único aplicativo, selecione **escopo** para especificar um escopo de aplicativo.

    ![restringir o escopo de atribuições de função qualificadas no Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Selecione **selecionar uma função** para abrir a lista **selecionar uma função** .

    ![Selecione a função qualificada a ser atribuída a um usuário](./media/azure-ad-custom-roles-assign/select-role.png)

1. Selecione uma função que você deseja atribuir e clique em **selecionar**. A lista **selecionar um membro** é aberta.

    ![Selecione o usuário ao qual você está atribuindo a função](./media/azure-ad-custom-roles-assign/select-member.png)

1. Selecione um usuário que você deseja atribuir à função e, em seguida, clique em **selecionar**. A lista de **configurações de associação** é aberta.

    ![Definir o tipo de atribuição de função como qualificado ou ativo](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na página **configurações de associação** , selecione **qualificado** ou **ativo**:

    - Atribuições **qualificadas** exigem que o usuário atribuído à função execute uma ação antes que possa usar a função. As ações podem incluir a passagem de uma verificação de autenticação multifator, fornecendo uma justificativa de negócios ou solicitando aprovação de aprovadores designados.
    - Atribuições **ativas** não exigem que o usuário atribuído execute qualquer ação para usar a função. Os usuários ativos têm os privilégios atribuídos à função em todos os momentos.

1. Se a caixa de seleção **permanente** estiver presente e disponível (dependendo das configurações de função), você poderá especificar se a atribuição é permanente. Marque a caixa de seleção para tornar a atribuição permanentemente qualificada ou permanentemente atribuída. Desmarque a caixa de seleção para especificar uma duração de atribuição.
1. Para criar a nova atribuição de função, clique em **salvar** e em **Adicionar**. Uma notificação do status do processo de atribuição é exibida.

Para verificar a atribuição de função, em uma função aberta, selecione **atribuições**  > **atribuir** e verifique se a atribuição de função está corretamente identificada como qualificada ou ativa.

 ![Verifique se a atribuição de função está visível como qualificada ou ativa](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../users-groups-roles/directory-assign-admin-roles.md)

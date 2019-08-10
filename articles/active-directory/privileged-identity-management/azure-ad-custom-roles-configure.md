---
title: Configurar funções personalizadas do Azure AD no Privileged Identity Management (PIM) | Microsoft Docs
description: Como configurar funções personalizadas do Azure AD no Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc7e52a2ef92120da60f085c0afebeb8af714203
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947360"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Configurar funções personalizadas do Azure AD no Privileged Identity Management

Um administrador de função com privilégios pode alterar as configurações de função que se aplicam a um usuário quando eles ativam sua atribuição a uma função personalizada e a outros administradores de aplicativo que estão atribuindo funções personalizadas.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Entre em [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal do Azure com uma conta de usuário atribuída à função de administrador de função com privilégios.
1. Selecione **funções personalizadas do Azure AD (versão prévia)** .

    ![Selecione a visualização de funções personalizadas do Azure AD para ver as atribuições de função qualificadas](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Selecione **configuração** para abrir a página **configurações** . Selecione a função para as configurações que você deseja configurar.
1. Selecione **Editar** para abrir a página **configurações de função** .

    ![Abrir a função personalizada do Azure AD para editar as configurações](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Definições de função

Há várias configurações que você pode configurar.

### <a name="assignment-duration"></a>Duração da atribuição

Você pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificado ou ativo) ao definir as configurações para uma função. Essas opções se tornam a duração máxima padrão quando um membro é atribuído à função no Privileged Identity Management.

Você pode escolher uma dessas opções de duração de atribuição qualificadas.

- **Permitir atribuição qualificada permanente**: Os administradores podem atribuir a associação qualificada permanente.
- **Expirar atribuição qualificada após**: Os administradores podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificada.

Além disso, você pode escolher uma dessas opções de duração de atribuição ativas:

- **Permitir atribuição ativa permanente**: Os administradores podem atribuir uma associação ativa permanente.
- **Expirar atribuição ativa após**: Os administradores podem exigir que todas as atribuições ativas tenham uma data de início e de término especificada.

### <a name="require-azure-multi-factor-authentication"></a>Exigir autenticação multifator do Azure

Privileged Identity Management fornece imposição opcional da autenticação multifator do Azure para dois cenários distintos.

- **Exigir autenticação multifator na atribuição ativa**

  Se você quiser atribuir um membro a uma função por uma duração curta (um dia, por exemplo), pode ser muito lento para exigir que os membros atribuídos solicitem a ativação. Nesse cenário, Privileged Identity Management não pode impor a autenticação multifator quando o usuário ativa sua atribuição de função, pois elas já estão ativas na função desde o momento em que são atribuídas. Para garantir que o administrador que está atendendo à atribuição seja quem eles dizem que eles são, selecione a caixa de **atribuição exigir autenticação multifator no Active** .

- **Exigir autenticação multifator na ativação**

  Você pode exigir que usuários qualificados atribuídos a uma função se registrem na autenticação multifator do Azure antes que possam ser ativados. Esse processo garante que o usuário que está solicitando a ativação seja quem dizem que eles estão com certeza razoável. Impor essa opção protege funções críticas em situações em que a conta de usuário pode ter sido comprometida. Para exigir que um membro qualificado execute a autenticação multifator do Azure antes da ativação, selecione a caixa **exigir autenticação multifator no modo de ativação** .

Para obter mais informações, consulte [autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Duração máxima da ativação

Use o controle deslizante **duração máxima de ativação** para definir o tempo máximo, em horas, que uma função permanece ativa antes de expirar. Esse valor pode ser de, 1 e 24 horas.

### <a name="require-justification"></a>Exigir justificação

Você pode exigir que os membros insiram uma justificativa na atribuição ativa ou quando eles forem ativados. Para exigir justificação, marque a caixa de seleção **exigir justificação de inatividade** ou requerer **justificativa na ativação** .

### <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Selecione a caixa de seleção **exigir aprovação para ativar** .
1. Selecione **selecionar aprovadores** para abrir a lista **selecionar um membro ou grupo** .

    ![Abrir a função personalizada do Azure AD para editar as configurações](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Selecione pelo menos um membro ou grupo e clique em **selecionar**. Você deve selecionar pelo menos um Aprovador. Não há aprovadores padrão. Suas seleções aparecerão na lista de aprovadores selecionados.
1. Depois de especificar as configurações de função, selecione **Atualizar** para salvar as alterações.

## <a name="next-steps"></a>Passos Seguintes

- [Ativar uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definições de função no Azure AD](../users-groups-roles/directory-assign-admin-roles.md)

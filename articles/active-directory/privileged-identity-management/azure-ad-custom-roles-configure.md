---
title: Configure Azure AD papel personalizado - Gestão de Identidade Privilegiada (PIM)
description: Como configurar funções personalizadas da Azure AD na Gestão de Identidade Privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77498685"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Configure Funções personalizadas da AD Azure na Gestão de Identidade Privilegiada

Um administrador privilegiado pode alterar as definições de funções que se aplicam a um utilizador quando ativam a sua atribuição a uma função personalizada e a outros administradores de aplicações que estão a atribuir funções personalizadas.

> [!NOTE]
> As funções personalizadas da Azure AD não estão integradas com as funções de diretório incorporada durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de papéis terá lugar na experiência de papéis incorporados. Se vir o seguinte banner, estas funções devem ser geridas na experiência de [papéis incorporados](pim-how-to-activate-role.md) e este artigo não se aplica:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Definições de funções abertas

Siga estes passos para abrir as definições para uma função de Anúncio Azure.

1. Inscreva-se na [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal Azure com uma conta de utilizador que é atribuída ao papel de administrador de funções privilegiada.
1. Selecione **funções personalizadas Azure AD (Pré-visualização)**.

    ![Selecione visualização de funções personalizadas da Azure AD para ver atribuições de papéis elegíveis](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Selecione **Definição** para abrir a página **Definições.** Selecione a função para as definições que pretende configurar.
1. Selecione **Editar** para abrir a página de definições de **funções.**

    ![Abra a função personalizada da AD Azure para editar definições](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Definições de funções

Existem várias definições que pode configurar.

### <a name="assignment-duration"></a>Duração da atribuição

Pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (elegíveis ou ativas) quando configurar as definições para uma função. Estas opções tornam-se a duração máxima padrão quando um membro é atribuído ao papel na Gestão de Identidade Privilegiada.

Pode escolher uma destas opções de duração de atribuição *elegíveis.*

- **Permitir a atribuição permanente elegível**: Os administradores podem atribuir a destmissão permanente elegível.
- **Caducar a atribuição elegível após**: Os administradores podem exigir que todas as atribuições elegíveis tenham uma data de início e fim especificada.

Além disso, pode escolher uma destas opções de duração de atribuição *ativa:*

- **Permitir a atribuição ativa permanente**: Os administradores podem atribuir a destmissão permanente.
- **Expirar a atribuição ativa após**: Os administradores podem exigir que todas as atribuições ativas tenham uma data de início e fim especificada.

### <a name="require-azure-multi-factor-authentication"></a>Exigir a Multi-Factor Authentication do Azure

A Privileged Identity Management fornece a aplicação opcional da Autenticação Multi-Factor Azure para dois cenários distintos.

- **Exigir autenticação multi-factor na atribuição ativa**

  Se quiser atribuir apenas um membro a um papel por uma curta duração (um dia, por exemplo), pode ser demasiado lento para exigir que os membros designados solicitem a ativação. Neste cenário, a Gestão de Identidade Privilegiada não pode impor a autenticação de vários fatores quando o utilizador ativa a sua atribuição de funções, porque já estão ativas no papel a partir do momento em que são atribuídos. Para garantir que o administrador que cumpre a atribuição é quem eles dizem ser, selecione a **Autenticação De Vários Fatores exigir na** caixa de atribuição ativa.

- **Exigir autenticação de vários fatores na ativação**

  Pode exigir utilizadores elegíveis atribuídos a uma função de inscrição na Autenticação Multi-Factor Azure antes de poderem ser ativados. Este processo garante que o utilizador que está a solicitar a ativação é quem diz ser com certeza razoável. A aplicação desta opção protege funções críticas em situações em que a conta de utilizador pode ter sido comprometida. Para exigir que um membro elegível execute a autenticação de multi-factores Azure antes da ativação, selecione a **autenticação de vários fatores na caixa de ativação.**

Para mais informações, consulte a [autenticação multifactor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

### <a name="activation-maximum-duration"></a>Duração máxima de ativação

Utilize o slider de **duração máxima** de ativação para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser de 1 e 24 horas.

### <a name="require-justification"></a>Exigir justificação

Pode exigir que os membros introduzam uma justificação na atribuição ativa ou quando ativam. Para exigir justificação, selecione a **justificação exigir na** caixa de verificação de atribuição ativa ou a justificação exigir na caixa de **ativação.**

### <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se quiser obter aprovação para ativar um papel, siga estes passos.

1. Selecione a **aprovação exigir para ativar a** caixa de verificação.
1. Selecione **os aprovadores para** abrir a lista **de membros ou grupos.**

    ![Abra a função personalizada da AD Azure para editar definições](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Selecione pelo menos um membro ou grupo e, em seguida, clique em **Selecionar**. Deve selecionar pelo menos um aprovador. Não há aprovadores por defeito. As suas seleções aparecerão na lista de aprovadores selecionados.
1. Depois de especificar as definições de função, selecione **'Actualizar'** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes

- [Ativar uma função personalizada da AD Azure](azure-ad-custom-roles-activate.md)
- [Atribuir uma função personalizada azure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de funções personalizadas da AD Azure](azure-ad-custom-roles-update-remove.md)
- [Definições de papéis em Azure AD](../users-groups-roles/directory-assign-admin-roles.md)

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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e483c5cdb7e5acc0677b0d0cf2b41c390eef0b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371567"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Configurar funções personalizadas da Azure AD na Gestão de Identidade Privilegiada

Um administrador privilegiado pode alterar as definições de função que se aplicam a um utilizador quando ativam a sua atribuição a uma função personalizada e para outros administradores de aplicações que estão a atribuir funções personalizadas.

> [!NOTE]
> As funções personalizadas AZURE AD não são integradas com as funções de diretório incorporado durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de funções terá lugar na experiência de funções incorporadas. Se vir o seguinte banner, estas funções devem ser geridas [na experiência de funções incorporadas](pim-how-to-activate-role.md) e este artigo não se aplica:
>
> [![Selecione Azure AD > Gestão de Identidade Privilegiada](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Definições de função abertas

Siga estes passos para abrir as definições para um papel AD Azure.

1. Inscreva-se na [Gestão de Identidade Privilegiada](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal Azure com uma conta de utilizador que é atribuída à função de administrador de função Privilegiada.
1. Selecione **funções personalizadas Azure AD (Preview)**.

    ![Selecione Azure AD funções personalizadas pré-visualização para ver atribuições de funções elegíveis](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Selecione **Definição** para abrir a página **Definições.** Selecione a função para as definições que pretende configurar.
1. Selecione **Editar** para abrir a página **de definições de Função.**

    ![Screenshot que mostra a página "Detalhes de definição de função" com a ação "Editar" selecionada.](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Definições de funções

Há várias configurações que pode configurar.

### <a name="assignment-duration"></a>Duração da atribuição

Pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (elegível ou ativo) quando configurar as definições para uma função. Estas opções tornam-se a duração máxima padrão quando um membro é atribuído ao papel na Gestão de Identidade Privilegiada.

Pode escolher uma destas opções de duração de atribuição *elegíveis.*

- **Permitir uma atribuição elegível permanente**: Os administradores podem atribuir adesão elegível permanente.
- **Atribuição elegível expirada após:** Os administradores podem exigir que todas as atribuições elegíveis tenham uma data de início e fim especificada.

Além disso, pode escolher uma destas opções de duração de atribuição *ativa:*

- **Permitir uma atribuição ativa permanente**: Os administradores podem atribuir uma adesão ativa permanente.
- **Expire a atribuição ativa após:** Os administradores podem exigir que todas as atribuições ativas tenham uma data de início e fim especificada.

### <a name="require-azure-multi-factor-authentication"></a>Exigir a Multi-Factor Authentication do Azure

A Gestão de Identidade Privilegiada proporciona a aplicação opcional da Autenticação Multi-Factor Azure para dois cenários distintos.

- **Requerer autenticação multi-factor em atribuição ativa**

  Se quiser apenas atribuir um membro a um papel por uma curta duração (um dia, por exemplo), pode ser demasiado lento para exigir que os membros designados solicitem a ativação. Neste cenário, a Gestão de Identidade Privilegiada não pode impor a autenticação de vários fatores quando o utilizador ativa a sua atribuição de funções, uma vez que já estão ativos no papel a partir do momento em que são atribuídos. Para garantir que o administrador que cumpre a atribuição é quem eles dizem ser, selecione a **Autenticação Multi-Factor Requere na** caixa de atribuição ativa.

- **Requerem autenticação multi-factor na ativação**

  Pode exigir que utilizadores elegíveis sejam designados para uma função para se inscreverem na Autenticação Multi-Factor Azure antes de poderem ser ativados. Este processo garante que o utilizador que está a solicitar a ativação é quem diz ser com certeza razoável. A aplicação desta opção protege funções críticas em situações em que a conta de utilizador possa ter sido comprometida. Para exigir que um membro elegível execute a autenticação multi-factor Azure antes da ativação, selecione a **Autenticação Multi-Factor Requere na caixa de ativação.**

Para mais informações, consulte [a autenticação multi-factor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

### <a name="activation-maximum-duration"></a>Duração máxima da ativação

Utilize o **deslizador de duração máxima de ativação** para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser de 1 e 24 horas.

### <a name="require-justification"></a>Requerem justificação

Pode exigir que os membros introduzam uma justificação sobre a atribuição ativa ou quando são ativados. Para exigir justificação, selecione a **justificação do Requerer na** caixa de verificação de atribuição ativa ou na **justificação do Requerer na caixa de ativação.**

### <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se quiser requerer aprovação para ativar uma função, siga estes passos.

1. Selecione a **aprovação 'Exigir' para ativar a** caixa de verificação.
1. **Selecione Selecione os aprovadores** para abrir a lista **de membros ou grupos.**

    ![Abra o papel personalizado AZure AD para editar definições](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Selecione pelo menos um membro ou grupo e, em seguida, clique em **Select**. Deve selecionar pelo menos um aprovador. Não há aprovadores predefinidos. As suas seleções aparecerão na lista de aprovadores selecionados.
1. Uma vez especificadas as definições de função, selecione **Update** para guardar as suas alterações.

## <a name="next-steps"></a>Passos seguintes

- [Ativar um papel personalizado AZure AD](azure-ad-custom-roles-activate.md)
- [Atribuir um papel personalizado AZure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definições de função em Azure AD](../roles/permissions-reference.md)

---
title: Definir configurações de função de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como definir as configurações de função de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9acc1487fcbf8398b7a556c63f97963b264451
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74182718"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Definir configurações de função de recurso do Azure no Privileged Identity Management

Ao definir as configurações de função de recurso do Azure, você define as configurações padrão que são aplicadas às atribuições de função de recurso do Azure no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Use os procedimentos a seguir para configurar o fluxo de trabalho de aprovação e especificar quem pode aprovar ou negar solicitações.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função de recurso do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário na função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **recursos do Azure**.

1. Selecione o recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

    ![Página de recursos do Azure listando recursos que podem ser gerenciados](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selecione **configurações de função**.

    ![Página de configurações de função listando funções de recurso do Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecione a função cujas configurações você deseja configurar.

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selecione **Editar** para abrir o painel **configurações de função** . A primeira guia permite que você atualize a configuração de ativação de função no Privileged Identity Management.

    ![Página Editar configurações de função com a guia ativação aberta](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selecione a guia **atribuição** ou o botão **próximo: atribuição** na parte inferior da página para abrir a guia Configuração de atribuição. Essas configurações controlam as atribuições de função feitas dentro da interface Privileged Identity Management.

    ![Guia atribuição de função na página Configurações de função](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Use a guia **notificação** ou o botão **Avançar: ativação** na parte inferior da página para acessar a guia Configuração de notificação para essa função. Essas configurações controlam todas as notificações por email relacionadas a essa função.

    > [!NOTE]
    > Esta interface do usuário de notificações está sendo revertida em regiões e assinaturas do Azure. Se você não puder configurar essas notificações granulares, verifique novamente em um ou dois dias.

    ![Guia notificações de função na página Configurações de função](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

1. Selecione o botão **Atualizar** a qualquer momento para atualizar as configurações de função.

## <a name="assignment-duration"></a>Duração da atribuição

Você pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificado e ativo) ao definir as configurações para uma função. Essas opções se tornam a duração máxima padrão quando um usuário é atribuído à função no Privileged Identity Management.

Você pode escolher uma destas opções de duração de atribuição **qualificadas** :

| | |
| --- | --- |
| **Permitir atribuição qualificada permanente** | Os administradores de recursos podem atribuir uma atribuição qualificada permanente. |
| **Expirar atribuição qualificada após** | Os administradores de recursos podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificada. |

E, você pode escolher uma dessas opções de duração de atribuição **ativas** :

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores de recursos podem atribuir uma atribuição ativa permanente. |
| **Expirar atribuição ativa após** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e de término especificada. |

> [!NOTE]
> Todas as atribuições que têm uma data de término especificada podem ser renovadas pelos administradores de recursos. Além disso, os usuários podem iniciar solicitações de autoatendimento para [estender ou renovar atribuições de função](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

Privileged Identity Management fornece imposição opcional da autenticação multifator do Azure para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir autenticação multifator na atribuição ativa

Em alguns casos, talvez você queira atribuir um usuário ou grupo a uma função por uma duração curta (um dia, por exemplo). Nesse caso, os usuários atribuídos não precisam solicitar ativação. Nesse cenário, Privileged Identity Management não pode impor a autenticação multifator quando o usuário usa sua atribuição de função porque eles já estão ativos na função a partir do momento em que são atribuídos.

Para garantir que o administrador de recursos que está atendendo à atribuição seja quem eles dizem que eles são, você pode impor a autenticação multifator na atribuição ativa marcando a caixa de **atribuição exigir autenticação multifator no Active** .

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir autenticação multifator na ativação

Você pode exigir que os usuários qualificados para uma função comprovem quem estão usando a autenticação multifator do Azure antes que possam ser ativados. A autenticação multifator garante que o usuário seja quem dizem que eles estão com certeza razoável. A imposição dessa opção protege os recursos críticos em situações em que a conta de usuário pode ter sido comprometida.

Para exigir a autenticação multifator antes da ativação, marque a caixa de seleção **exigir autenticação multifator no modo de ativação** .

Para obter mais informações, consulte [autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima da ativação

Use o controle deslizante **duração máxima de ativação** para definir o tempo máximo, em horas, que uma função permanece ativa antes de expirar. Esse valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificação

Você pode exigir que os usuários insiram uma justificativa de negócios ao serem ativados. Para exigir justificação, marque a caixa de seleção **exigir justificação ativa** ou a **justificação necessária na ativação** .

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **exigir aprovação para ativar** .

1. Selecione **selecionar aprovadores** para abrir a página **selecionar um membro ou grupo** .

    ![Selecione um painel de usuário ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um usuário ou grupo e clique em **selecionar**. Você pode adicionar qualquer combinação de usuários e grupos. Você deve selecionar pelo menos um Aprovador. Não há aprovadores padrão.

    Suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **Atualizar** para salvar suas alterações.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Configurar alertas de segurança para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-alerts.md)

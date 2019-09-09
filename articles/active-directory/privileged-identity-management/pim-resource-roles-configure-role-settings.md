---
title: Definir configurações de função de recurso do Azure em PIM-Azure Active Directory | Microsoft Docs
description: Saiba como definir as configurações de função de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88645d07ff213b0b7a56f2b081f0aaed01fd2929
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804196"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Definir configurações de função de recurso do Azure no PIM

Ao definir as configurações de função de recurso do Azure, você define as configurações padrão que são aplicadas às atribuições de função de recurso do Azure no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Use os procedimentos a seguir para configurar o fluxo de trabalho de aprovação e especificar quem pode aprovar ou negar solicitações.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função de recurso do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique no recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

    ![Página de recursos do Azure listando recursos que podem ser gerenciados](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Clique em **configurações de função**.

    ![Página de configurações de função listando funções de recurso do Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Clique na função cujas configurações você deseja configurar.

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Clique em **Editar** para abrir o painel configurações de função.

    ![Página Editar configurações de função com opções para atualizar a atribuição e as configurações de ativação](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    No painel de configuração de função para cada função, há várias configurações que você pode configurar.

## <a name="assignment-duration"></a>Duração da atribuição

Você pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificado e ativo) ao definir as configurações para uma função. Essas opções se tornam a duração máxima padrão quando um membro é atribuído à função no PIM.

Você pode escolher uma destas opções de duração de atribuição **qualificadas** :

| | |
| --- | --- |
| **Permitir atribuição qualificada permanente** | Os administradores de recursos podem atribuir a associação qualificada permanente. |
| **Expirar atribuição qualificada após** | Os administradores de recursos podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificada. |

E, você pode escolher uma dessas opções de duração de atribuição **ativas** :

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores de recursos podem atribuir uma associação ativa permanente. |
| **Expirar atribuição ativa após** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e de término especificada. |

> [!NOTE] 
> Todas as atribuições que têm uma data de término especificada podem ser renovadas pelos administradores de recursos. Além disso, os membros podem iniciar as solicitações de autoatendimento para [estender ou renovar atribuições de função](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

O PIM fornece imposição opcional da MFA (autenticação multifator) do Azure para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir autenticação multifator na atribuição ativa

Em alguns casos, talvez você queira atribuir um membro a uma função por uma duração curta (um dia, por exemplo). Nesse caso, eles não precisam dos membros atribuídos para solicitar ativação. Nesse cenário, o PIM não pode impor a MFA quando o membro usa sua atribuição de função, já que elas já estão ativas na função desde o momento em que são atribuídas.

Para garantir que o administrador de recursos que atende a atribuição seja quem dizem que eles são, você pode impor a MFA na atribuição ativa marcando a caixa de **atribuição exigir autenticação multifator no Active** .

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir autenticação multifator na ativação

Você pode exigir que membros qualificados de uma função executem o MFA antes que eles possam ser ativados. Esse processo garante que o usuário que está solicitando a ativação seja quem dizem que eles estão com certeza razoável. A imposição dessa opção protege os recursos críticos em situações em que a conta de usuário pode ter sido comprometida.

Para exigir que um membro qualificado execute o MFA antes da ativação, marque a caixa de seleção **exigir autenticação multifator no modo de ativação** .

Para obter mais informações, consulte [autenticação multifator (MFA) e PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima da ativação

Use o controle deslizante **duração máxima de ativação** para definir o tempo máximo, em horas, que uma função permanece ativa antes de expirar. Esse valor pode ser entre 1 e 24 horas.

## <a name="require-justification"></a>Exigir justificação

Você pode exigir que os membros insiram uma justificativa na atribuição ativa ou quando eles forem ativados. Para exigir justificação, marque a caixa de seleção **exigir justificação ativa** ou a **justificação necessária na ativação** .

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **exigir aprovação para ativar** .

1. Clique em **selecionar aprovadores** para abrir o painel Selecionar um membro ou grupo.

    ![Selecione um painel membro ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um membro ou grupo e clique em **selecionar**. Você pode adicionar qualquer combinação de membros e grupos. Você deve selecionar pelo menos um Aprovador. Não há aprovadores padrão.

    Suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, clique em **Atualizar** para salvar suas alterações.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
- [Configurar alertas de segurança para funções de recurso do Azure no PIM](pim-resource-roles-configure-alerts.md)

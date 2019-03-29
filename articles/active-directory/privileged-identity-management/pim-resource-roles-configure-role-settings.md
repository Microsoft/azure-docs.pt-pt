---
title: Configurar as definições de função de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como configurar as definições de função de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9db39e67cf3ee67b7990f227dbad7e3210cd14b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577157"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Configurar as definições de função de recursos do Azure no PIM

Quando configurar as definições de função de recursos do Azure, define as configurações padrão que são aplicadas a atribuições de funções de recursos do Azure no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Utilize os procedimentos seguintes para configurar o fluxo de trabalho de aprovação e especificar quem pode aprovar ou negar pedidos.

## <a name="open-role-settings"></a>Abra as definições de função

Siga estes passos para abrir as definições para uma função de recursos do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com um utilizador que seja um membro a [administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) função.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique em recursos que pretende gerir, tal como um grupo de gestão ou de subscrição.

    ![Lista de recursos do Azure para gerir](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Clique em **definições de função**.

    ![Definições de função](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Clique na função cujas definições pretende configurar.

    ![Detalhes da definição de função](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Clique em **editar** para abrir o painel de definições de função.

    ![Editar definições de função](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    No painel de definição de função para cada função, existem várias definições que pode configurar.

## <a name="assignment-duration"></a>Duração de atribuição

Pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (elegível e ativa) ao configurar definições para uma função. Estas opções tornam-se a duração máxima do padrão quando um membro é atribuído à função no PIM.

Pode escolher um destes **elegíveis** opções de duração de atribuição:

| | |
| --- | --- |
| **Permitir a atribuição elegível permanente** | Os administradores de recursos podem atribuir associação elegível permanente. |
| **Atribuição elegível, depois de expirar** | Os administradores de recursos podem exigir que todas as atribuições elegíveis têm uma data de início e de fim especificada. |

E, pode escolher um destes **Active Directory** opções de duração de atribuição:

| | |
| --- | --- |
| **Permitir a atribuição ativa permanente** | Os administradores de recursos podem atribuir a subscrição ativa permanente. |
| **Atribuição ativa depois de expirar** | Os administradores de recursos podem exigir que todas as atribuições de Active Directory têm uma data de início e de fim especificada. |

> [!NOTE] 
> Todas as atribuições de que têm uma data de fim especificada podem ser renovadas por administradores de recursos. Além disso, os membros podem iniciar pedidos de Self-serviços de mensagens em fila para [expandir ou renovar as atribuições de funções](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

PIM fornece opcional imposição de multi-factor Authentication (MFA) do Azure para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Requerer autenticação multifator aquando da atribuição ativa

Em alguns casos, pode querer atribuir um membro a uma função durante um breve período (um dia, por exemplo). Neste caso, não precisam de membros atribuídos para pedir a ativação. Neste cenário, PIM não é possível impor a MFA quando o membro utiliza a respetiva atribuição de função, uma vez que eles já estão ativos na função desde o momento que são atribuídas.

Para garantir que o administrador de recursos cumprindo a atribuição é que eles dizem que eles são, pode impor o MFA aquando da atribuição ativa, verificando a **exigir a autenticação Multifator aquando da atribuição ativa** caixa.

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir Multi-Factor Authentication

Pode exigir elegíveis membros de uma função executar a MFA para que poderem ativar. Este processo garante que o utilizador que está a pedir a ativação é realmente quem afirma são com certeza razoável. Impor esta opção protege os recursos críticos em situações, quando a conta de utilizador pode ter sido comprometida.

Para exigir um membro elegível executar a MFA antes da ativação, consulte a **requerer o multi-factor Authentication na ativação** caixa.

Para obter mais informações, consulte [multi-factor authentication (MFA) e o PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima da ativação

Utilize o **duração máxima da ativação** controlo de deslize para definir o tempo máximo, em horas, que uma função fica ativa antes de expirar. Este valor pode ser entre 1 e 24 horas.

## <a name="require-justification"></a>Requerer justificação

Pode exigir que os membros de introduzir uma justificação aquando da atribuição ativa ou quando ativar. Para requerer justificação, verifique os **requerer justificação aquando da atribuição ativa** caixa ou o **requerer justificação aquando da ativação** caixa.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se quiser exigir aprovação para ativar uma função, siga estes passos.

1. Verifique os **exigir aprovação para ativar** caixa de verificação.

1. Clique em **selecionar aprovadores** para abrir um painel de membro ou grupo de Select.

    ![Selecionar um membro ou grupo](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um membro ou grupo e, em seguida, clique em **selecione**. Pode adicionar qualquer combinação de membros e grupos. Tem de selecionar pelo menos um aprovador. Não há nenhum aprovadores de predefinição.

    As suas seleções aparecerá na lista de aprovadores selecionados.

1. Depois de especificar as todas as suas definições de função, clique em **atualização** para guardar as alterações.

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções de recursos do Azure no PIM](pim-resource-roles-assign-roles.md)
- [Configurar alertas de segurança para funções de recursos do Azure no PIM](pim-resource-roles-configure-alerts.md)

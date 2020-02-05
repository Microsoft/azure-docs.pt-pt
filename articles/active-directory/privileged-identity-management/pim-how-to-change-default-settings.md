---
title: Definir as configurações de função do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como definir as configurações de função do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a480327efacee2d1eb74353b2d0ef7885a6194
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024216"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Definir as configurações de função do Azure AD no Privileged Identity Management

Um administrador de função com privilégios pode personalizar Privileged Identity Management (PIM) em sua organização do Azure Active Directory (Azure AD), incluindo a alteração da experiência de um usuário que está ativando uma atribuição de função qualificada.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

    ![Nova versão das funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

Siga as etapas neste artigo para aprovar ou negar solicitações para funções do Azure AD.

# <a name="new-versiontabnew"></a>[Nova versão](#tab/new)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário na função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **configurações de função**.

    ![Página de configurações de função listando funções de recurso do Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecione a função cujas configurações você deseja configurar.

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selecione **Editar** para abrir a página Configurações de função.

    ![Página Editar configurações de função com opções para atualizar a atribuição e as configurações de ativação](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    No painel de configuração de função para cada função, há várias configurações que você pode configurar.

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

# <a name="previous-versiontabprevious"></a>[Versão anterior](#tab/previous)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **definições**.

    ![Funções do Azure AD – configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecione **funções**.

1. Selecione a função cujas configurações você deseja configurar.

    ![Funções do Azure AD – funções de configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página Configurações de cada função, há várias configurações que você pode configurar. Essas configurações afetam apenas os usuários que são atribuições **qualificadas** , não atribuições **permanentes** .

## <a name="activations"></a>Ativações

Use o controle deslizante de **ativações** para definir o tempo máximo, em horas, que uma função permanece ativa antes de expirar. Esse valor pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Use a opção de **notificações** para especificar se os administradores receberão notificações por email quando as funções forem ativadas. Essa notificação pode ser útil para detectar ativações não autorizadas ou ilegítimos.

Quando definido como **habilitar**, as notificações são enviadas para:

- Administrador de função com privilégios
- Administrador de segurança
- Administrador global

Para obter mais informações, consulte [notificações por email em Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Tíquete de solicitação/incidente

Use a opção de **tíquete de incidente/solicitação** para exigir que os administradores qualificados incluam um número de tíquete quando ativarem sua função. Essa prática pode tornar as auditorias de acesso de função mais eficientes.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Use a opção **autenticação multifator** para especificar se deseja exigir que os usuários verifiquem sua identidade com o MFA antes que possam ativar suas funções. Eles só precisam verificar sua identidade uma vez por sessão, nem sempre que ativam uma função. Há duas dicas para ter em mente ao habilitar a MFA:

- Os usuários que têm contas da Microsoft para seus endereços de email (normalmente @outlook.com, mas nem sempre) não podem se registrar para a autenticação multifator do Azure. Se você quiser atribuir funções a usuários com contas da Microsoft, você deve torná-los administradores permanentes ou desabilitar a autenticação multifator para essa função.
- Não é possível desabilitar a autenticação multifator do Azure para funções altamente privilegiadas para o Azure AD e o Office 365. Esse recurso de segurança ajuda a proteger as seguintes funções:  
  
  - Administrador da proteção de informações do Azure
  - Administrador de faturação
  - Administrador de aplicativos de nuvem
  - Administrador de conformidade
  - Administrador de acesso condicional
  - Administrador do Dynamics 365
  - Aprovador de acesso de LockBox do cliente
  - Gravadores de diretório
  - Administrador de intercâmbio
  - Administrador global
  - Administrador insinado
  - Administrador de Power BI
  - Administrador de função com privilégios
  - Administrador de segurança
  - Administrador do SharePoint
  - Skype para administrador de negócios
  - Administrador do usuário

Para obter mais informações, consulte [autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exigir aprovação

Se você quiser delegar a aprovação necessária para ativar uma função, siga estas etapas.

1. Defina a opção **exigir aprovação** como **habilitada**. O painel se expande com opções para selecionar aprovadores.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se você não especificar nenhum aprovador, o administrador da função com privilégios se tornará o aprovador padrão e, em seguida, será necessário para aprovar todas as solicitações de ativação para essa função.

1. Para adicionar Aprovadores, clique em **selecionar aprovadores**.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais Aprovadores, além do administrador de função com privilégios e, em seguida, clique em **selecionar**. Você pode selecionar usuários ou grupos. É recomendável que você adicione pelo menos dois aprovadores. Mesmo que você se adicione como um aprovador, não é possível aprovar automaticamente uma ativação de função. Suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **salvar** para salvar as alterações.

---

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurar alertas de segurança para funções do Azure AD no Privileged Identity Management](pim-how-to-configure-security-alerts.md)

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
ms.date: 02/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a409d71ff3eae3bc62527a0669a74696246a50cd
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048076"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Definir as configurações de função do Azure AD no Privileged Identity Management

Um administrador de função com privilégios pode personalizar Privileged Identity Management (PIM) em sua organização do Azure Active Directory (Azure AD), incluindo a alteração da experiência de um usuário que está ativando uma atribuição de função qualificada.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja no papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Open **Azure AD Privileged Identity Management.** Se tiver um banner no topo da página de visão geral, siga as instruções no separador **versão Nova** deste artigo. Caso contrário, siga as instruções no separador versão **anterior.**

    ![Nova versão das funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

Siga as etapas neste artigo para aprovar ou negar solicitações para funções do Azure AD.

# <a name="new-versiontabnew"></a>[Nova versão](#tab/new)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador no papel de Administrador de [Funções Privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
gt
1. Open **Azure AD Privileged Identity Management** &gt; **Azure AD funções** &gt; Definições de **funções.**

    ![Página de configurações de função listando funções de recurso do Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecione a função cujas configurações você deseja configurar.

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selecione **Editar** para abrir a página de definições de funções.

    ![Página Editar configurações de função com opções para atualizar a atribuição e as configurações de ativação](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    No painel de configuração de função para cada função, há várias configurações que você pode configurar.

## <a name="assignment-duration"></a>Duração da atribuição

Você pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificado e ativo) ao definir as configurações para uma função. Essas opções se tornam a duração máxima padrão quando um usuário é atribuído à função no Privileged Identity Management.

Pode escolher uma destas opções de duração de atribuição **elegíveis:**

| | |
| --- | --- |
| **Permitir atribuição permanente elegível** | Os administradores de recursos podem atribuir uma atribuição qualificada permanente. |
| **Caducar a atribuição elegível após** | Os administradores de recursos podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificada. |

E, pode escolher uma destas opções **ativas** de duração de atribuição:

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores de recursos podem atribuir uma atribuição ativa permanente. |
| **Caducar a atribuição ativa após** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e de término especificada. |

> [!NOTE]
> Todas as atribuições que têm uma data de término especificada podem ser renovadas pelos administradores de recursos. Além disso, os utilizadores podem iniciar pedidos de self-service para estender ou renovar atribuições de [funções.](pim-resource-roles-renew-extend.md)

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

Privileged Identity Management fornece imposição opcional da autenticação multifator do Azure para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir autenticação multifator na atribuição ativa

Em alguns casos, é possível atribuir um utilizador a uma função por uma curta duração (um dia, por exemplo). Nesse caso, os usuários atribuídos não precisam solicitar ativação. Nesse cenário, Privileged Identity Management não pode impor a autenticação multifator quando o usuário usa sua atribuição de função porque eles já estão ativos na função a partir do momento em que são atribuídos.

Para garantir que o administrador de recursos que cumpre a atribuição é quem eles dizem ser, pode impor a autenticação de vários fatores na atribuição ativa, verificando a **Autenticação De Vários Fatores necessários na** caixa de atribuição ativa.

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir autenticação multifator na ativação

Você pode exigir que os usuários qualificados para uma função comprovem quem estão usando a autenticação multifator do Azure antes que possam ser ativados. A autenticação multifator garante que o usuário seja quem dizem que eles estão com certeza razoável. A imposição dessa opção protege os recursos críticos em situações em que a conta de usuário pode ter sido comprometida.

Para exigir a autenticação de vários fatores antes da ativação, verifique a **autenticação de vários fatores na caixa de ativação.**

Para mais informações, consulte a [autenticação multifactor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="activation-maximum-duration"></a>Duração máxima da ativação

Utilize o slider de **duração máxima** de ativação para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Esse valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificação

Você pode exigir que os usuários insiram uma justificativa de negócios ao serem ativados. Para exigir justificação, verifique a **justificação necessária na** caixa de atribuição ativa ou a justificação de exigir na caixa de **ativação.**

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Verifique a **aprovação do Requerio para ativar a** caixa de verificação.

1. Selecione **os aprovadores**.

    ![Selecione um painel de usuário ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um utilizador e, em seguida, clique em **Selecionar**. Você deve selecionar pelo menos um Aprovador. Não há aprovadores padrão.

    Suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de ter especificado todas as definições de funções, selecione **'Actualizar'** para guardar as alterações.

# <a name="previous-versiontabprevious"></a>[Versão anterior](#tab/previous)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Definições**.

    ![Funções do Azure AD – configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecione **Funções**.

1. Selecione a função cujas configurações você deseja configurar.

    ![Funções do Azure AD – funções de configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página Configurações de cada função, há várias configurações que você pode configurar. Estas definições apenas afetam os utilizadores que são atribuições **elegíveis,** e não atribuições **permanentes.**

## <a name="activations"></a>Ativações

Utilize o slider **de Ativações** para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Esse valor pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Utilize o interruptor **Notificações** para especificar se os administradores receberão notificações de e-mail quando as funções forem ativadas. Essa notificação pode ser útil para detectar ativações não autorizadas ou ilegítimos.

Quando definido para **Enable,** as notificações são enviadas para:

- Administrador de função com privilégios
- Administrador de segurança
- Administrador global

Para mais informações, consulte [notificações por e-mail em Gestão de Identidade Privilegiada.](pim-email-notifications.md)

## <a name="incidentrequest-ticket"></a>Tíquete de solicitação/incidente

Utilize o interruptor **de bilhete Incident/Request** para exigir que os administradores elegíveis incluam um número de bilhete quando ativarem a sua função. Essa prática pode tornar as auditorias de acesso de função mais eficientes.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Utilize o interruptor de **autenticação multi-factor** para especificar se exige que os utilizadores verifiquem a sua identidade com MFA antes de poderem ativar as suas funções. Eles só precisam verificar sua identidade uma vez por sessão, nem sempre que ativam uma função. Há duas dicas para ter em mente ao habilitar a MFA:

- Os utilizadores que possuem contas microsoft para os seus endereços de e-mail (normalmente @outlook.com, mas nem sempre) não podem registar-se para autenticação multi-factor do Azure. Se você quiser atribuir funções a usuários com contas da Microsoft, você deve torná-los administradores permanentes ou desabilitar a autenticação multifator para essa função.
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

Para mais informações, consulte a [autenticação multifactor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="require-approval"></a>Exigir aprovação

Se você quiser delegar a aprovação necessária para ativar uma função, siga estas etapas.

1. Desloque o interruptor de **homologação Require** para **Ativado**. O painel se expande com opções para selecionar aprovadores.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se você não especificar nenhum aprovador, o administrador da função com privilégios se tornará o aprovador padrão e, em seguida, será necessário para aprovar todas as solicitações de ativação para essa função.

1. Para adicionar aprovadores, clique em **Selecionar aprovadores**.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais aprovadores para além do administrador de funções privilegiado e, em seguida, clique em **Selecionar**. É recomendável que você adicione pelo menos dois aprovadores. Mesmo que você se adicione como um aprovador, não é possível aprovar automaticamente uma ativação de função. Suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de ter especificado todas as definições de funções, selecione **Guardar** para guardar as suas alterações.

---

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Configure alertas de segurança para funções da AD Azure na Gestão de Identidade Privilegiada](pim-how-to-configure-security-alerts.md)

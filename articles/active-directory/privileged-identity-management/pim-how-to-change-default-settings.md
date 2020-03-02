---
title: Configurar definições de funções de AD Azure em PIM - Azure AD / Microsoft Docs
description: Saiba como configurar as definições de funções da Azure AD na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205029"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configure definições de funções de AD Azure na Gestão de Identidade Privilegiada

Um administrador privilegiado pode personalizar a Privileged Identity Management (PIM) na sua organização Azure Ative Directory (Azure AD), incluindo alterar a experiência para um utilizador que está a ativar uma atribuição de funções elegíveis.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão do PIM

A partir de novembro de 2019, a parte da AD Azure da Privileged Identity Management está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão da Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos neste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja no papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Open **Azure AD Privileged Identity Management.** Se tiver um banner no topo da página de visão geral, siga as instruções no separador **versão Nova** deste artigo. Caso contrário, siga as instruções no separador versão **anterior.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga os passos deste artigo para aprovar ou negar pedidos de funções da Azure AD.

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="open-role-settings"></a>Definições de funções abertas

Siga estes passos para abrir as definições para uma função de Anúncio Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador no papel de Administrador de [Funções Privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
gt
1. Open **Azure AD Privileged Identity Management** &gt; **Azure AD funções** &gt; Definições de **funções.**

    ![Página de definições de funções listando funções de Anúncio Azure](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selecione a função cujas definições pretende configurar.

    ![Definição de definição de funções listando várias definições de atribuição e ativação](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selecione **Editar** para abrir a página de definições de funções.

    ![Editar página de definições de funções com opções para atualizar definições de atribuição e ativação](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    No painel de definição de funções para cada função, existem várias definições que pode configurar.

## <a name="assignment-duration"></a>Duração da atribuição

Pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (elegíveis e ativas) quando configurar as definições para uma função. Estas opções tornam-se a duração máxima padrão quando um utilizador é atribuído ao papel na Gestão de Identidade Privilegiada.

Pode escolher uma destas opções de duração de atribuição **elegíveis:**

| | |
| --- | --- |
| **Permitir atribuição permanente elegível** | Os administradores globais e os administradores privilegiados podem atribuir atribuição permanente elegível. |
| **Caducar a atribuição elegível após** | Os administradores globais e os administradores privilegiados podem exigir que todas as atribuições elegíveis tenham uma data de início e fim especificada. |

E, pode escolher uma destas opções **ativas** de duração de atribuição:

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores globais e os administradores privilegiados podem atribuir uma atribuição ativa permanente. |
| **Caducar a atribuição ativa após** | Os administradores globais e os administradores privilegiados podem exigir que todas as atribuições ativas tenham uma data de início e fim especificada. |

> [!NOTE]
> Todas as atribuições que tenham uma data de fim especificada podem ser renovadas pelos administradores da Global e pelos administradores privilegiados. Além disso, os utilizadores podem iniciar pedidos de self-service para estender ou renovar atribuições de [funções.](pim-resource-roles-renew-extend.md)

## <a name="require-multi-factor-authentication"></a>Exigir autenticação de vários fatores

A Privileged Identity Management fornece a aplicação opcional da Autenticação Multi-Factor Azure para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir autenticação multi-factor na atribuição ativa

Em alguns casos, é possível atribuir um utilizador a uma função por uma curta duração (um dia, por exemplo). Neste caso, os utilizadores designados não precisam de solicitar a ativação. Neste cenário, a Gestão de Identidade Privilegiada não pode impor a autenticação de vários fatores quando o utilizador utiliza a sua atribuição de funções porque já estão ativas no papel a partir do momento em que é atribuída.

Para garantir que o administrador que cumpre a atribuição é quem eles dizem ser, pode impor a autenticação de vários fatores na atribuição ativa, verificando a **Autenticação De Vários Fatores necessários na** caixa de atribuição ativa.

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir autenticação de vários fatores na ativação

Pode exigir que os utilizadores elegíveis para uma função provem quem estão a usar a Autenticação Multi-Factor Azure antes de poderem ativar. A autenticação multifactor garante que o utilizador é quem diz ser com certeza razoável. A aplicação desta opção protege os recursos críticos em situações em que a conta de utilizador pode ter sido comprometida.

Para exigir a autenticação de vários fatores antes da ativação, verifique a **autenticação de vários fatores na** caixa de ativação no separador de atribuição da definição de **funções de Edição**.

Para mais informações, consulte a [autenticação multifactor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="activation-maximum-duration"></a>Duração máxima de ativação

Utilize o slider de **duração máxima** de ativação para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificação

Pode exigir que os utilizadores introduzam uma justificação de negócio quando ativam. Para exigir justificação, verifique a **justificação necessária na** caixa de atribuição ativa ou a justificação de exigir na caixa de **ativação.**

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se a definição de vários aprovadores, a aprovação termina assim que um deles aprovar ou negar. Não é possível que seja aprovada por pelo menos dois utilizadores. Para exigir aprovação para ativar uma função, siga estes passos.

1. Verifique a **aprovação do Requerio para ativar a** caixa de verificação.

1. Selecione **os aprovadores**.

    ![Selecione um painel de utilizador ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um utilizador e, em seguida, clique em **Selecionar**. Deve selecionar pelo menos um aprovador. Não há aprovadores por defeito.

    As suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de ter especificado todas as definições de funções, selecione **'Actualizar'** para guardar as alterações.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="open-role-settings"></a>Definições de funções abertas

Siga estes passos para abrir as definições para uma função de Anúncio Azure.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Definições**.

    ![Funções Azure AD - Definições](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecione **Funções**.

1. Selecione a função cujas definições pretende configurar.

    ![Funções Azure AD - Definições Funções](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página de definições para cada função, existem várias definições que pode configurar. Estas definições apenas afetam os utilizadores que são atribuições **elegíveis,** e não atribuições **permanentes.**

## <a name="activations"></a>Ativações

Utilize o slider **de Ativações** para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Utilize o interruptor **Notificações** para especificar se os administradores receberão notificações de e-mail quando as funções forem ativadas. Esta notificação pode ser útil para detetar ativações não autorizadas ou ilegítimas.

Quando definido para **Enable,** as notificações são enviadas para:

- Administrador privilegiado
- Administrador de segurança
- Administrador global

Para mais informações, consulte [notificações por e-mail em Gestão de Identidade Privilegiada.](pim-email-notifications.md)

## <a name="incidentrequest-ticket"></a>Bilhete incidente/pedido

Utilize o interruptor **de bilhete Incident/Request** para exigir que os administradores elegíveis incluam um número de bilhete quando ativarem a sua função. Esta prática pode tornar as auditorias de acesso a papéis mais eficazes.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Utilize o interruptor de **autenticação multi-factor** para especificar se exige que os utilizadores verifiquem a sua identidade com MFA antes de poderem ativar as suas funções. Só têm de verificar a sua identidade uma vez por sessão, não sempre que ativam um papel. Há duas dicas a ter em mente quando ativa o MFA:

- Os utilizadores que possuem contas microsoft para os seus endereços de e-mail (normalmente @outlook.com, mas nem sempre) não podem registar-se para autenticação multi-factor do Azure. Se pretender atribuir funções aos utilizadores com contas da Microsoft, deverá efetuar administradores permanentes ou desativar a autenticação de vários fatores para essa função.
- Não é possível desativar a Autenticação Multi-Factor Azure para funções altamente privilegiadas para o Azure AD e office 365. Esta função de segurança ajuda a proteger as seguintes funções:  
  
  - Administrador de Proteção de Informação Azure
  - Administrador de faturação
  - Administrador de aplicação em nuvem
  - Administrador de conformidade
  - Administrador de acesso condicional
  - Administrador da Dinâmica 365
  - Aprovador de acesso lockBox do cliente
  - Escritores de diretórios
  - Administrador de intercâmbio
  - Administrador global
  - Administrador insinado
  - Administrador power BI
  - Administrador privilegiado
  - Administrador de segurança
  - Administrador do SharePoint
  - Skype para administrador de negócios
  - Administrador de utilizadores

Para mais informações, consulte a [autenticação multifactor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="require-approval"></a>Exigir aprovação

Se quiser delegar a aprovação necessária para ativar uma função, siga estes passos.

1. Desloque o interruptor de **homologação Require** para **Ativado**. O painel expande-se com opções para selecionar aprovadores.

    ![Funções Azure AD - Definições - Requerem aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se não especificar nenhum aprovador, o administrador de funções privilegiado torna-se o aprovador de predefinição e é então obrigado a aprovar todos os pedidos de ativação para esta função.

1. Para adicionar aprovadores, clique em **Selecionar aprovadores**.

    ![Funções Azure AD - Definições - Requerem aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais aprovadores para além do administrador de funções privilegiado e, em seguida, clique em **Selecionar**. Recomendamos que adicione pelo menos dois aprovadores. Mesmo que se adicione como um aprovador, não pode auto-aprovar uma ativação de papel. As suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de ter especificado todas as definições de funções, selecione **Guardar** para guardar as suas alterações.

---

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Configure alertas de segurança para funções da AD Azure na Gestão de Identidade Privilegiada](pim-how-to-configure-security-alerts.md)

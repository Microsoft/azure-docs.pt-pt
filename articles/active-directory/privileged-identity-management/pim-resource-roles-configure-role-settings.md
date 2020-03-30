---
title: Configure definições de função de recursos Azure em PIM - Azure AD [ Microsoft Docs
description: Saiba como configurar as definições de funções de recursos azure na Azure AD Privileged Identity Management (PIM).
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
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638685"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Configure definições de funções de recursos Azure na Gestão de Identidade Privilegiada

Quando configura as definições de funções de recurso Azure, define as definições predefinidas que são aplicadas às atribuições de funções de recursos Azure em Azure Ative Directory (Azure AD) Privileged Identity Management (PIM). Utilize os seguintes procedimentos para configurar o fluxo de trabalho de aprovação e especificar quem pode aprovar ou negar pedidos.

## <a name="open-role-settings"></a>Definições de funções abertas

Siga estes passos para abrir as definições para uma função de recurso Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador no papel de Administrador de [Funções Privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **recursos Azure**.

1. Selecione o recurso que pretende gerir, como uma subscrição ou grupo de gestão.

    ![Página de recursos azure listando recursos que podem ser geridos](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selecione **as definições**de função .

    ![Página de definições de funções listando funções de recurso Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecione a função cujas definições pretende configurar.

    ![Definição de definição de funções listando várias definições de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **Selecione Editar** para abrir o painel de definições de **funções.** O primeiro separador permite-lhe atualizar a configuração para ativação de funções na Gestão de Identidade Privilegiada.

    ![Editar página de definições de funções com separador de ativação aberto](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selecione o separador **Atribuição** ou o **Seguinte: Botão de atribuição** na parte inferior da página para abrir o separador de definição de atribuição. Estas definições controlam as atribuições de funções efetuadas dentro da interface de Gestão de Identidade Privilegiada.

    ![Separador de atribuição de funções na página de definições de funções](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Utilize o separador **Notificação** ou o **Seguinte: Botão** de ativação na parte inferior da página para chegar ao separador de definição de notificação para esta função. Estas definições controlam todas as notificações de e-mail relacionadas com esta função.

    ![Separador de notificações de função na página de definições de funções](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    No separador **Notificações** na página de definições de funções, a Privileged Identity Management permite o controlo granular sobre quem recebe notificações e quais as notificações que recebem.

    - **Desligando um e-mail**<br>Pode desativar e-mails específicos, limpando a caixa de verificação do destinatário predefinido e eliminando quaisquer destinatários adicionais.  

    - **Limite e-mails a endereços de e-mail especificados**<br>Pode desativar os e-mails enviados para destinatários predefinidos, limpando a caixa de verificação predefinida do destinatário. Em seguida, pode adicionar endereços de e-mail adicionais como destinatários adicionais. Se quiser adicionar mais do que um endereço de e-mail, separe-os utilizando um ponto e vírgula (;).

    - **Envie e-mails tanto para destinatários por defeito como para destinatários adicionais**<br>Pode enviar e-mails para o destinatário predefinido e para o destinatário adicional, selecionando a caixa de verificação do destinatário predefinido e adicionando endereços de e-mail para destinatários adicionais.

    - **E-mails críticos apenas**<br>Para cada tipo de e-mail, pode selecionar a caixa de verificação para receber apenas e-mails críticos. O que isto significa é que a Gestão de Identidade Privilegiada continuará a enviar e-mails aos destinatários configurados apenas quando o e-mail necessitar de uma ação imediata. Por exemplo, os e-mails que pedem aos utilizadores que alargem a sua atribuição de funções não serão desencadeados enquanto serão desencadeados e-mails que exijam que os administradores aprovem um pedido de extensão.

1. Selecione o botão **'Actualizar'** a qualquer momento para atualizar as definições de funções.

## <a name="assignment-duration"></a>Duração da atribuição

Pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (elegíveis e ativas) quando configurar as definições para uma função. Estas opções tornam-se a duração máxima padrão quando um utilizador é atribuído ao papel na Gestão de Identidade Privilegiada.

Pode escolher uma destas opções de duração de atribuição **elegíveis:**

| | |
| --- | --- |
| **Permitir atribuição permanente elegível** | Os administradores de recursos podem atribuir uma atribuição permanente elegível. |
| **Caducar a atribuição elegível após** | Os administradores de recursos podem exigir que todas as atribuições elegíveis tenham uma data de início e fim especificada. |

E, pode escolher uma destas opções **ativas** de duração de atribuição:

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores de recursos podem atribuir uma atribuição ativa permanente. |
| **Caducar a atribuição ativa após** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e fim especificada. |

> [!NOTE]
> Todas as atribuições que tenham uma data final especificada podem ser renovadas pelos administradores de recursos. Além disso, os utilizadores podem iniciar pedidos de self-service para estender ou renovar atribuições de [funções.](pim-resource-roles-renew-extend.md)

## <a name="require-multi-factor-authentication"></a>Exigir autenticação de vários fatores

A Privileged Identity Management fornece a aplicação opcional da Autenticação Multi-Factor Azure para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir autenticação multi-factor na atribuição ativa

Em alguns casos, é possível atribuir um utilizador ou grupo a uma função por uma curta duração (um dia, por exemplo). Neste caso, os utilizadores designados não precisam de solicitar a ativação. Neste cenário, a Gestão de Identidade Privilegiada não pode impor a autenticação de vários fatores quando o utilizador utiliza a sua atribuição de funções porque já estão ativas no papel a partir do momento em que é atribuída.

Para garantir que o administrador de recursos que cumpre a atribuição é quem eles dizem ser, pode impor a autenticação de vários fatores na atribuição ativa, verificando a **Autenticação De Vários Fatores necessários na** caixa de atribuição ativa.

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir autenticação de vários fatores na ativação

Pode exigir que os utilizadores elegíveis para uma função provem quem estão a usar a Autenticação Multi-Factor Azure antes de poderem ativar. A autenticação multifactor garante que o utilizador é quem diz ser com certeza razoável. A aplicação desta opção protege os recursos críticos em situações em que a conta de utilizador pode ter sido comprometida.

Para exigir a autenticação de vários fatores antes da ativação, verifique a **autenticação de vários fatores na caixa de ativação.**

Para mais informações, consulte a [autenticação multifactor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="activation-maximum-duration"></a>Duração máxima de ativação

Utilize o slider de **duração máxima** de ativação para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificação

Pode exigir que os utilizadores introduzam uma justificação de negócio quando ativam. Para exigir justificação, verifique a **justificação necessária na** caixa de atribuição ativa ou a justificação de exigir na caixa de **ativação.**

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se quiser obter aprovação para ativar um papel, siga estes passos.

1. Verifique a **aprovação do Requerio para ativar a** caixa de verificação.

1. Selecione **os aprovadores para** abrir a página **Select um membro ou grupo.**

    ![Selecione um painel de utilizador ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um utilizador ou grupo e, em seguida, clique em **Selecionar**. Pode adicionar qualquer combinação de utilizadores e grupos. Deve selecionar pelo menos um aprovador. Não há aprovadores por defeito.

    As suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de ter especificado todas as definições de funções, selecione **'Actualizar'** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
- [Configure alertas de segurança para funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-alerts.md)

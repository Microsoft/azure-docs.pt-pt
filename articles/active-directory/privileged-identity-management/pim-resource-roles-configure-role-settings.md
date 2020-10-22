---
title: Configurar definições de função de recursos Azure em PIM - Azure AD / Microsoft Docs
description: Saiba como configurar as definições de funções de recursos Azure na Gestão de Identidade Privilegiada AZure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8458fc11f5e836e290c593d3ad4983f44e6abf6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370377"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada

Ao configurar as definições de funções de recursos Azure, define as definições padrão que são aplicadas às atribuições de funções de recursos Azure em Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM). Utilize os seguintes procedimentos para configurar o fluxo de trabalho de aprovação e especificar quem pode aprovar ou negar pedidos.

## <a name="open-role-settings"></a>Definições de função abertas

Siga estes passos para abrir as definições para um papel de recurso Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador na função [de Administrador de Função Privilegiada.](../roles/permissions-reference.md#privileged-role-administrator)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Selecione o recurso que pretende gerir, como um grupo de subscrição ou gestão.

    ![Página de recursos Azure que lista recursos que podem ser geridos](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selecione **definições de função**.

    ![Definições de função página listando funções de recursos Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecione a função cujas definições pretende configurar.

    ![Página de detalhes de definição de função listando várias definições de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **Selecione Editar** para abrir o painel de definições de **função.** O primeiro separador permite-lhe atualizar a configuração para ativação de funções na Gestão de Identidade Privilegiada.

    ![Editar página de definições de função com separador de ativação aberto](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selecione o **separador Atribuição** ou o **seguinte: Botão de atribuição** na parte inferior da página para abrir o separador de definição de atribuição. Estas definições controlam atribuições de funções escontes dentro da interface de Gestão de Identidade Privilegiada.

    ![Separador de atribuição de funções na página de definições de funções](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Utilize o **separador De Notificação** ou o **seguinte: Botão** de ativação na parte inferior da página para chegar ao separador de definição de notificação para esta função. Estas definições controlam todas as notificações de e-mail relacionadas com esta função.

    ![Separador de notificações de funções na página de definições de funções](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    No **separador Notificações** na página de definições de funções, a Gestão de Identidade Privilegiada permite o controlo granular sobre quem recebe notificações e quais as notificações que recebem.

    - **Desligar um e-mail**<br>Pode desativar e-mails específicos limpando a caixa de verificação do destinatário predefinido e eliminando quaisquer destinatários adicionais.  

    - **Limite os e-mails a endereços de e-mail especificados**<br>Pode desativar os e-mails enviados para destinatários predefinidos, limpando a caixa de verificação do destinatário predefinido. Em seguida, pode adicionar endereços de e-mail adicionais como destinatários adicionais. Se quiser adicionar mais de um endereço de e-mail, separe-o com um ponto e vírgula (;).

    - **Enviar e-mails para destinatários predefinidos e destinatários adicionais**<br>Pode enviar e-mails para o destinatário predefinido e para o destinatário adicional selecionando a caixa de verificação do destinatário predefinido e adicionando endereços de e-mail para destinatários adicionais.

    - **Apenas e-mails críticos**<br>Para cada tipo de e-mail, pode selecionar a caixa de verificação apenas para receber e-mails críticos. O que isto significa é que a Gestão de Identidade Privilegiada continuará a enviar e-mails aos destinatários configurados apenas quando o e-mail exigir uma ação imediata. Por exemplo, os e-mails a pedir aos utilizadores para prolongarem a sua atribuição de funções não serão desencadeados enquanto um e-mail que exija que os administradores aprovem um pedido de extensão seja desencadeado.

1. Selecione o botão **'Actualização'** a qualquer momento para atualizar as definições de função.

## <a name="assignment-duration"></a>Duração da atribuição

Pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (elegível e ativo) quando configurar as definições para uma função. Estas opções tornam-se a duração máxima padrão quando um utilizador é atribuído ao papel na Gestão de Identidade Privilegiada.

Pode escolher uma destas opções de duração da atribuição **elegíveis:**

| | |
| --- | --- |
| **Permitir a atribuição elegível permanente** | Os administradores de recursos podem atribuir uma atribuição elegível permanente. |
| **Expirar atribuição elegível após** | Os administradores de recursos podem exigir que todas as atribuições elegíveis tenham uma data de início e fim especificada. |

E, pode escolher uma destas opções de duração de atribuição **ativa:**

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores de recursos podem atribuir uma atribuição ativa permanente. |
| **Expire a atribuição ativa depois** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e fim especificada. |

> [!NOTE]
> Todas as atribuições que tenham uma data de fim especificada podem ser renovadas por administradores de recursos. Além disso, os utilizadores podem iniciar pedidos de self-service para [alargar ou renovar atribuições de funções](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Requerem autenticação de vários fatores

A Gestão de Identidade Privilegiada proporciona a aplicação opcional da Autenticação Multi-Factor Azure para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Requerer autenticação multi-factor em atribuição ativa

Em alguns casos, é melhor atribuir um utilizador ou grupo a uma função por uma curta duração (um dia, por exemplo). Neste caso, os utilizadores designados não precisam de solicitar a ativação. Neste cenário, a Gestão de Identidade Privilegiada não pode impor a autenticação de vários fatores quando o utilizador utiliza a sua atribuição de funções porque já estão ativos no papel a partir do momento em que é atribuído.

Para garantir que o administrador de recursos que cumpre a atribuição é quem eles dizem ser, você pode impor a autenticação de vários fatores na atribuição ativa, verificando a **Autenticação Multi-Factor Requere na** caixa de atribuição ativa.

### <a name="require-multi-factor-authentication-on-activation"></a>Requerem autenticação multi-factor na ativação

Pode exigir que os utilizadores elegíveis para uma função provem quem estão a usar a Autenticação Multi-Factor Azure antes de poderem ser ativados. A autenticação multi-factor garante que o utilizador é quem diz ser com certeza razoável. A aplicação desta opção protege recursos críticos em situações em que a conta de utilizador possa ter sido comprometida.

Para exigir a autenticação de vários fatores antes da ativação, verifique a **autenticação multi-factor requere na caixa de ativação.**

Para mais informações, consulte [a autenticação multi-factor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="activation-maximum-duration"></a>Duração máxima da ativação

Utilize o **deslizador de duração máxima de ativação** para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Requerem justificação

Pode exigir que os utilizadores introduzam uma justificação de negócio quando ativam. Para exigir justificação, verifique a **justificação do Requerer na** caixa de atribuição ativa ou na **justificação do Requerer na caixa de ativação.**

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se quiser requerer aprovação para ativar uma função, siga estes passos.

1. Verifique a **aprovação do Requerer para ativar a** caixa de verificação.

1. **Selecione Selecione os aprovadores** para abrir a página **De seleção de um membro ou grupo.**

    ![Selecione um painel de utilizador ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um utilizador ou grupo e, em seguida, clique em **Select**. Pode adicionar qualquer combinação de utilizadores e grupos. Deve selecionar pelo menos um aprovador. Não há aprovadores predefinidos.

    As suas seleções aparecerão na lista de aprovadores selecionados.

1. Uma vez especificado todas as suas definições de função, selecione **Update** para guardar as suas alterações.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
- [Configure alertas de segurança para funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-alerts.md)

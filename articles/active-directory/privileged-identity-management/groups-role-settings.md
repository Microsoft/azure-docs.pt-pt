---
title: Configurar configurações de grupos de acesso privilegiados em PIM - Azure Ative Directory / Microsoft Docs
description: Saiba como configurar as configurações de grupos atribuíveis por funções na Gestão de Identidade Privilegiada AD Azure (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb7f93437cd45914d3824e9f557241ba0d71162
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835395"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Configurar configurações privilegiadas do grupo de acesso (pré-visualização) na Gestão de Identidade Privilegiada

As definições de função são as definições predefinidos que são aplicadas às atribuições de acesso privilegiada do proprietário do grupo e membro do grupo na Gestão de Identidade Privilegiada (PIM). Utilize as seguintes etapas para configurar o fluxo de trabalho de aprovação para especificar quem pode aprovar ou negar pedidos para elevar o privilégio.

## <a name="open-role-settings"></a>Definições de função abertas

Siga estes passos para abrir as definições para uma função de grupo de acesso privilegiado Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador na função [de Administrador de Função Privilegiada.](../roles/permissions-reference.md#privileged-role-administrator)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **acesso privilegiado (Pré-visualização)**.

1. Selecione o grupo que pretende gerir.

    ![Grupos de acesso privilegiados filtrados por um nome de grupo](./media/groups-role-settings/group-select.png)

1. Selecione **Definições**.

    ![Definições de definições de grupo de listagem de páginas para o grupo selecionado](./media/groups-role-settings/group-settings-select-role.png)

1. Selecione a função Proprietário ou Membro cujas definições pretende visualizar ou alterar. Pode ver as definições atuais para a função na página de detalhes da **definição de função.**

    ![Página de detalhes de definição de função listando várias definições de atribuição e ativação](./media/groups-role-settings/group-role-setting-details.png)

1. Selecione **Editar** para abrir a página **de definição de função Editar.** O **separador ativação** permite alterar as definições de ativação de funções, incluindo se permite atribuições permanentes elegíveis e ativas.

    ![Editar página de definições de função com separador de ativação aberto](./media/groups-role-settings/role-settings-activation-tab.png)

1. Selecione o separador **Atribuição** para abrir o separador definições de atribuição. Estas definições controlam as definições de atribuição de Gestão de Identidade Privilegiada para este papel.

    ![Separador de atribuição de funções na página de definições de funções](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Utilize o **separador De Notificação** ou o **seguinte: Botão** de ativação na parte inferior da página para chegar ao separador de definição de notificação para esta função. Estas definições controlam todas as notificações de e-mail relacionadas com esta função.

    ![Separador de notificações de funções na página de definições de funções](./media/groups-role-settings/role-settings-notification-tab.png)

1. Selecione o botão **'Actualização'** a qualquer momento para atualizar as definições de função.

No **separador Notificações** na página de definições de funções, a Gestão de Identidade Privilegiada permite o controlo granular sobre quem recebe notificações e quais as notificações que recebem.

- **Desligar um e-mail**<br>Pode desativar e-mails específicos limpando a caixa de verificação do destinatário predefinido e eliminando quaisquer destinatários adicionais.  
- **Limite os e-mails a endereços de e-mail especificados**<br>Pode desativar os e-mails enviados para destinatários predefinidos, limpando a caixa de verificação do destinatário predefinido. Em seguida, pode adicionar endereços de e-mail adicionais como destinatários adicionais. Se quiser adicionar mais de um endereço de e-mail, separe-o com um ponto e vírgula (;).
- **Enviar e-mails para destinatários predefinidos e destinatários adicionais**<br>Pode enviar e-mails para o destinatário predefinido e para o destinatário adicional selecionando a caixa de verificação do destinatário predefinido e adicionando endereços de e-mail para destinatários adicionais.
- **Apenas e-mails críticos**<br>Para cada tipo de e-mail, pode selecionar a caixa de verificação apenas para receber e-mails críticos. O que isto significa é que a Gestão de Identidade Privilegiada continuará a enviar e-mails aos destinatários configurados apenas quando o e-mail exigir uma ação imediata. Por exemplo, os e-mails a pedir aos utilizadores para prolongarem a sua atribuição de funções não serão desencadeados enquanto um e-mail que exija que os administradores aprovem um pedido de extensão seja desencadeado.

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

A Gestão de Identidade Privilegiada proporciona a aplicação opcional da Autenticação Multi-Factor Azure AD para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Requerer autenticação multi-factor em atribuição ativa

Em alguns casos, é melhor atribuir um utilizador ou grupo a uma função por uma curta duração (um dia, por exemplo). Neste caso, os utilizadores designados não precisam de solicitar a ativação. Neste cenário, a Gestão de Identidade Privilegiada não pode impor a autenticação de vários fatores quando o utilizador utiliza a sua atribuição de funções porque já estão ativos no papel a partir do momento em que é atribuído.

Para garantir que o administrador de recursos que cumpre a atribuição é quem eles dizem ser, você pode impor a autenticação de vários fatores na atribuição ativa, verificando a **Autenticação Multi-Factor Requere na** caixa de atribuição ativa.

### <a name="require-multi-factor-authentication-on-activation"></a>Requerem autenticação multi-factor na ativação

Pode exigir aos utilizadores elegíveis para uma função que provem quem estão a utilizar a autenticação multi-factor Ad Azure antes de poderem ser ativados. A autenticação multi-factor garante que o utilizador é quem diz ser com certeza razoável. A aplicação desta opção protege recursos críticos em situações em que a conta de utilizador possa ter sido comprometida.

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

    ![Selecione um painel de utilizador ou grupo para selecionar aprovadores](./media/groups-role-settings/group-settings-select-approvers.png)

1. Selecione pelo menos um utilizador ou grupo e, em seguida, clique em **Select**. Pode adicionar qualquer combinação de utilizadores e grupos. Deve selecionar pelo menos um aprovador. Não há aprovadores predefinidos.

    As suas seleções aparecerão na lista de aprovadores selecionados.

1. Uma vez especificado todas as suas definições de função, selecione **Update** para guardar as suas alterações.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir adesão ou propriedade de grupo de acesso privilegiado na PIM](groups-assign-member-owner.md)

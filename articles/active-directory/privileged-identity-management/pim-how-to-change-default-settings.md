---
title: Configurar configurações de funções AD Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como configurar as definições de funções Ad Azure em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 676fdf264cb930069425a330c3da9cd753bf8e0a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567038"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configurar definições de papel de AD Azure na Gestão de Identidade Privilegiada

Um administrador privilegiado pode personalizar a Gestão de Identidade Privilegiada (PIM) na sua organização Azure Ative Directory (Azure AD), incluindo alterar a experiência para um utilizador que está a ativar uma atribuição de funções elegíveis.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão de PIM

A partir de novembro de 2019, a parte de funções da AZure AD da Gestão de Identidade Privilegiada está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão de Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos deste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja na [função de administrador](../roles/permissions-reference.md#privileged-role-administrator) privilegiado.
1. Open **Azure AD Gestão de Identidade Privilegiada.** Se tiver um banner no topo da página geral, siga as instruções no separador **Nova versão** deste artigo. Caso contrário, siga as instruções no separador **versão anterior.**

  [![Selecione Azure AD > Gestão de Identidade Privilegiada.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga os passos deste artigo para aprovar ou negar pedidos de funções Azure AD.

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="open-role-settings"></a>Definições de função abertas

Siga estes passos para abrir as definições para um papel AD Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador na função [de Administrador de Função Privilegiada.](../roles/permissions-reference.md#privileged-role-administrator)

1. Open **Azure AD Gestão de Identidade Privilegiada** &gt; **Azure Ad** &gt; **funções Funções de funções**.

    ![Definições de função página listando funções AD Azure](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selecione a função cujas definições pretende configurar.

    ![Página de detalhes de definição de função listando várias definições de atribuição e ativação](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selecione **Editar** para abrir a página de definições de Função.

    ![Editar página de definições de função com opções para atualizar definições de atribuição e ativação](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    No painel de definição de funções para cada função, existem várias configurações que pode configurar.

## <a name="assignment-duration"></a>Duração da atribuição

Pode escolher entre duas opções de duração de atribuição para cada tipo de atribuição (elegível e ativo) quando configurar as definições para uma função. Estas opções tornam-se a duração máxima padrão quando um utilizador é atribuído ao papel na Gestão de Identidade Privilegiada.

Pode escolher uma destas opções de duração da atribuição **elegíveis:**

| | Description |
| --- | --- |
| **Permitir a atribuição elegível permanente** | Os administradores globais e os administradores privilegiados podem atribuir uma atribuição elegível permanente. |
| **Expirar atribuição elegível após** | Os administradores globais e os administradores privilegiados podem exigir que todas as atribuições elegíveis tenham uma data de início e fim especificada. |

E, pode escolher uma destas opções de duração de atribuição **ativa:**

| | Description |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores globais e os administradores privilegiados podem atribuir uma atribuição ativa permanente. |
| **Expire a atribuição ativa depois** | Os administradores globais e os administradores de funções privilegiadas podem exigir que todas as atribuições ativas tenham uma data de início e fim especificada. |

> [!NOTE]
> Todas as atribuições que tenham uma data de fim especificada podem ser renovadas pelos administradores globais e administradores de funções privilegiadas. Além disso, os utilizadores podem iniciar pedidos de self-service para [alargar ou renovar atribuições de funções](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Requerem autenticação de vários fatores

A Gestão de Identidade Privilegiada proporciona a aplicação opcional da Autenticação Multi-Factor Azure AD para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Requerer autenticação multi-factor em atribuição ativa

Em alguns casos, é melhor atribuir um utilizador a uma função por uma curta duração (um dia, por exemplo). Neste caso, os utilizadores designados não precisam de solicitar a ativação. Neste cenário, a Gestão de Identidade Privilegiada não pode impor a autenticação de vários fatores quando o utilizador utiliza a sua atribuição de funções porque já estão ativos no papel a partir do momento em que é atribuído.

Para garantir que o administrador que cumpre a atribuição é quem eles dizem ser, você pode impor a autenticação de vários fatores na atribuição ativa, verificando a **Autenticação Multi-Factor Requere na** caixa de atribuição ativa.

### <a name="require-multi-factor-authentication-on-activation"></a>Requerem autenticação multi-factor na ativação

Pode exigir aos utilizadores elegíveis para uma função que provem quem estão a utilizar a autenticação multi-factor Ad Azure antes de poderem ser ativados. A autenticação multi-factor garante que o utilizador é quem diz ser com certeza razoável. A aplicação desta opção protege recursos críticos em situações em que a conta de utilizador possa ter sido comprometida.

Para exigir a autenticação de vários fatores antes da ativação, verifique a **autenticação multi-factor requere na** caixa de ativação no separador atribuição da definição de **função de edição**.

Para mais informações, consulte [a autenticação multi-factor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="activation-maximum-duration"></a>Duração máxima da ativação

Utilize o **deslizador de duração máxima de ativação** para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Requerem justificação

Pode exigir que os utilizadores introduzam uma justificação de negócio quando ativam. Para exigir justificação, verifique a **justificação do Requerer na** caixa de atribuição ativa ou na **justificação do Requerer na caixa de ativação.**

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se definir vários aprovadores, a aprovação termina assim que um deles aprovar ou negar. Não pode requerer a aprovação de pelo menos dois utilizadores. Para exigir aprovação para ativar uma função, siga estes passos.

1. Verifique a **aprovação do Requerer para ativar a** caixa de verificação.

1. **Selecione Selecione aprovadores**.

    ![Selecione um painel de utilizador ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um utilizador e, em seguida, clique **em Select**. Deve selecionar pelo menos um aprovador. Não há aprovadores predefinidos.

    As suas seleções aparecerão na lista de aprovadores selecionados.

1. Uma vez especificado todas as suas definições de função, selecione **Update** para guardar as suas alterações.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Abra as definições de funções (versão anterior)

Siga estes passos para abrir as definições para um papel AD Azure.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **Definições**.

    ![Funções AD AZure - Definições](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecione **Funções**.

1. Selecione a função cujas definições pretende configurar.

    ![Funções AD AZure - Definições de Funções](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página de definições para cada função, existem várias configurações que pode configurar. Estas definições apenas afetam utilizadores que são atribuições **elegíveis,** e não atribuições **permanentes.**

## <a name="activations"></a>Ativações

Utilize o slider **ativação** para definir o tempo máximo, em horas, para que uma função permaneça ativa antes de expirar. Este valor pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Utilize o comutador **de Notificações** para especificar se os administradores receberão notificações por e-mail quando as funções são ativadas. Esta notificação pode ser útil para detetar ativações não autorizadas ou ilegítimas.

Quando definido para **Ativar,** as notificações são enviadas para:

- Administrador privilegiado
- Administrador de segurança
- Administrador global

Para mais informações, consulte [notificações por email na Gestão de Identidade Privilegiada.](pim-email-notifications.md)

## <a name="incidentrequest-ticket"></a>Bilhete de incidente/pedido

Utilize o comutador **de bilhetes Incident/Request** para exigir que os administradores elegíveis incluam um número de bilhete quando ativarem a sua função. Esta prática pode tornar as auditorias de acesso a funções mais eficazes.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Utilize o **comutador multi-factor authentication** para especificar se deve exigir que os utilizadores verifiquem a sua identidade com MFA antes de poderem ativar as suas funções. Só têm de verificar a sua identidade uma vez por sessão, não sempre que ativam um papel. Há duas dicas a ter em mente quando ativar o MFA:

- Os utilizadores que tenham contas da Microsoft para os seus endereços de e-mail (normalmente @outlook.com , mas nem sempre) não podem registar-se para autenticação multi-factor Azure AD. Se pretender atribuir funções a utilizadores com contas Microsoft, deve torná-las administradores permanentes ou desativar a autenticação multi-factor para essa função.
- Não é possível desativar a autenticação multi-factor Azure AD para funções altamente privilegiadas para Azure AD e Microsoft 365. Esta função de segurança ajuda a proteger as seguintes funções:  
  
  - Administrador de Proteção de Informação da Azure
  - Administrador de faturação
  - Administrador de aplicação em nuvem
  - Administrador de conformidade
  - Administrador de acesso condicional
  - Administrador dinâmico 365
  - Aprovação de acesso lockBox ao cliente
  - Escritores de diretórios
  - Administrador do Exchange
  - Administrador global
  - Administrador do Intune
  - Administrador de Bi de potência
  - Administrador privilegiado
  - Administrador de segurança
  - Administrador do SharePoint
  - Administrador do Skype para Empresas
  - Administrador de utilizadores

Para mais informações, consulte [a autenticação multi-factor e a Gestão de Identidade Privilegiada.](pim-how-to-require-mfa.md)

## <a name="require-approval"></a>Requerer aprovação

Se quiser delegar a aprovação necessária para ativar uma função, siga estes passos.

1. Desa **estação de homologação Require** para **Ativação**. O painel expande-se com opções para selecionar aprovadores.

    ![Screenshot que mostra o interruptor "Exigir aprovação" com "Enable" selecionado.](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se não especificar quaisquer aprovadores, o administrador de função Privilegeed torna-se o aprovador por defeito e é então obrigado a aprovar todos os pedidos de ativação para esta função.

1. Para adicionar aprovadores, clique **em Selecionar aprovadores**.

    ![Funções AD AZure - Definições - Requer aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais aprovadores para além do administrador de função privilegiado e, em seguida, clique em **Selecionar**. Recomendamos que adicione pelo menos dois aprovadores. Mesmo que se adida como um aprovador, não pode aprovar uma ativação de papéis. As suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de ter especificado todas as definições de funções, **selecione Guardar** para guardar as suas alterações.

---

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Configure alertas de segurança para funções de Ad Azure em Gestão de Identidade Privilegiada](pim-how-to-configure-security-alerts.md)

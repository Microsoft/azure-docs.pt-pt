---
title: Ativar as minhas funções de AD AZure em PIM - Azure Ative Directory / Microsoft Docs
description: Saiba como ativar as funções de Azure AD em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38992c15c23216aa81cda566a333d8e45f90b17e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369850"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Ativar as minhas funções do Microsoft Azure AD no PIM

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) simplifica a forma como as empresas gerem o acesso privilegiado a recursos em Azure AD e outros serviços online da Microsoft, como o Microsoft 365 ou o Microsoft Intune.  

Se foi elegível para um cargo administrativo, então deve ativar a atribuição de funções quando precisar de realizar ações privilegiadas. Por exemplo, se ocasionalmente gere as funcionalidades do Microsoft 365, os administradores privilegiados da sua organização podem não fazer de si um Administrador Global permanente, uma vez que essa função também tem impacto noutros serviços. Em vez disso, tornam-no elegível para funções de Azure AD, como o Exchange Online Administrator. Pode solicitar a ativação dessa função quando precisar dos seus privilégios, e depois terá o controlo do administrador durante um período de tempo pré-determinado.

Este artigo destina-se a administradores que necessitem de ativar o seu papel AZURE AD na Gestão de Identidade Privilegiada.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão de PIM

A partir de novembro de 2019, a parte de funções da AZure AD da Gestão de Identidade Privilegiada está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão de Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos deste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com a [função de administrador privilegiado.](../roles/permissions-reference.md#privileged-role-administrator)
1. Open **Azure AD Gestão de Identidade Privilegiada.** Se tiver um banner no topo da página geral, siga as instruções no separador **Nova versão** deste artigo. Caso contrário, siga as instruções no separador **versão anterior.**

    [![Selecione Azure AD > Gestão de Identidade Privilegiada.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="activate-a-role-for-new-version"></a>Ativar um papel para nova versão

Quando precisar de assumir um papel de AD Azure, pode solicitar a ativação abrindo **as minhas funções** na Gestão de Identidade Privilegiada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.** Para obter informações sobre como adicionar o azulejo privilegiado de Gestão de Identidade ao seu painel de instrumentos, consulte [Começar a utilizar a Gestão de Identidade Privilegiada.](pim-getting-started.md)

1. Selecione **as minhas funções**e, em seguida, selecione **as funções AD Azure** para ver uma lista das suas funções elegíveis Azure AD.

    ![A página dos meus papéis mostrando papéis que pode ativar](./media/pim-how-to-activate-role/my-roles.png)

1. Na lista de **funções AZure AD,** encontre o papel que pretende ativar.

    ![Funções de AD AZure - A minha lista de funções elegíveis](./media/pim-how-to-activate-role/activate-link.png)

1. **Selecione Ativar** para abrir a página Ativar.

    ![Funções AD AZure - página de ativação contém duração e âmbito](./media/pim-how-to-activate-role/activate-page.png)

1. Se a sua função necessitar de autenticação multi-factor, **selecione Verifique a sua identidade antes de prosseguir**. Só tem de autenticar uma vez por sessão.

    ![Verifique a minha identidade com o MFA antes da ativação da função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **Selecione Verifique a minha identidade** e siga as instruções para fornecer uma verificação adicional de segurança.

    ![Tela para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se pretender especificar um âmbito reduzido, selecione **Scope** para abrir o painel do filtro. No painel de filtros, pode especificar os recursos Azure AD aos quais precisa de acesso. É uma boa prática pedir acesso apenas aos recursos de que precisa.

1. Se necessário, especifique uma hora de início de ativação personalizada. A função AD AZure seria ativada após o tempo selecionado.

1. Na caixa **Reason,** insira o motivo do pedido de ativação.

1. **Selecione Ativar**.

    Se a [função necessitar de aprovação](pim-resource-roles-approval-workflow.md) para ser ativada, aparecerá uma notificação no canto superior direito do seu navegador informando-o de que o pedido está pendente de aprovação.

    ![O pedido de ativação está pendente de notificação de aprovação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Veja o estado dos seus pedidos para nova versão

Pode visualizar o estado dos seus pedidos pendentes para ativar.

1. Abrir Azure AD Gestão de Identidade Privilegiada.

1. Selecione **Os meus pedidos** para ver uma lista da sua função AD Azure e pedidos de função de recurso Azure.

    ![Meus pedidos - página AD Azure mostrando os seus pedidos pendentes](./media/pim-how-to-activate-role/my-requests-page.png)

1. Percorra o direito de visualizar a coluna **'Estado de pedido'.**

## <a name="cancel-a-pending-request-for-new-version"></a>Cancelar um pedido pendente de nova versão

Se não necessitar de ativação de uma função que exija aprovação, pode cancelar um pedido pendente a qualquer momento.

1. Abrir Azure AD Gestão de Identidade Privilegiada.

1. Selecione **os meus pedidos.**

1. Para o papel que pretende cancelar, selecione o link **Cancelar.**

    Quando selecionar Cancelar, o pedido será cancelado. Para ativar novamente o papel, terá de apresentar um novo pedido de ativação.

   ![A minha lista de pedidos com ação cancelada em destaque](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Resolução de problemas para nova versão

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não concedidas após a ativação de uma função

Quando ativar uma função na Gestão de Identidade Privilegiada, a ativação pode não se propagar instantaneamente a todos os portais que requerem o papel privilegiado. Às vezes, mesmo que a alteração seja propagada, a colocação na cache Web num portal pode fazer com que a alteração não entre imediatamente em vigor. Se a sua ativação for adiada, eis o que deve fazer.

1. Termine sessão no portal do Azure e, em seguida, inicie sessão novamente.

1. Na Gestão de Identidade Privilegiada, verifique se está listado como membro do papel.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="activate-a-role-previous-version"></a>Ativar uma função (versão anterior)

Quando você precisa assumir um papel AD Azure, você pode solicitar a ativação usando a opção de navegação **de minhas funções** em Gestão de Identidade Privilegiada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.** Para obter informações sobre como adicionar o azulejo privilegiado de Gestão de Identidade ao seu painel de instrumentos, consulte [Começar a utilizar a Gestão de Identidade Privilegiada.](pim-getting-started.md)

1. Clique **nos papéis de AD Azure**.

1. Clique nas **minhas funções** para ver uma lista das suas funções elegíveis para a Azure AD.

    ![Funções Azure AD - As minhas funções que mostram a lista de funções elegíveis ou ativas](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Encontre um papel que queira ativar.

    ![Funções de Ad Azure - A minha lista de funções elegíveis mostrando link Activate](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique **em Ativar** para abrir o painel de detalhes de ativação de funções.

1. Se a sua função necessitar de autenticação multi-factor (MFA), clique em **Verificar a sua identidade antes de prosseguir**. Só tem de autenticar uma vez por sessão.

    ![Verifique o meu painel de identidade com MFA antes da ativação do papel](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique **em Verificar a minha identidade** e siga as instruções para fornecer uma verificação adicional de segurança.

    ![Página adicional de verificação de segurança perguntando como contactá-lo](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique **em Ativar** para abrir o painel de ativação.

    ![Painel de ativação para especificar hora de início, duração, bilhete e razão](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizada.

1. Especifique a duração da ativação.

1. Na caixa de **motivo de ativação,** insira o motivo do pedido de ativação. Algumas funções exigem que você forneça um número de bilhete problemático.

    ![Painel de ativação completo com uma hora de início, duração, bilhete e razão personalizado](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique **em Ativar**.

    Se a função não necessitar de aprovação, aparece um painel de estado de **ativação** que apresenta o estado da ativação.

    ![Página de estado de ativação mostrando os três estágios de ativação](./media/pim-how-to-activate-role/activation-status.png)

    Assim que todas as fases estiverem concluídas, clique na ligação **Iniciar** para assinar fora do portal Azure. Quando voltar a entrar no portal, pode agora usar o papel.

    Se a [função necessitar de aprovação](./azure-ad-pim-approval-workflow.md) para ser ativada, aparecerá uma notificação Azure no canto superior direito do seu navegador informando-o de que o pedido está pendente de aprovação.

## <a name="view-the-status-of-your-requests-previous-version"></a>Ver o estado dos seus pedidos (versão anterior)

Pode visualizar o estado dos seus pedidos pendentes para ativar.

1. Abrir Azure AD Gestão de Identidade Privilegiada.

1. Clique **nos papéis de AD Azure**.

1. Clique nos **meus pedidos** para ver uma lista dos seus pedidos.

    ![Funções AD AD - A minha lista de pedidos](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role-previous-version"></a>Desativar um papel (versão anterior)

Uma vez ativada uma função, desativa-se automaticamente quando o seu prazo (duração elegível) é atingido.

Se completar as suas tarefas de administrador mais cedo, também pode desativar uma função manualmente na Gestão de Identidade Privilegiada AD Azure.

1. Abrir Azure AD Gestão de Identidade Privilegiada.

1. Clique **nos papéis de AD Azure**.

1. Clique nos **meus papéis.**

1. Clique **em funções Ative** para ver a sua lista de funções ativas.

1. Encontre o papel que acabou de usar e, em seguida, clique em **Desativar**.

## <a name="cancel-a-pending-request-previous-version"></a>Cancelar um pedido pendente (versão anterior)

Se não necessitar de ativação de uma função que exija aprovação, pode cancelar um pedido pendente a qualquer momento.

1. Abrir Azure AD Gestão de Identidade Privilegiada.

1. Clique **nos papéis de AD Azure**.

1. Clique nos **meus pedidos.**

1. Para o papel que pretende cancelar, clique no botão **Cancelar.**

    Quando clicar em Cancelar, o pedido será cancelado. Para ativar novamente o papel, terá de apresentar um novo pedido de ativação.

   ![A minha lista de pedidos com o botão Cancelar realçado](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot-previous-version"></a>Resolução de problemas (versão anterior)

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não concedidas após a ativação de uma função

Quando ativar uma função na Gestão de Identidade Privilegiada, a ativação pode não se propagar instantaneamente a todos os portais que requerem o papel privilegiado. Às vezes, mesmo que a alteração seja propagada, a colocação na cache Web num portal pode fazer com que a alteração não entre imediatamente em vigor. Se a sua ativação for adiada, eis o que deve fazer.

1. Termine sessão no portal do Azure e, em seguida, inicie sessão novamente.

    Quando ativar um papel AD Azure, verá as fases da sua ativação. Quando todas as fases estiverem concluídas, verá a ligação **Terminar sessão**. Pode usar este link para assinar. Isto resolverá a maioria dos casos para atraso de ativação.

1. Na Gestão de Identidade Privilegiada, verifique se está listado como membro do papel.

 ---

## <a name="next-steps"></a>Passos seguintes

- [Ativar os meus papéis de AD Azure em Gestão de Identidade Privilegiada](pim-how-to-activate-role.md)

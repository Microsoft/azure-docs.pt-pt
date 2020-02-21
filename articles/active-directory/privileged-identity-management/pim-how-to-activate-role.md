---
title: Ativar as minhas funções de AD Azure na PIM - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Saiba como ativar funções de AD Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77499230"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Ativar as minhas funções de AD Azure na PIM

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) simplifica a forma como as empresas gerem o acesso privilegiado a recursos em Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se foi elegível para um papel administrativo, isso significa que pode ativar esse papel quando precisa de realizar ações privilegiadas. Por exemplo, se gere ocasionalmente as funcionalidades do Office 365, os administradores privilegiados da sua organização podem não torná-lo um Administrador Global permanente, uma vez que esse papel também afeta outros serviços. Em vez disso, tornam-no elegível para funções de AD Azure, como O Administrador Online de Intercâmbio. Pode solicitar ativar essa função quando precisar dos seus privilégios, e depois terá o controlo do administrador por um período de tempo pré-determinado.

Este artigo é para administradores que precisam ativar o seu papel de AD Azure na Gestão de Identidade Privilegiada.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão do PIM

A partir de novembro de 2019, a parte da AD Azure da Privileged Identity Management está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão da Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos neste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com o papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Open **Azure AD Privileged Identity Management.** Se tiver um banner no topo da página de visão geral, siga as instruções no separador **versão Nova** deste artigo. Caso contrário, siga as instruções no separador versão **anterior.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="activate-a-role"></a>Ativar um papel

Quando precisa assumir uma função de AD Azure, pode solicitar a ativação utilizando a opção de navegação **das Minhas funções** na Gestão de Identidade Privilegiada.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Privileged Identity Management.** Para obter informações sobre como adicionar o azulejo privilegiado de Gestão de Identidade ao seu painel de instrumentos, consulte Comece a utilizar a Gestão de [Identidade Privilegiada.](pim-getting-started.md)

1. Selecione **as minhas funções**e, em seguida, selecione **funções Azure AD** para ver uma lista das suas funções de AD Azure elegíveis.

    ![A minha página de papéis mostrando papéis que pode ativar](./media/pim-how-to-activate-role/my-roles.png)

1. Na lista de **papéis da AD Azure,** encontre o papel que pretende ativar.

    ![Papéis da AD Azure - A minha lista de papéis elegíveis](./media/pim-how-to-activate-role/activate-link.png)

1. **Selecione Ativar** para abrir o painel Activate.

    ![Funções Azure AD - página de ativação contém duração e âmbito](./media/pim-how-to-activate-role/activate-page.png)

1. Se o seu papel necessitar de autenticação multifactor, selecione **Verifique a sua identidade antes de prosseguir**. Só tens de autenticar uma vez por sessão.

    ![Verifique a minha identidade com o MFA antes da ativação do papel](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecione Verificar a **minha identidade** e siga as instruções para fornecer uma verificação de segurança adicional.

    ![Ecrã para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se pretender especificar um âmbito reduzido, selecione **Scope** para abrir o painel do filtro. No painel de filtros, pode especificar os recursos da AD Azure a que necessita de acesso. É uma boa prática pedir acesso apenas aos recursos que precisa.

1. Se necessário, especifique uma hora de início de ativação personalizada. A função Azure AD seria ativada após o tempo selecionado.

1. Na caixa **Reason,** introduza o motivo do pedido de ativação.

1. **Selecione Ativar**.

    Se a função não necessitar de aprovação, é ativada e adicionada à lista de funções ativas. Se quiser usar o papel, siga os passos na próxima secção.

    ![Painel de ativação concluído com âmbito, hora de início, duração e razão](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Se a [função necessitar](pim-resource-roles-approval-workflow.md) de aprovação para ser ativada, uma notificação aparecerá no canto superior direito do seu navegador informando-o de que o pedido está pendente de aprovação.

    ![Pedido de ativação está pendente de notificação de aprovação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Use uma função imediatamente após a ativação

Em caso de atraso após a ativação, siga estes passos após a ativação para utilizar imediatamente as suas funções de AD Azure.

1. Open Azure AD Privileged Identity Management.

1. Selecione **as minhas funções** para ver uma lista das suas funções de AD Azure elegíveis e funções de recurso Azure.

1. Selecione **funções Azure AD**.

1. Selecione o separador **de funções Ativa.**

1. Uma vez que o papel esteja ativo, assine para fora do portal e volte a entrar.

    O papel deve agora estar disponível para ser utilizado.

## <a name="view-the-status-of-your-requests"></a>Veja o estado dos seus pedidos

Pode ver o estado dos seus pedidos pendentes para ativar.

1. Open Azure AD Privileged Identity Management.

1. Selecione **Os meus pedidos** para ver uma lista do seu papel de AD Azure e pedidos de função de recursos Azure.

    ![Meus pedidos - Página da AD Azure mostrando os seus pedidos pendentes](./media/pim-how-to-activate-role/my-requests-page.png)

1. Percorra para a direita a coluna **'Status'** de pedido.

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Se não necessitar de ativação de uma função que requer aprovação, pode cancelar um pedido pendente a qualquer momento.

1. Open Azure AD Privileged Identity Management.

1. Selecione **os meus pedidos.**

1. Para o papel que pretende cancelar, selecione o link **Cancelar.**

    Quando selecionar cancelar, o pedido será cancelado. Para voltar a ativar a função, terá de apresentar um novo pedido de ativação.

   ![A minha lista de pedidos com a ação de cancelamento destacada](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não concedidas após a ativação de uma função

Quando ativa uma função na Gestão de Identidade Privilegiada, a ativação pode não se propagar instantaneamente a todos os portais que requerem o papel privilegiado. Às vezes, mesmo que a alteração seja propagada, a colocação na cache Web num portal pode fazer com que a alteração não entre imediatamente em vigor. Se a sua ativação está atrasada, eis o que deve fazer.

1. Termine sessão no portal do Azure e, em seguida, inicie sessão novamente.

    Quando ativar uma função de AD Azure, verá as fases da sua ativação. Quando todas as fases estiverem concluídas, verá a ligação **Terminar sessão**. Pode usar este link para assinar. Isto resolverá a maioria dos casos para o atraso de ativação.

1. Na Gestão de Identidade Privilegiada, verifique se está listado como membro do cargo.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="activate-a-role"></a>Ativar um papel

Quando precisa de assumir um papel de AD Azure, pode solicitar a ativação utilizando a opção de navegação **das Minhas funções** na Gestão de Identidade Privilegiada.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Privileged Identity Management.** Para obter informações sobre como adicionar o azulejo privilegiado de Gestão de Identidade ao seu painel de instrumentos, consulte Comece a utilizar a Gestão de [Identidade Privilegiada.](pim-getting-started.md)

1. Clique em **funções de AD Azure**.

1. Clique nas **minhas funções** para ver uma lista dos seus papéis de AD Azure elegíveis.

    ![Funções Azure AD - As minhas funções mostrando a lista de papéis elegíveis ou ativos](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Encontre um papel que queira ativar.

    ![Funções Azure AD - A minha lista de papéis elegíveis mostrando o link Activate](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique **em Ativar** para abrir o painel de detalhes de ativação da função.

1. Se o seu papel necessitar de autenticação multifactor (MFA), clique em **Verificar a sua identidade antes de prosseguir**. Só tens de autenticar uma vez por sessão.

    ![Verifique o meu painel de identidade com MFA antes da ativação do papel](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique **em Verificar a minha identidade** e siga as instruções para fornecer verificação de segurança adicional.

    ![Página adicional de verificação de segurança perguntando como contatá-lo](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique **em Ativar** para abrir o painel de ativação.

    ![Painel de ativação para especificar a hora de início, duração, bilhete e razão](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizada.

1. Especifique a duração da ativação.

1. Na caixa de motivo de **ativação,** introduza o motivo do pedido de ativação. Algumas funções exigem que forneça um número de bilhete problemático.

    ![Painel de ativação concluído com uma hora de início personalizada, duração, bilhete e razão](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique em **Ativar**.

    Se a função não necessitar de aprovação, aparece um painel de estado de **ativação** que apresenta o estado da ativação.

    ![Página de estado de ativação mostrando os três estágios de ativação](./media/pim-how-to-activate-role/activation-status.png)

    Uma vez concluídas todas as etapas, clique no link **Sign out** para assinar fora do portal Azure. Quando voltar a entrar no portal, pode agora usar o papel.

    Se a [função necessitar](./azure-ad-pim-approval-workflow.md) de aprovação para ser ativada, uma notificação do Azure aparecerá no canto superior direito do seu navegador informando-o de que o pedido está pendente de aprovação.

## <a name="view-the-status-of-your-requests"></a>Veja o estado dos seus pedidos

Pode ver o estado dos seus pedidos pendentes para ativar.

1. Open Azure AD Privileged Identity Management.

1. Clique em **funções de AD Azure**.

1. Clique nos **meus pedidos** para ver uma lista dos seus pedidos.

    ![Funções azure AD - A minha lista de pedidos](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Desativar um papel

Uma vez ativada uma função, desativa-se automaticamente quando o seu prazo (duração elegível) é atingido.

Se completar as suas tarefas de administrador mais cedo, também pode desativar manualmente uma função na Azure AD Privileged Identity Management.

1. Open Azure AD Privileged Identity Management.

1. Clique em **funções de AD Azure**.

1. Clique nos **meus papéis.**

1. Clique em **funções Ativas** para ver a sua lista de funções ativas.

1. Encontre o papel que já fez de usar e, em seguida, clique em **Desativar**.

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Se não necessitar de ativação de uma função que requer aprovação, pode cancelar um pedido pendente a qualquer momento.

1. Open Azure AD Privileged Identity Management.

1. Clique em **funções de AD Azure**.

1. Clique nos **meus pedidos.**

1. Para o papel que pretende cancelar, clique no botão **Cancelar.**

    Quando clicar em Cancelar, o pedido será cancelado. Para voltar a ativar a função, terá de apresentar um novo pedido de ativação.

   ![A minha lista de pedidos com o botão Cancel em destaque](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não concedidas após a ativação de uma função

Quando ativa uma função na Gestão de Identidade Privilegiada, a ativação pode não se propagar instantaneamente a todos os portais que requerem o papel privilegiado. Às vezes, mesmo que a alteração seja propagada, a colocação na cache Web num portal pode fazer com que a alteração não entre imediatamente em vigor. Se a sua ativação está atrasada, eis o que deve fazer.

1. Termine sessão no portal do Azure e, em seguida, inicie sessão novamente.

    Quando ativar uma função de AD Azure, verá as fases da sua ativação. Quando todas as fases estiverem concluídas, verá a ligação **Terminar sessão**. Pode usar este link para assinar. Isto resolverá a maioria dos casos para o atraso de ativação.

1. Na Gestão de Identidade Privilegiada, verifique se está listado como membro do cargo.

 ---

## <a name="next-steps"></a>Passos Seguintes

- [Ativar as minhas funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-activate-role.md)

---
title: Ativar funções de recurso Azure na PIM - Azure AD [ Microsoft Docs
description: Saiba como ativar as suas funções de recurso Azure na Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023141"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Ativar as minhas funções de recurso Azure na Gestão de Identidade Privilegiada

Utilize a Gestão de Identidade Privilegiada (PIM) para permitir que os membros elegíveis para recursos Do Azure programem a ativação para uma data e hora futuras. Também podem selecionar uma duração de ativação específica dentro do máximo (configurada por administradores).

Este artigo é para membros que precisam ativar o seu papel de recurso Azure na Gestão de Identidade Privilegiada.

## <a name="activate-a-role"></a>Ativar um papel

Quando precisa de assumir uma função de recurso Azure, pode solicitar a ativação utilizando a opção de navegação **das Minhas funções** na Gestão de Identidade Privilegiada.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Privileged Identity Management.** Para obter informações sobre como adicionar o azulejo privilegiado de Gestão de Identidade ao seu painel de instrumentos, consulte Comece a utilizar a Gestão de [Identidade Privilegiada.](pim-getting-started.md)

1. **Selecione as minhas funções.**

    ![A minha página de papéis mostrando papéis que pode ativar](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selecione funções de **recurso Azure** para ver uma lista das suas funções de recurso Azure elegíveis.

   ![Os meus papéis - Página de funções de recursos Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Na lista de funções de **recursos do Azure,** encontre o papel que pretende ativar.

    ![Funções de recursos Azure - A minha lista de papéis elegíveis](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **Selecione Ativar** para abrir o painel Activate.

1. Se o seu papel necessitar de autenticação multifactor, selecione **Verifique a sua identidade antes de prosseguir**. Só tens de autenticar uma vez por sessão.

    ![Verifique a minha identidade com o MFA antes da ativação do papel](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecione Verificar a **minha identidade** e siga as instruções para fornecer uma verificação de segurança adicional.

    ![Ecrã para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se pretender especificar um âmbito reduzido, selecione **Scope** para abrir o painel de filtros Resource.

    É uma boa prática apenas pedir acesso aos recursos que precisa. No painel de filtros Resource, pode especificar os grupos de recursos ou recursos a que necessita de acesso.

    ![Ativar - Painel de filtro de recursos para especificar o âmbito](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. O membro seria ativado após o tempo selecionado.

1. Na caixa **Reason,** introduza o motivo do pedido de ativação.

    ![Painel de ativação concluído com âmbito, hora de início, duração e razão](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **Selecione Ativar**.

    Se a função não necessitar de aprovação, é ativada e adicionada à lista de funções ativas. Se quiser usar o papel, siga os passos na próxima secção.

    Se a [função necessitar](pim-resource-roles-approval-workflow.md) de aprovação para ser ativada, uma notificação aparecerá no canto superior direito do seu navegador informando-o de que o pedido está pendente de aprovação.

    ![Pedido de ativação está pendente de notificação de aprovação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Use uma função imediatamente após a ativação

Em caso de atraso após a ativação, siga estes passos após a ativação para utilizar imediatamente as suas funções de recurso Azure.

1. Open Azure AD Privileged Identity Management.

1. Selecione **as minhas funções** para ver uma lista das suas funções de AD Azure elegíveis e funções de recurso Azure.

1. Selecione **funções de recurso Azure**.

1. Selecione o separador **de funções Ativa.**

1. Uma vez que o papel esteja ativo, assine para fora do portal e volte a entrar.

    O papel deve agora estar disponível para ser utilizado.

## <a name="view-the-status-of-your-requests"></a>Veja o estado dos seus pedidos

Pode ver o estado dos seus pedidos pendentes para ativar.

1. Open Azure AD Privileged Identity Management.

1. Selecione **Os meus pedidos** para ver uma lista do seu papel de AD Azure e pedidos de função de recursos Azure.

    ![Meus pedidos - Página de recursos Azure mostrando os seus pedidos pendentes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

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

    Quando ativar uma função de recurso Azure, verá as fases da sua ativação. Quando todas as fases estiverem concluídas, verá a ligação **Terminar sessão**. Pode usar este link para assinar. Isto resolverá a maioria dos casos para o atraso de ativação.

1. Na Gestão de Identidade Privilegiada, verifique se está listado como membro do cargo.

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Ativar as minhas funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-activate-role.md)

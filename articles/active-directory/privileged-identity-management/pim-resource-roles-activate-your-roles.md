---
title: Ativar as minhas funções de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Aprenda a ativar as suas funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501657"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Ativar as minhas funções de recursos do Azure no PIM

Utilizar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), os membros da função elegível para recursos do Azure podem agendar ativação numa data futura e a hora. Também podem selecionar uma duração de ativação específico dentro do máximo (configurado por administradores).

Este artigo é para os membros que tem de ativar a respetiva função de recursos do Azure no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando precisar da desempenhar uma função de recursos do Azure, pode pedir a ativação ao utilizar o **minhas funções** opção de navegação no PIM.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o mosaico do PIM ao dashboard, consulte [começar a utilizar o PIM](pim-getting-started.md).

1. Clique em **minhas funções**.

    ![Minha página de funções, funções, que pode ativar a mostrar](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Clique em **funções de recursos do Azure** para ver uma lista das suas funções de recursos do Azure elegíveis.

   ![As minhas funções - página de funções de recursos do Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Na **funções de recursos do Azure** lista, localize a função que pretende ativar.

    ![Funções de recursos do Azure - minha lista de funções elegíveis](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Clique em **Activate** para abrir o painel ativar.

1. Se a sua função requer autenticação multifator (MFA), clique em **verificar a sua identidade antes de continuar**. Só precisa que autenticar uma vez por sessão.

    ![Verificar a minha identidade com a MFA antes da ativação de função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Clique em **verificar a minha identidade** e siga as instruções para fornecer verificação adicional de segurança.

    ![Ecrã para verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se pretender especificar um âmbito reduzido, clique em **âmbito** para abrir o painel de filtro de recursos.

    É melhor prática para apenas solicitar acesso a recursos de que precisa. No painel de filtro de recursos, pode especificar os grupos de recursos ou recursos que precisam acessar.

    ![Ativar - o painel de filtro de recurso para especificar o âmbito](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizado. O membro seria ativado após o período selecionado.

1. Na **motivo** , digite a razão para o pedido de ativação.

    ![Concluído ativar painel com escopo, a hora de início, duração e motivo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Clique em **ativar**.

    Se a função de não necessitar de aprovação, ativado e adicionado à lista de funções do Active Directory. Se pretender utilizar a função, siga os passos na secção seguinte.

    Se o [a função requer aprovação](pim-resource-roles-approval-workflow.md) para ativar, uma notificação será exibida no canto superior direito do seu navegador informando o pedido está com aprovação pendente.

    ![Pedido de ativação é a notificação de aprovação pendente](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Utilize uma função imediatamente após a ativação

Em caso de qualquer atraso após a ativação, siga estes passos depois de ativar a utilizar as funções de recursos do Azure imediatamente.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **minhas funções** para ver uma lista de sua elegíveis funções do Azure AD e funções de recursos do Azure.

1. Clique em **funções de recursos do Azure**.

1. Clique nas **funções do Active Directory** separador.

1. Quando a função estiver ativa, termine sessão do portal e inicie sessão novamente.

    A função agora deve estar disponível para utilização.

## <a name="view-the-status-of-your-requests"></a>Ver o estado dos pedidos

Pode ver o estado dos seus pedidos pendentes para ativar.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **os meus pedidos** para ver uma lista de sua função do Azure AD e a função de recursos do Azure pedidos.

    ![Os meus pedidos - página de recursos do Azure que mostra os pedidos pendentes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Desloque-se para a direita para ver os **estado do pedido de** coluna.

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Se não necessitar de ativação de uma função que requer aprovação, pode cancelar um pedido pendente em qualquer altura.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **os meus pedidos**.

1. Para a função que pretende cancelar, clique nas **Cancelar** ligação.

    Ao clicar em Cancelar, o pedido será cancelado. Para ativar a função mais uma vez, terá que submeter um novo pedido de ativação.

   ![Minha lista de pedidos com a ação de cancelamento realçada](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Não são concedidas permissões após a ativação de uma função

Quando ativar uma função no PIM, a ativação não pode propagar instantaneamente para todos os portais que requerem a função com privilégios. Às vezes, mesmo que a alteração é propagada, cache da web num portal pode resultar na alteração não entrarem em vigor imediatamente. Se a ativação é atrasada, eis o que deve fazer.

1. Terminar sessão do portal do Azure e, em seguida, inicie sessão novamente.

    Ao ativar uma função de recursos do Azure, verá as fases de sua ativação. Assim que todas as fases são concluídas, verá uma **terminar sessão** ligação. Pode utilizar esta ligação para terminar sessão. Isto irá resolver a maioria dos casos, para atraso de ativação.

1. No PIM, certifique-se de que está listado como o membro da função.

## <a name="next-steps"></a>Passos Seguintes

- [Expandir ou renovar funções de recursos do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Ativar as minhas funções do Azure AD no PIM](pim-how-to-activate-role.md)

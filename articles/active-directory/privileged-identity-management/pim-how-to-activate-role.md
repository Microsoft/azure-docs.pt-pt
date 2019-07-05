---
title: Ativar as minhas funções do Azure AD no PIM - Azure Active Directory | Documentos da Microsoft
description: Aprenda a ativar as funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b3e02222580da67a4eeb4159e4fc5dc3b9a98f
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501685"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Ativar as minhas funções do Azure AD no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifica como as empresas a gerir o acesso privilegiado a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se tiver sido efetuado elegível para uma função administrativa, o que significa que pode ativar essa função, quando precisa efetuar ações privilegiadas. Por exemplo, se ocasionalmente gerir recursos do Office 365, os administradores de função com privilégios da sua organização podem não fazer é um Administrador Global permanente, uma vez que essa função tem impacto sobre outros serviços, também. Em vez disso, eles fazem-no elegível para funções do Azure AD, como o administrador do Exchange Online. Pode pedir para ativar essa função, quando precisar de seus privilégios e, em seguida, terá controlo de administrador para um período de tempo predeterminado.

Este artigo destina-se a administradores que precisam de ativar a respetiva função do Azure AD no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando tiver de assumir uma função do Azure AD, pode pedir a ativação ao utilizar o **minhas funções** opção de navegação no PIM.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o mosaico do PIM ao dashboard, consulte [começar a utilizar o PIM](pim-getting-started.md).

1. Clique em **funções do Azure AD**.

1. Clique em **minhas funções** para ver uma lista de sua elegíveis funções do Azure AD.

    ![Funções do Azure AD - as minhas funções que mostra a lista de funções elegíveis ou Active Directory](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Localize uma função que pretende ativar.

    ![Funções do Azure AD – minha lista de funções elegíveis que mostra ativar ligação](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique em **Activate** para abrir o painel de detalhes de ativação de função.

1. Se a sua função requer autenticação multifator (MFA), clique em **verificar a sua identidade antes de continuar**. Só precisa que autenticar uma vez por sessão.

    ![Verifique se o meu painel de identidade com a MFA antes da ativação de função](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique em **verificar a minha identidade** e siga as instruções para fornecer verificação adicional de segurança.

    ![Página de verificação de segurança adicionais perguntar como entrar em contacto consigo](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique em **Activate** para abrir o painel de ativação.

    ![Painel de ativação para especificar a hora de início, duração, pedido de suporte e motivo](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizado.

1. Especifique a duração de ativação.

1. Na **razão da ativação** , digite a razão para o pedido de ativação. Algumas funções exigem que forneça um número de pedido de suporte de problemas.

    ![Painel de ativação concluída com uma hora de início personalizados, a duração, o pedido de suporte e a razão](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique em **ativar**.

    Se a função de não necessitar de aprovação, um **estado de ativação** é apresentado o painel que apresenta o estado da ativação.

    ![Página de estado de ativação que mostra as três fases de ativação](./media/pim-how-to-activate-role/activation-status.png)

    Depois de todas as fases são concluídas, clique nas **terminar sessão** hiperligação terminar sessão do portal do Azure. Quando iniciar sessão novamente no portal, agora, pode utilizar a função.

    Se o [a função requer aprovação](./azure-ad-pim-approval-workflow.md) para ativar, uma notificação será exibida no canto superior direito do seu navegador informando o pedido está com aprovação pendente.

    ![Pedido de ativação é a notificação de aprovação pendente](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Ver o estado dos pedidos

Pode ver o estado dos seus pedidos pendentes para ativar.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Azure AD**.

1. Clique em **os meus pedidos** para ver uma lista dos pedidos.

    ![Funções do Azure AD – minha lista de pedidos](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Desativar uma função

Depois de uma função, automaticamente desativa quando for atingido o limite de tempo (duração elegível).

Se concluir suas tarefas de administrador no início, que também pode desativar uma função manualmente no Azure AD Privileged Identity Management.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Azure AD**.

1. Clique em **minhas funções**.

1. Clique em **funções do Active Directory** para ver uma lista de funções ativas.

1. Localizar a função já está a utilizar e, em seguida, clique em **desativar**.

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Se não necessitar de ativação de uma função que requer aprovação, pode cancelar um pedido pendente em qualquer altura.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Azure AD**.

1. Clique em **os meus pedidos**.

1. Para a função que pretende cancelar, clique nas **Cancelar** botão.

    Ao clicar em Cancelar, o pedido será cancelado. Para ativar a função mais uma vez, terá que submeter um novo pedido de ativação.

   ![Minha lista de pedidos com o botão de cancelar realçado](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Não são concedidas permissões após a ativação de uma função

Quando ativar uma função no PIM, a ativação não pode propagar instantaneamente para todos os portais que requerem a função com privilégios. Às vezes, mesmo que a alteração é propagada, cache da web num portal pode resultar na alteração não entrarem em vigor imediatamente. Se a ativação é atrasada, eis o que deve fazer.

1. Terminar sessão do portal do Azure e, em seguida, inicie sessão novamente.

    Ao ativar uma função do Azure AD, verá as fases de sua ativação. Assim que todas as fases são concluídas, verá uma **terminar sessão** ligação. Pode utilizar esta ligação para terminar sessão. Isto irá resolver a maioria dos casos, para atraso de ativação.

1. No PIM, certifique-se de que está listado como o membro da função.

## <a name="next-steps"></a>Passos Seguintes

- [Ativar as minhas funções de recursos do Azure no PIM](pim-resource-roles-activate-your-roles.md)

---
title: Ativar as minhas funções de diretório do Azure AD no PIM | Documentos da Microsoft
description: Aprenda a ativar as funções de diretório do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fc0a22f4300a06b5c827973ffb2436d0b8cfb36
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542765"
---
# <a name="activate-my-azure-ad-directory-roles-in-pim"></a>Ativar as minhas funções de diretório do Azure AD no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifica como as empresas a gerir o acesso privilegiado a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se tiver sido efetuado elegível para uma função administrativa, o que significa que pode ativar essa função, quando precisa efetuar ações privilegiadas. Por exemplo, se ocasionalmente gerir recursos do Office 365, os administradores de função com privilégios da sua organização podem não fazer é um Administrador Global permanente, uma vez que essa função tem impacto sobre outros serviços, também. Em vez disso, eles fazem-no elegível para funções do Azure AD, como o administrador do Exchange Online. Pode pedir para ativar essa função, quando precisar de seus privilégios e, em seguida, terá controlo de administrador para um período de tempo predeterminado.

Este artigo destina-se a administradores que precisam de ativar a respetiva função de diretório do Azure AD no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando tiver de assumir uma função de diretório do Azure AD, pode pedir a ativação ao utilizar o **minhas funções** opção de navegação no PIM.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o mosaico do PIM ao dashboard, consulte [começar a utilizar o PIM](pim-getting-started.md).

1. Clique em **funções de diretório do Azure AD**.

1. Clique em **minhas funções** para ver uma lista de sua elegíveis funções de diretório do Azure AD.

    ![Funções de diretório do Azure AD - as minhas funções](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Localize uma função que pretende ativar.

    ![Funções de diretório do Azure AD - minha lista de funções](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique em **Activate** para abrir o painel de detalhes de ativação de função.

1. Se a sua função requer autenticação multifator (MFA), clique em **verificar a sua identidade antes de continuar**. Só precisa que autenticar uma vez por sessão.

    ![Certifique-se com a MFA antes da ativação de função](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique em **verificar a minha identidade** e siga as instruções para fornecer verificação adicional de segurança.

    ![Verificação adicional de segurança](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique em **Activate** para abrir o painel de ativação.

    ![Painel de ativação](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizado.

1. Especifique a duração de ativação.

1. Na **razão da ativação** , digite a razão para o pedido de ativação. Algumas funções exigem que forneça um número de pedido de suporte de problemas.

    ![Painel de ativação concluída](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique em **ativar**.

    Se a função de não necessitar de aprovação, um **estado de ativação** é apresentado o painel que apresenta o estado da ativação.

    ![Estado de ativação](./media/pim-how-to-activate-role/activation-status.png)

    Depois de todas as fases são concluídas, clique nas **terminar sessão** hiperligação terminar sessão do portal do Azure. Quando iniciar sessão novamente no portal, agora, pode utilizar a função.

    Se o [a função requer aprovação](./azure-ad-pim-approval-workflow.md) para ativar, uma notificação será exibida no canto superior direito do seu navegador informando o pedido está com aprovação pendente.

    ![Pedido pendente de notificação](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Ver o estado dos pedidos

Pode ver o estado dos seus pedidos pendentes para ativar.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções de diretório do Azure AD**.

1. Clique em **os meus pedidos** para ver uma lista dos pedidos.

    ![Funções de diretório do Azure AD - os meus pedidos](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Desativar uma função

Depois de uma função, automaticamente desativa quando for atingido o limite de tempo (duração elegível).

Se concluir suas tarefas de administrador no início, que também pode desativar uma função manualmente no Azure AD Privileged Identity Management.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções de diretório do Azure AD**.

1. Clique em **minhas funções**.

1. Clique em **funções do Active Directory** para ver uma lista de funções ativas.

1. Localizar a função já está a utilizar e, em seguida, clique em **desativar**.

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Se não necessitar de ativação de uma função que requer aprovação, pode cancelar um pedido pendente em qualquer altura.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções de diretório do Azure AD**.

1. Clique em **os meus pedidos**.

1. Para a função que pretende cancelar, clique nas **Cancelar** botão.

    Ao clicar em Cancelar, o pedido será cancelado. Para ativar a função mais uma vez, terá que submeter um novo pedido de ativação.

   ![Cancelar pedido pendente](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-not-granted-after-activating-a-role"></a>Permissões concedidas não após a ativação de uma função

Quando ativar uma função no PIM, demora menos de 10 minutos antes de poder aceder ao portal administrativo pretendido ou executar funções dentro de uma carga de trabalho administrativa específica. Assim que a ativação estar concluída, termine a sessão do portal do Azure e inicie sessão novamente para começar a utilizar a função recém-ativada.

Para obter os passos de resolução de problemas adicionais, consulte [resolução de problemas de permissões elevadas](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

## <a name="next-steps"></a>Passos Seguintes

- [Ativar as minhas funções de recursos do Azure no PIM](pim-resource-roles-activate-your-roles.md)

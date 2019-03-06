---
title: Ativar as minhas funções de recursos do Azure no PIM | Documentos da Microsoft
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
ms.date: 11/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b02b0d36c6d7f5ddabae7a0e1d27dcca811fe56
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447783"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Ativar as minhas funções de recursos do Azure no PIM

Utilizar o Azure AD Privileged Identity Management (PIM), os membros da função elegível para recursos do Azure podem agendar ativação numa data futura e a hora. Também podem selecionar uma duração de ativação específico dentro do máximo (configurado por administradores).

Este artigo é para os membros que tem de ativar a respetiva função de recursos do Azure no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando precisar da desempenhar uma função de recursos do Azure, pode pedir a ativação ao utilizar o **minhas funções** opção de navegação no PIM.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o mosaico do PIM ao dashboard, consulte [começar a utilizar o PIM](pim-getting-started.md).

1. Clique em **minhas funções** para ver uma lista de sua elegíveis funções de diretório do Azure AD e funções de recursos do Azure.

    ![Funções de diretório do Azure AD e funções de recursos do Azure - as minhas funções](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Na **funções de recursos do Azure** lista, localize a função que pretende ativar.

    ![Funções de recursos do Azure - minha lista de funções](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Clique em **Activate** para abrir o painel ativar.

1. Se a sua função requer autenticação multifator (MFA), clique em **verificar a sua identidade antes de continuar**. Só precisa que autenticar uma vez por sessão.

    ![Certifique-se com a MFA antes da ativação de função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Clique em **verificar a minha identidade** e siga as instruções para fornecer verificação adicional de segurança.

    ![Verificação adicional de segurança](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se pretender especificar um âmbito reduzido, clique em **âmbito** para abrir o painel de filtro de recursos.

    É melhor prática para apenas solicitar acesso a recursos de que precisa. No painel de filtro de recursos, pode especificar os grupos de recursos ou recursos que precisam acessar.

    ![Ativar - filtro de recursos](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizado. O membro seria ativado após o período selecionado.

1. Na **motivo** , digite a razão para o pedido de ativação.

    ![Painel de ativar concluída](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Clique em **ativar**.

    Se a função de não necessitar de aprovação, ativado e adicionado à lista de funções do Active Directory. Se pretender utilizar a função imediatamente, siga os passos na secção seguinte.

    Se o [a função requer aprovação](pim-resource-roles-approval-workflow.md) para ativar, uma notificação será exibida no canto superior direito do seu navegador informando o pedido está com aprovação pendente.

    ![Pedido pendente de notificação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Utilize uma função imediatamente após a ativação

Quando ativar uma função no PIM, demora menos de 10 minutos antes de poder aceder ao portal administrativo pretendido ou executar funções dentro de uma carga de trabalho administrativa específica. Para forçar uma atualização de suas permissões, utilize o **acesso a aplicações** página conforme descrito nos passos seguintes.

1. Abra o Azure AD Privileged Identity Management.

1. Clique nas **acesso a aplicações** página.

    ![Acesso de aplicativo PIM - captura de ecrã](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Clique nas **recursos do Azure** ligação para reabrir o portal no **todos os recursos** página.

    Quando clicar nesse link, invalidar o token atual e forçar o portal do Azure para obter um novo token que deve conter as suas permissões atualizadas.

## <a name="view-the-status-of-your-requests"></a>Ver o estado dos pedidos

Pode ver o estado dos seus pedidos pendentes para ativar.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **os meus pedidos** para ver uma lista de sua função de diretório do Azure AD e a função de recursos do Azure pedidos.

    ![Funções de diretório do Azure AD e funções de recursos do Azure - os meus pedidos](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Desloque-se para a direita para ver os **estado do pedido de** coluna.

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Se não necessitar de ativação de uma função que requer aprovação, pode cancelar um pedido pendente em qualquer altura.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **os meus pedidos**.

1. Para a função que pretende cancelar, clique nas **Cancelar** ligação.

    Ao clicar em Cancelar, o pedido será cancelado. Para ativar a função mais uma vez, terá que submeter um novo pedido de ativação.

   ![Cancelar pedido pendente](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-not-granted-after-activating-a-role"></a>Permissões concedidas não após a ativação de uma função

Quando ativar uma função no PIM, demora menos de 10 minutos antes de poder aceder ao portal administrativo pretendido ou executar funções dentro de uma carga de trabalho administrativa específica. Para forçar uma atualização de suas permissões, utilize o **acesso aos aplicativos** página, tal como descrito anteriormente na [utilizar uma função imediatamente após a ativação](#use-a-role-immediately-after-activation).

Para obter os passos de resolução de problemas adicionais, consulte [resolução de problemas de permissões elevadas](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>Não é possível ativar uma função devido a um bloqueio de recursos

Se receber uma mensagem que um recurso do Azure é bloqueado ao tentar ativar uma função, poderá ser porque um recurso dentro do escopo de uma atribuição de função tem um bloqueio de recursos. Bloqueios de proteger os recursos de eliminação acidental ou alterações inesperadas. Um bloqueio também impede que o PIM remover uma atribuição de função no recurso no fim do período de ativação. Uma vez que o PIM não consegue funcionar corretamente quando um bloqueio é aplicado, o PIM proíbe os utilizadores de ativação de funções no recurso. Existem duas formas que pode resolver este problema:

- O bloqueio de eliminação, conforme descrito em [bloquear recursos pode prevenir alterações inesperadas](../../azure-resource-manager/resource-group-lock-resources.md).
- Se pretender manter o bloqueio, tornar a atribuição de função permanentes ou utilize uma conta de quebra de vidro.

## <a name="next-steps"></a>Passos Seguintes

- [Expandir ou renovar funções de recursos do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Ativar as minhas funções de diretório do Azure AD no PIM](pim-how-to-activate-role.md)

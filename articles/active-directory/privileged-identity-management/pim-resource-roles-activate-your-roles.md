---
title: Ativar funções de recurso Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como ativar as suas funções de recurso Azure na Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a6ddde80ca554aea25d24694aff76e61e47d928
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672462"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Ativar os meus recursos Azure na Gestão de Identidade Privilegiada

Use a Gestão de Identidade Privilegiada (PIM) para permitir que membros elegíveis para recursos Azure marquem a ativação para uma data e hora futuras. Também podem selecionar uma duração de ativação específica dentro do máximo (configurado pelos administradores).

Este artigo destina-se a membros que necessitem de ativar o seu papel de recurso Azure na Gestão de Identidade Privilegiada.

## <a name="activate-a-role"></a>Ativar um papel

Quando precisa assumir uma função de recurso Azure, pode solicitar a ativação utilizando a opção de navegação **das minhas funções** na Gestão de Identidade Privilegiada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.** Para obter informações sobre como adicionar o azulejo privilegiado de Gestão de Identidade ao seu painel de instrumentos, consulte [Começar a utilizar a Gestão de Identidade Privilegiada.](pim-getting-started.md)

1. Selecione **os meus papéis.**

    ![A página dos meus papéis mostrando papéis que pode ativar](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selecione **funções de recurso Azure** para ver uma lista das suas funções de recurso Azure elegíveis.

    ![Os meus papéis - Página de funções de recursos Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Na lista de **funções de recursos Azure,** encontre o papel que pretende ativar.

    ![Funções de recursos Azure - A minha lista de funções elegíveis](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **Selecione Ativar** para abrir a página Ativar.

    ![O painel de ativação aberto com âmbito, hora de início, duração e razão](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Se a sua função necessitar de autenticação multi-factor, **selecione Verifique a sua identidade antes de prosseguir**. Só tem de autenticar uma vez por sessão.

    ![Verifique a minha identidade com o MFA antes da ativação da função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **Selecione Verifique a minha identidade** e siga as instruções para fornecer uma verificação adicional de segurança.

    ![Tela para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se pretender especificar um âmbito reduzido, selecione **Scope** para abrir o painel de filtros de recursos.

    É uma boa prática apenas pedir acesso aos recursos de que precisa. No painel de filtros de recursos, pode especificar os grupos de recursos ou recursos a que necessita de acesso.

    ![Ativar - Painel de filtro de recursos para especificar o âmbito](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. O membro seria ativado após o tempo selecionado.

1. Na caixa **Reason,** insira o motivo do pedido de ativação.

    ![Painel de ativação concluído com âmbito, hora de início, duração e razão](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **Selecione Ativar**.

    Se a [função necessitar de aprovação](pim-resource-roles-approval-workflow.md) para ser ativada, aparecerá uma notificação no canto superior direito do seu navegador informando-o de que o pedido está pendente de aprovação.

    ![O pedido de ativação está pendente de notificação de aprovação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Ver o estado dos seus pedidos

Pode visualizar o estado dos seus pedidos pendentes para ativar.

1. Abrir Azure AD Gestão de Identidade Privilegiada.

1. Selecione **Os meus pedidos** para ver uma lista da sua função AD Azure e pedidos de função de recurso Azure.

    ![Os meus pedidos - Página de recursos Azure mostrando os seus pedidos pendentes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Percorra o direito de visualizar a coluna **'Estado de pedido'.**

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Se não necessitar de ativação de uma função que exija aprovação, pode cancelar um pedido pendente a qualquer momento.

1. Abrir Azure AD Gestão de Identidade Privilegiada.

1. Selecione **os meus pedidos.**

1. Para o papel que pretende cancelar, selecione o link **Cancelar.**

    Quando selecionar Cancelar, o pedido será cancelado. Para ativar novamente o papel, terá de apresentar um novo pedido de ativação.

   ![A minha lista de pedidos com ação cancelada em destaque](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não concedidas após a ativação de uma função

Quando ativar uma função na Gestão de Identidade Privilegiada, a ativação pode não se propagar instantaneamente a todos os portais que requerem o papel privilegiado. Às vezes, mesmo que a alteração seja propagada, a colocação na cache Web num portal pode fazer com que a alteração não entre imediatamente em vigor. Se a sua ativação for adiada, eis o que deve fazer.

1. Termine sessão no portal do Azure e, em seguida, inicie sessão novamente.
1. Na Gestão de Identidade Privilegiada, verifique se está listado como membro do papel.

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar funções de recursos do Azure na Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Ativar os meus papéis de AD Azure em Gestão de Identidade Privilegiada](pim-how-to-activate-role.md)

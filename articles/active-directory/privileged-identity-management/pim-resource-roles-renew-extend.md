---
title: Renovar as atribuições de recursos da Azure na PIM - Azure AD [ Microsoft Docs
description: Saiba como estender ou renovar as atribuições de recursos azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022902"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Alargar ou renovar atribuições de recursos azure na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) fornece controlos para gerir o ciclo de vida de acesso e atribuição de recursos Azure. Os administradores podem atribuir funções utilizando propriedades de início e fim de data. Quando a atribuição termina, a Privileged Identity Management envia notificações por e-mail aos utilizadores ou grupos afetados. Também envia notificações por e-mail aos administradores do recurso para garantir que o acesso adequado é mantido. As atribuições podem ser renovadas e permanecer visíveis num estado caducado por um período de até 30 dias, mesmo que o acesso não seja prolongado.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Apenas os administradores do recurso podem estender ou renovar as atribuições de funções. O utilizador ou grupo afetado pode solicitar a prorrogação das funções que estão prestes a expirar e solicitar a renovação de funções que já estão caducadas.

## <a name="when-are-notifications-sent"></a>Quando são enviadas notificações?

A Privileged Identity Management envia notificações por e-mail a administradores e utilizadores ou grupos de funções afetados que estão expirando no prazo de 14 dias e um dia antes da expiração. Envia um e-mail adicional quando uma atribuição expira oficialmente.

Os administradores recebem notificações quando um utilizador ou grupo atribuiu um pedido de função expirante ou expirado para prolongar ou renovar. Quando um administrador específico resolve o pedido, todos os outros administradores são notificados da decisão de resolução (aprovada ou negada). Em seguida, o utilizador ou grupo solicitado é notificado da decisão.

## <a name="extend-role-assignments"></a>Alargar as atribuições de funções

As seguintes etapas delineiam o processo de solicitação, resolução ou gestão de uma extensão ou renovação de uma atribuição de funções.

### <a name="self-extend-expiring-assignments"></a>Auto-prorrogação das atribuições de caducidade

Os utilizadores ou grupos atribuídos a uma função podem alargar as atribuições de funções de validade diretamente do separador **Elegível** ou **Ativo** na página minhas **funções** de um recurso e a partir do nível superior **As minhas funções** página do portal de Gestão de Identidade Privilegiada. Os utilizadores ou grupos podem solicitar a extensão de funções elegíveis e ativas (atribuídas) que expirem nos próximos 14 dias.

![Recursos Azure - A minha página de papéis listafunções elegíveis com uma coluna de Ação](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando a data-data final da atribuição é dentro de 14 dias, o botão para **estender** torna-se um link ativo na interface do utilizador. No exemplo seguinte, assuma que a data atual é 27 de março.

![Coluna de ação com ligações para Ativar ou Estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para solicitar uma extensão desta atribuição de funções, **selecione Extend** para abrir o formulário de pedido.

![Alargar o painel de atribuição de papéis com uma caixa reason](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para ver informações sobre a atribuição original, expandir **detalhes de Atribuição.** Introduza uma razão para o pedido de extensão e, em seguida, **selecione Extend**.

>[!NOTE]
>Recomendamos que inclua os detalhes do porquê da extensão ser necessária e por quanto tempo a extensão deve ser concedida (se tiver essa informação).

![Alargar o painel de atribuição de funções com detalhes de atribuição expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Em questão de momentos, os administradores de recursos recebem uma notificação por e-mail solicitando que revejam o pedido de extensão. Se já tiver sido apresentado um pedido de extensão, aparece no portal uma notificação do Azure.

![Notificação explicando que já existe uma extensão de atribuição de funções pendente](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vá à página **de pedidos pendentes** para ver o estado do seu pedido ou cancelá-lo.

![Recursos Azure - Página pendente de pedidos listando qualquer pendente solicitado e um link para Cancelar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Extensão aprovada pela Administração

Quando um utilizador ou grupo submete um pedido para estender uma atribuição de funções, os administradores de recursos recebem uma notificação de e-mail que contém os detalhes da atribuição original e o motivo do pedido. A notificação inclui um link direto para o pedido para que o administrador aprove ou negue.

Além de utilizarem o seguinte link a partir de e-mail, os administradores podem aprovar ou negar pedidos indo ao portal da administração de Gestão de Identidade Privilegiada e selecionando pedidos de **aprovação** no painel esquerdo.

![Recursos Azure - Aprovar pedidos de listagem de páginas pedidos e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um Administrador seleciona **Aprovar** ou **Negar,** os detalhes do pedido são mostrados, juntamente com um campo para fornecer uma justificação de negócio para os registos de auditoria.

![Aprovar pedido de atribuição de funções com motivo de pedido, tipo de atribuição, hora de início, tempo final e razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovarum pedido de prorrogação da atribuição de funções, os administradores de recursos podem escolher uma nova data de início, data de fim e tipo de atribuição. A alteração do tipo de atribuição poderá ser necessária se o administrador quiser fornecer acesso limitado para completar uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **Elegível** para **Ativo**. Isto significa que podem fornecer acesso ao solicitador sem exigir que eles se ativem.

### <a name="admin-initiated-extension"></a>Admin iniciado extensão

Se um utilizador atribuído a uma função não solicitar uma extensão para a atribuição de funções, um administrador pode estender uma atribuição em nome do utilizador. As prorrogações administrativas da atribuição de funções não requerem aprovação, mas as notificações são enviadas a todos os outros administradores após o alargamento do papel.

Para estender uma atribuição de funções, navegue para a função de recurso ou vista de atribuição na Gestão de Identidade Privilegiada. Encontre a atribuição que requer uma extensão. Em seguida, **selecione Extend** na coluna de ação.

![Recursos Azure - página de atribuições listando funções elegíveis com links para estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de papéis

Embora conceptualmente semelhante ao processo de pedido de extensão, o processo de renovação de uma atribuição de funções expirada é diferente. Utilizando os seguintes passos, as atribuições e os administradores podem renovar o acesso às funções vencidas quando necessário.

### <a name="self-renew"></a>Autorrenovar

Os utilizadores que já não possam aceder aos recursos podem aceder até 30 dias de histórico de atribuição expirado. Para isso, navegam para **My Roles** no painel esquerdo e, em seguida, selecionam o separador de **papéis Expirado** na secção de funções de recursos Azure.

![Minha página de papéis - Separador de papéis expirados](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A lista de funções apresentadas não tem **predefinições para funções elegíveis**. Utilize o menu suspenso para alternar entre as funções elegíveis e ativas atribuídas.

Para solicitar a renovação de qualquer uma das atribuições de funções na lista, selecione a ação **Renovar.** Então forneça uma razão para o pedido. É útil fornecer uma duração para além de qualquer contexto adicional ou uma justificação de negócio que possa ajudar o administrador de recursos a decidir aprovar ou negar.

![Renovar o painel de atribuição de papéis mostrando a caixa da razão](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Após a apresentação do pedido, os administradores de recursos são notificados de um pedido pendente para renovar uma atribuição de funções.

### <a name="admin-approves"></a>Administrador aprova

Os administradores de recursos podem aceder ao pedido de renovação a partir do link na notificação de e-mail ou acedendo à Gestão de Identidade Privilegiada do portal Azure e selecionando Os pedidos de **aprovação** do painel esquerdo.

![Recursos Azure - Aprovar pedidos de listagem de páginas pedidos e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **Aprovar** ou **Negar,** os detalhes do pedido são mostrados juntamente com um campo para fornecer uma justificação de negócio para os registos de auditoria.

![Aprovar pedido de atribuição de funções com motivo de pedido, tipo de atribuição, hora de início, tempo final e razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovarem um pedido de renovação da atribuição de funções, os administradores de recursos devem introduzir uma nova data de início, data de fim e tipo de atribuição.

### <a name="admin-renew"></a>Administrador renovar

Os administradores de recursos podem renovar as atribuições de funções expiradas do separador **Dos Membros** no menu de navegação à esquerda de um recurso. Também podem renovar as atribuições de funções expiradas a partir do separador de funções **expiradas** de uma função de recurso.

Para visualizar uma lista de todas as atribuições de funções expiradas, no ecrã dos **Membros,** selecione **funções caducadas**.

![Recursos Azure - Lista de membros página de funções caducadas com links para renovar](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Passos seguintes

- [Aprovar ou negar pedidos de funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-approval-workflow.md)
- [Configure definições de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)

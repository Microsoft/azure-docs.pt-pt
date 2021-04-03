---
title: Renovar atribuições de funções de recursos Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como estender ou renovar atribuições de funções de recursos Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f0d22fc540aae448a3da731b709a3b4ea13a69d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742238"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Alargar ou renovar atribuições de funções de recursos Azure na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) fornece controlos para gerir o ciclo de vida de acesso e atribuição para recursos Azure. Os administradores podem atribuir funções utilizando propriedades de início e fim da data de início e fim. Quando o fim da atribuição se aproxima, a Gestão de Identidade Privilegiada envia notificações de e-mail aos utilizadores ou grupos afetados. Também envia notificações por e-mail aos administradores do recurso para garantir que o acesso adequado seja mantido. As atribuições podem ser renovadas e permanecer visíveis num estado expirado por um período de validade até 30 dias, mesmo que o acesso não seja prolongado.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Apenas os administradores do recurso podem estender ou renovar atribuições de funções. O utilizador ou grupo afetado pode solicitar a extensão de funções que estão prestes a expirar e solicitar a renovação de funções que já estão caducadas.

## <a name="when-are-notifications-sent"></a>Quando são enviadas notificações?

A Gestão de Identidade Privilegiada envia notificações por e-mail a administradores e utilizadores ou grupos de funções que expiram no prazo de 14 dias e um dia antes da expiração. Envia um e-mail adicional quando uma atribuição expira oficialmente.

Os administradores recebem notificações quando um utilizador ou grupo atribuiu um pedido de função expirado ou caducado para prolongar ou renovar. Quando um administrador específico resolve o pedido, todos os outros administradores são notificados da decisão de resolução (aprovada ou negada). Em seguida, o utilizador ou grupo que solicita é notificado da decisão.

## <a name="extend-role-assignments"></a>Alargar atribuições de funções

Os passos seguintes descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de funções.

### <a name="self-extend-expiring-assignments"></a>Auto-prorrogação de atribuições caducadas

Os utilizadores ou grupos atribuídos a uma função podem estender as atribuições de funções expiradas diretamente a partir do **separador Elegível** ou **Ativo** na página **My Roles** of a resource e do nível superior A página **das minhas funções** do portal de Gestão de Identidade Privilegiada. Os utilizadores ou grupos podem solicitar o alargamento das funções elegíveis e ativas (atribuídas) que expirem nos próximos 14 dias.

![Recursos Azure - A minha página de papéis listando papéis elegíveis com uma coluna de Ação](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando a data de fim da atribuição é dentro de 14 dias, o botão a **estender** torna-se um link ativo na interface do utilizador. No exemplo seguinte, assuma que a data atual é 27 de março.

![Coluna de ação com links para Ativar ou Estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para solicitar uma extensão desta atribuição de funções, **selecione Extend** para abrir o formulário de pedido.

![Alargar o painel de atribuição de funções com uma caixa reason](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para visualizar informações sobre a atribuição original, expanda os **detalhes da Atribuição.** Introduza uma razão para o pedido de extensão e, em seguida, **selecione Extend**.

>[!NOTE]
>Recomendamos incluir os detalhes do porquê da extensão necessária, e por quanto tempo a extensão deve ser concedida (se tiver essa informação).

![Alargar o painel de atribuição de funções com detalhes de atribuição expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Em questão de momentos, os administradores de recursos recebem uma notificação de e-mail solicitando que revejam o pedido de extensão. Se já tiver sido apresentado um pedido de extensão, aparece no portal uma notificação do Azure.

![Notificação explicando que já existe uma extensão pendente de atribuição de funções](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vá à página **de pedidos pendentes** para ver o estado do seu pedido ou para cancelá-lo.

![Recursos Azure - Página de pedidos pendentes listando qualquer pedido pendente e um link para Cancelar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Extensão aprovada pela Administração

Quando um utilizador ou grupo submete um pedido para estender uma atribuição de funções, os administradores de recursos recebem uma notificação de e-mail que contém os detalhes da atribuição original e o motivo do pedido. A notificação inclui uma ligação direta ao pedido para que o administrador aprove ou negue.

Além de utilizarem o link seguindo o link a partir de e-mail, os administradores podem aprovar ou negar pedidos indo para o portal de administração de Gestão de Identidade Privilegiada e selecionando **Pedidos de Aprovação** no painel esquerdo.

![Recursos Azure - Aprovar pedidos de página de pedidos e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **Aprovar** ou **Negar,** os detalhes do pedido são apresentados, juntamente com um campo para fornecer uma justificação de negócio para os registos de auditoria.

![Aprovar pedido de atribuição de funções com pedido de motivo, tipo de atribuição, hora de início, tempo de fim e razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar um pedido de extensão da atribuição de funções, os administradores de recursos podem escolher uma nova data de início, data de fim e tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para completar uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **Elegível** para **Ativo.** Isto significa que podem fornecer acesso ao solicitador sem exigir que os ativem.

### <a name="admin-initiated-extension"></a>Admin iniciou extensão

Se um utilizador atribuído a uma função não solicitar uma extensão para a atribuição de funções, um administrador pode estender uma atribuição em nome do utilizador. As prorrogações administrativas da atribuição de funções não requerem aprovação, mas as notificações são enviadas a todos os outros administradores após o prolongamento da função.

Para estender uma atribuição de funções, navegue para a função de recurso ou vista de atribuição na Gestão de Identidade Privilegiada. Encontre a missão que requer uma extensão. Em seguida, **selecione Extend** na coluna de ação.

![Recursos Azure - página de atribuições listando funções elegíveis com links para estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de funções

Embora conceptualmente semelhante ao processo de pedido de extensão, o processo de renovação de uma atribuição de funções expirada é diferente. Utilizando os seguintes passos, as atribuições e os administradores podem renovar o acesso a funções caducadas quando necessário.

### <a name="self-renew"></a>Autorrenovar

Os utilizadores que já não puderem aceder aos recursos podem aceder até 30 dias do histórico de atribuição expirado. Para isso, navegam para **os Meus Papéis** no painel esquerdo e, em seguida, selecionam o separador **funções expiradas** na secção funções de recursos Azure.

![Página de papéis - Separador de papéis expirados](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A lista de funções apresentadas é de incumprimento das **funções elegíveis.** Utilize o menu suspenso para alternar entre funções elegíveis e ativas atribuídas.

Para solicitar a renovação de qualquer uma das atribuições de funções na lista, selecione a ação **Renovar.** Em seguida, fornecer uma razão para o pedido. É útil fornecer uma duração para além de qualquer contexto adicional ou uma justificação de negócio que possa ajudar o administrador de recursos a decidir aprovar ou negar.

![Renovar o painel de atribuição de funções mostrando a caixa da razão](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Após a apresentação do pedido, os administradores de recursos são notificados de um pedido pendente de renovação de uma atribuição de funções.

### <a name="admin-approves"></a>Administrador aprova

Os administradores de recursos podem aceder ao pedido de renovação a partir do link na notificação de email ou acedendo à Gestão de Identidade Privilegiada a partir do portal Azure e selecionando Pedidos de **aprovação** do painel esquerdo.

![Recursos Azure - Aprovar pedidos de página de pedidos e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **Aprovar** ou **Negar,** os detalhes do pedido são apresentados juntamente com um campo para fornecer uma justificação de negócio para os registos de auditoria.

![Aprovar pedido de atribuição de funções com pedido de motivo, tipo de atribuição, hora de início, tempo de fim e razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar um pedido de renovação da atribuição de funções, os administradores de recursos devem introduzir uma nova data de início, data de fim e tipo de atribuição.

### <a name="admin-renew"></a>Renovação de administrador

Os administradores de recursos podem renovar as atribuições de funções vencidas a partir do separador **Membros** no menu de navegação à esquerda de um recurso. Podem também renovar as atribuições de funções caducadas a partir do separador de funções **expiradas** de uma função de recurso.

Para visualizar uma lista de todas as atribuições de funções caducadas, no ecrã dos **membros,** selecione **Funções Expiradas**.

![Recursos Azure - Página de membros listando papéis caducados com links para renovar](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Passos seguintes

- [Aprovar ou negar pedidos de funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-approval-workflow.md)
- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)

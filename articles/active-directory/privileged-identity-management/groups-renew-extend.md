---
title: Renovar o proprietário do grupo caducado de atribuições de membros em Gestão de Identidade Privilegiada - Azure AD | Microsoft Docs
description: Saiba como estender ou renovar atribuições de grupos atribuíveis em Azure AD Gestão de Identidade Privilegiada (PIM).
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
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87505994"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Alargar ou renovar atribuições de grupos de acesso privilegiado (pré-visualização) em Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) fornece controlos para gerir o ciclo de vida de acesso e atribuição para grupos de acesso privilegiados. Os administradores podem atribuir funções utilizando propriedades de início e fim da data de início e fim. Quando o fim da atribuição se aproxima, a Gestão de Identidade Privilegiada envia notificações de e-mail aos utilizadores ou grupos afetados. Também envia notificações por e-mail aos administradores do recurso para garantir que o acesso adequado seja mantido. As atribuições podem ser renovadas e permanecer visíveis num estado expirado por um período de validade até 30 dias, mesmo que o acesso não seja prolongado.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar

Apenas os administradores do recurso podem estender ou renovar as atribuições privilegiadas do grupo de acesso. O utilizador ou grupo afetado pode solicitar o alargamento das atribuições que estão prestes a expirar e solicitar a renovação das atribuições que já estão caducadas.

## <a name="when-notifications-are-sent"></a>Quando as notificações são enviadas

A Gestão de Identidade Privilegiada envia notificações por e-mail aos administradores e utilizadores afetados das atribuições privilegiadas do grupo de acesso que estão a expirar:

- No prazo de 14 dias antes do termo
- Um dia antes da expiração
- Quando uma atribuição expira

Os administradores recebem notificações quando um utilizador ou grupo solicita para prolongar ou renovar uma atribuição expirada ou caducada. Quando um administrador resolve o pedido, todos os administradores e o utilizador que solicita são notificados da aprovação ou negação.

## <a name="extend-group-assignments"></a>Alargar as atribuições de grupo

Os passos seguintes descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de grupo.

### <a name="self-extend-expiring-assignments"></a>Auto-prorrogação de atribuições caducadas

Os utilizadores atribuídos a um grupo de acesso privilegiado podem estender as atribuições de grupo expirando diretamente a partir do **separador Elegível** ou **Ativo** na página **atribuições** para o grupo. Os utilizadores ou grupos podem solicitar o alargamento das atribuições elegíveis e ativas que expirem nos próximos 14 dias.

![A minha página de papéis listando assgnments elegíveis com uma coluna de Ação](media/groups-renew-extend/self-extend-group-assignment.png)

Quando a data de fim da atribuição for dentro de 14 dias, o comando **Extend** está disponível. Para solicitar uma extensão de uma atribuição de grupo, **selecione Extend** para abrir o formulário de pedido.

![Alargar o painel de atribuição de grupo com uma caixa de razão e detalhes](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>Recomendamos incluir os detalhes do porquê da extensão necessária, e por quanto tempo a extensão deve ser concedida (se tiver essa informação).

Em questão de momentos, os administradores recebem uma notificação de e-mail solicitando que revejam o pedido de extensão. Se já tiver sido apresentado um pedido de extensão, aparece no portal uma notificação do Azure.

Para visualizar o estado ou cancelar o seu pedido, abra a página **de pedidos pendentes** para a atribuição do grupo.

![Atribuições privilegiadas do grupo de acesso - Página de pedidos pendentes que mostra o link para Cancelar](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Extensão aprovada pela Administração

Quando um utilizador ou grupo submete um pedido para estender uma atribuição de grupo, os administradores recebem uma notificação de e-mail que contém os detalhes da atribuição original e o motivo do pedido. A notificação inclui uma ligação direta ao pedido para que o administrador aprove ou negue.

Além de utilizarem o link seguindo o link a partir de e-mail, os administradores podem aprovar ou negar pedidos indo para o portal de administração de Gestão de Identidade Privilegiada e selecionando **Pedidos de Aprovação** no painel esquerdo.

![Atribuições privilegiadas de grupo de acesso - Aprovar pedidos de listagem de páginas e links para aprovar ou negar](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Quando um administrador seleciona **Aprovar** ou **Negar,** os detalhes do pedido são apresentados, juntamente com um campo para fornecer uma justificação de negócio para os registos de auditoria.

![Aprovar pedido de atribuição de grupo com pedido de pedido ou razão, tipo de atribuição, hora de início, tempo de fim e razão](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

Ao aprovar um pedido de extensão de uma atribuição de grupo, os administradores de recursos podem escolher uma nova data de início, data de fim e tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para completar uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **Elegível** para **Ativo.** Isto significa que podem fornecer acesso ao solicitador sem exigir que os ativem.

### <a name="admin-initiated-extension"></a>Admin iniciou extensão

Se um utilizador atribuído a um grupo não solicitar uma extensão para a atribuição do grupo, um administrador pode estender uma atribuição em nome do utilizador. As prorrogações administrativas da atribuição do grupo não requerem aprovação, mas as notificações são enviadas a todos os outros administradores após o prolongamento da atribuição.

Para estender uma atribuição de grupo, navegue para a vista de atribuição na Gestão de Identidade Privilegiada. Encontre a missão que requer uma extensão. Em seguida, **selecione Extend** na coluna de ação.

![Atribuição de página de atribuições de atribuição de atribuições de grupos elegíveis com links para estender](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Renovar atribuições de grupo

Embora conceptualmente semelhante ao processo de pedido de extensão, o processo de renovação de uma atribuição de grupo expirada é diferente. Utilizando os seguintes passos, as atribuições e os administradores podem renovar o acesso às atribuições caducadas quando necessário.

### <a name="self-renew"></a>Autorrenovar

Os utilizadores que já não puderem aceder aos recursos podem aceder até 30 dias do histórico de atribuição expirado. Para isso, navegam para **os Meus Papéis** no painel esquerdo e, em seguida, selecionam o **separador de atribuições expiradas.**

![Página de papéis - Separador de atribuições expiradas](media/groups-renew-extend/groups-renew-from-my-roles.png)

A lista de atribuições apresentadas não tem padrão para **atribuições elegíveis.** Utilize o menu suspenso para alternar entre as atribuições Elegíveis e Ativas.

Para solicitar a renovação de qualquer uma das atribuições de grupo na lista, selecione a ação **Renovar.** Em seguida, fornecer uma razão para o pedido. É útil fornecer uma duração para além de qualquer contexto adicional ou uma justificação de negócio que possa ajudar o administrador de recursos a decidir aprovar ou negar.

![Renovar painel de atribuição de grupo mostrando a caixa reason](media/groups-renew-extend/groups-renew-request-form.png)

Após a apresentação do pedido, os administradores de recursos são notificados de um pedido pendente de renovação de uma atribuição de grupo.

### <a name="admin-approves"></a>Administrador aprova

Os administradores de recursos podem aceder ao pedido de renovação a partir do link na notificação de email ou acedendo à Gestão de Identidade Privilegiada a partir do portal Azure e selecionando Pedidos de **aprovação** do painel esquerdo.

Quando um administrador seleciona **Aprovar** ou **Negar,** os detalhes do pedido são apresentados juntamente com um campo para fornecer uma justificação de negócio para os registos de auditoria.

Ao aprovar um pedido de renovação de uma atribuição de grupo, os administradores de recursos devem introduzir uma nova data de início, data de fim e tipo de atribuição.

## <a name="next-steps"></a>Passos seguintes

- [Aprovar ou negar pedidos de atribuição de grupos de acesso privilegiados em Gestão de Identidade Privilegiada](groups-approval-workflow.md)
- [Configurar configurar configurações privilegiadas do grupo de acesso na Gestão de Identidade Privilegiada](groups-role-settings.md)

---
title: Estender ou renovar atribuições de função de recurso do Azure no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como estender ou renovar atribuições de função de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eeba148945f7aa52dd32edc0fec4c45e2ab0748
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804127"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Estender ou renovar atribuições de função de recurso do Azure no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) apresenta novos controles para gerenciar o acesso e o ciclo de vida de atribuição para recursos do Azure. Os administradores podem atribuir Associação usando as propriedades de data e hora de início e término. Quando as finalidades de atribuição se aproximam, o PIM envia notificações por email aos usuários ou grupos afetados. Ele também envia notificações por email aos administradores do recurso para garantir que o acesso apropriado seja mantido. As atribuições podem ser renovadas e permanecer visíveis em um estado expirado por até 30 dias, mesmo que o acesso não seja estendido.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Somente os administradores do recurso podem estender ou renovar atribuições de função. O membro afetado pode solicitar a extensão das funções que estão prestes a expirar e a solicitação para renovar as funções que já expiraram.

## <a name="when-are-notifications-sent"></a>Quando as notificações são enviadas?

O PIM envia notificações por email aos administradores e aos Membros afetados de funções que estão expirando em 14 dias e um dia antes da expiração. Ele envia um email adicional quando uma atribuição expira oficialmente. 

Os administradores recebem notificações quando um membro de uma função expirada ou expirada solicita a extensão ou renovação. Quando um administrador específico resolve a solicitação, todos os outros administradores são notificados sobre a decisão de resolução (aprovada ou negada). Em seguida, o membro solicitante é notificado sobre a decisão. 

## <a name="extend-role-assignments"></a>Estender atribuições de função

As etapas a seguir descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de função. 

### <a name="member-extend"></a>Expandir o membro

Os membros de uma atribuição de função podem estender as atribuições de função que expiram diretamente da guia **qualificada** ou **ativa** na página **minhas funções** de um recurso e na página **minhas funções** de nível superior do portal do PIM. Os membros podem solicitar a extensão de funções qualificadas e ativas (atribuídas) que expiram nos próximos 14 dias.

![Recursos do Azure – página minhas funções listando funções qualificadas com uma coluna de ação](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando a data/hora de término da atribuição é dentro de 14 dias, o botão para **estender** se torna um link ativo na interface do usuário. No exemplo a seguir, suponha que a data atual seja 27 de março.

![Coluna de ação com links para ativar ou estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para solicitar uma extensão dessa atribuição de função, selecione **estender** para abrir o formulário de solicitação.

![Estender o painel de atribuição de função com uma caixa de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para exibir informações sobre a atribuição original, expanda **detalhes da atribuição**. Insira um motivo para a solicitação de extensão e, em seguida, selecione **estender**.

>[!Note]
>É recomendável incluir os detalhes de por que a extensão é necessária e por quanto tempo a extensão deve ser concedida (se você tiver essas informações).

![Estender painel de atribuição de função com detalhes de atribuição expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Em questão de minutos, os administradores de recursos recebem uma notificação por email solicitando que eles revisem a solicitação de extensão. Se uma solicitação para estender já tiver sido enviada, uma notificação do sistema aparecerá na parte superior da portal do Azure explicando o erro.

![Notificação explicando que já existe uma extensão de atribuição de função pendente existente](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vá para a página **solicitações pendentes** no painel esquerdo para exibir o status da solicitação ou para cancelá-la.

![Recursos do Azure-página de solicitações pendentes listando qualquer solicitação pendente e um link para cancelar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Aprovação do administrador

Quando um membro envia uma solicitação para estender uma atribuição de função, os administradores de recursos recebem uma notificação por email que contém os detalhes da atribuição original e o motivo da solicitação. A notificação inclui um link direto para a solicitação para que o administrador aprove ou negue. 

Além de usar o link do email a seguir, os administradores podem aprovar ou negar solicitações acessando o portal de administração do PIM e selecionando **aprovar solicitações** no painel esquerdo.

![Recursos do Azure – aprovar solicitações de listagem de páginas e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **aprovar** ou **negar**, os detalhes da solicitação são mostrados, juntamente com um campo para fornecer justificativa para os logs de auditoria.

![Aprovar solicitação de atribuição de função com motivo do solicitante, tipo de atribuição, hora de início, hora de término e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para estender a atribuição de função, os administradores de recursos podem escolher uma nova data de início, data de término e tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para concluir uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **qualificado** para **ativo**. Isso significa que eles podem fornecer acesso ao solicitante sem exigir que eles sejam ativados.

### <a name="admin-extend"></a>Expandir Admin

Se um membro de função esquecer ou não puder solicitar uma extensão de associação de função, um administrador poderá estender uma atribuição em nome do membro. As extensões administrativas da Associação de função não exigem aprovação, mas as notificações são enviadas a todos os outros administradores após a extensão da função.

Para estender uma associação de função, navegue até a exibição função de recurso ou membro no PIM. Localize o membro que requer uma extensão. Em seguida, selecione **estender** na coluna ação.

![Recursos do Azure-página Membros listando funções qualificadas com links para estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de função

Embora seja conceitualmente semelhante ao processo de solicitação de uma extensão, o processo para renovar uma atribuição de função expirada é diferente. Usando as etapas a seguir, os membros e os administradores podem renovar o acesso às funções expiradas quando necessário.

### <a name="member-renew"></a>Renovação do membro

Os membros que não podem mais acessar recursos podem acessar até 30 dias de histórico de atribuição expirado. Para fazer isso, navegue até **minhas funções** no painel esquerdo e, em seguida, selecione a guia **funções expiradas** na seção funções de recurso do Azure.

![Página minhas funções-guia funções expiradas](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A lista de funções mostrada usa como padrão as **funções qualificadas**. Use o menu suspenso para alternar entre funções atribuídas qualificadas e ativas.

Para solicitar a renovação de qualquer uma das atribuições de função na lista, selecione a ação **renovar** . Em seguida, forneça um motivo para a solicitação. É útil fornecer uma duração além de qualquer contexto adicional que ajude o administrador de recursos a decidir aprovar ou negar.

![Painel renovar atribuição de função mostrando a caixa de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Depois que a solicitação tiver sido enviada, os administradores de recursos serão notificados sobre uma solicitação pendente para renovar uma atribuição de função.

### <a name="admin-approves"></a>Aprovações de administrador

Os administradores de recursos podem acessar a solicitação de renovação por meio do link na notificação por email ou acessando o PIM na portal do Azure e selecionando **aprovar solicitações** no painel esquerdo.

![Recursos do Azure – aprovar solicitações de listagem de páginas e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **aprovar** ou **negar**, os detalhes da solicitação são mostrados junto com um campo para fornecer justificativa para os logs de auditoria.

![Aprovar solicitação de atribuição de função com motivo do solicitante, tipo de atribuição, hora de início, hora de término e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para renovar a atribuição de função, os administradores de recursos devem inserir uma nova data de início, data de término e tipo de atribuição. 

### <a name="admin-renew"></a>Renovar Admin

Os administradores de recursos podem renovar as atribuições de função expiradas na guia **Membros** no menu de navegação à esquerda de um recurso. Eles também podem renovar as atribuições de função expiradas de dentro da guia funções **expiradas** de uma função de recurso.

Para exibir uma lista de todas as atribuições de função expiradas, na tela **Membros** , selecione **funções expiradas**.

![Recursos do Azure-página Membros listando funções expiradas com links para renovar](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Passos Seguintes

- [Aprovar ou negar solicitações para funções de recurso do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)

---
title: Expandir ou renovar as atribuições de funções de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como expandir ou renovar as atribuições de funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 147b1714c88fd93a3098ecf7a28164a227af29de
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476291"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Expandir ou renovar as atribuições de funções de recursos do Azure no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) introduz novos controles para gerir o ciclo de vida de acesso e a atribuição de recursos do Azure. Os administradores podem atribuir associação usando propriedades de data e hora de início e de fim. Quando se aproxima o fim de atribuição, o PIM envia notificações por e-mail para os utilizadores afetados ou grupos. Também envia notificações por e-mail aos administradores do recurso para garantir que o acesso apropriado é mantido. Atribuições podem ser renovadas e permanecem visíveis no estado expirado durante 30 dias, mesmo que o acesso não é expandido.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovação?

Apenas os administradores do recurso podem expandir ou renovar as atribuições de funções. O membro afetado pode pedir uma extensão de funções que estão prestes a expirar e o pedido para renovar funções que já estão a expirou.

## <a name="when-are-notifications-sent"></a>Quando são enviadas notificações?

PIM envia notificações por e-mail para os administradores e os membros afetados das funções que estão prestes a expirar dentro de 14 dias e 1 dia antes da expiração. Envia uma mensagem de e-mail adicional quando uma atribuição oficialmente expira. 

Os administradores recebem notificações quando um membro de uma função prestes a expirar ou expirado pede para expandir ou renovar. Quando um administrador específico resolve o pedido, todos os outros administradores são notificados sobre a decisão de resolução (aprovado ou negado). Em seguida, o membro solicitante é notificado da decisão. 

## <a name="extend-role-assignments"></a>Expandir as atribuições de funções

Os passos seguintes descrevem o processo para pedir, resolver ou administrar uma extensão ou renovação de uma atribuição de função. 

### <a name="member-extend"></a>Expandir o membro

Membros de uma atribuição de função podem expandir prestes a expirar atribuições de funções diretamente a partir da **elegíveis** ou **Active Directory** separador no **minhas funções** página de um recurso e de nível superior **Minhas funções** página do portal do PIM. Os membros podem pedir uma extensão de funções (atribuídas) elegíveis e ativas que expirarem dentro de 14 dias após.

![Recursos do Azure - as minhas funções de página lista de funções elegíveis com uma coluna de ação](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando a atribuição data-hora de fim está dentro de 14 dias, no botão para **expandir** torna-se de um link ativo na interface do usuário. No exemplo a seguir, suponha que a data atual for 27 de Março.

![Coluna de ação com ligações para ativar ou expandir](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para pedir uma extensão desta atribuição de função, selecione **expandir** para abrir o formulário de pedido.

![Expandir o painel de atribuição de função com uma caixa de razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para ver informações sobre a atribuição original, expanda **detalhes de atribuição**. Introduza um motivo para o pedido de extensão e, em seguida, selecione **expandir**.

>[!Note]
>É recomendável, incluindo os detalhes por que a extensão é necessária, e para o período de tempo deve ser concedida a extensão (se tiver estas informações).

![Expandir o painel de atribuição de função com os detalhes de atribuição expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Numa questão de minutos, os administradores de recursos recebem uma notificação de e-mail a pedir que ele Revise o pedido de extensão. Se já foi submetido um pedido para aumentar a, é apresentada uma notificação de alerta na parte superior do portal do Azure, que explica o erro.

![Notificação explicando que há já existente pendentes extensão da atribuição de função](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vá para o **pedidos pendentes** página no painel da esquerda para ver o estado do pedido de ou para o cancelar.

![Recursos do Azure - pendentes à página de pedidos a listagem de todas as pendentes solicitada e uma ligação para cancelar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Aprovação de administrador

Quando um membro envia um pedido para aumentar a atribuição de função, os administradores de recursos recebem uma notificação de e-mail que contém os detalhes sobre a atribuição original e o motivo para o pedido. A notificação inclui uma ligação direta para o pedido para o administrador aprovar ou recusar. 

Além de usar a seguir a ligação do e-mail, os administradores podem aprovar ou negar pedidos indo para a administração do PIM portal e selecionando **aprovar pedidos** no painel esquerdo.

![Recursos do Azure - aprovar pedidos de página pedidos de listagem e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando o administrador seleciona **aprovar** ou **negar**, são apresentados os detalhes do pedido, juntamente com um campo para fornecer a justificação para os registos de auditoria.

![Aprovar pedido de atribuição de função com o motivo de requerente, o tipo de atribuição, hora de início, hora de fim e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando a aprovar um pedido para aumentar a atribuição de função, os administradores de recursos podem escolher uma nova data de início, a data de fim e o tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador deseja disponibilizar acesso limitado para concluir uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **elegíveis** ao **Active Directory**. Isso significa que podem fornecer acesso para o requerente sem exigi-los a ativar.

### <a name="admin-extend"></a>Expandir Admin

Se um membro da função se esquecer ou não conseguir pedir uma extensão de associação de função, um administrador pode expandir uma atribuição em nome do membro. Extensões administrativas de associação de função não necessitam de aprovação, mas as notificações são enviadas para todos os outros administradores depois da função tiver sido expandida.

Para expandir um membro da função, navegue para a vista de função ou membro de recursos no PIM. Encontre o membro que requer uma extensão. Em seguida, selecione **expandir** na coluna ação.

![Recursos do Azure - os membros da página lista de funções elegíveis com ligações para expandir](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovar as atribuições de função

Embora conceitualmente semelhante ao processo para pedir uma extensão, o processo de renovar uma atribuição de função expirada é diferente. Utilizar os seguintes passos, membros e administradores podem renovar acesso a funções expiradas quando necessário.

### <a name="member-renew"></a>Membro de renovação

Os membros que já não podem aceder a recursos podem acessar até 30 dias do histórico de atribuição expirada. Para tal, navegue até **minhas funções** no painel esquerdo e, em seguida, selecione a **expirado funções** separador na secção de funções de recursos do Azure.

![Minha página de funções - separador de funções expiradas](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A lista de funções mostrado assume a predefinição **funções elegíveis**. Utilize o menu de lista suspensa para alternar entre elegíveis e ativas atribuídos a funções.

Para pedir a renovação de qualquer um das atribuições de funções na lista, selecione o **renovar** ação. Em seguida, forneça um motivo para o pedido. É útil fornecer uma duração para além de qualquer contexto adicional que ajuda o administrador de recursos decidir aprovar ou recusar.

![O painel de atribuição de função mostra a caixa de razão de renovação](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Depois do pedido foi submetido, os administradores de recursos são notificados de um pedido pendente para renovar uma atribuição de função.

### <a name="admin-approves"></a>Aprovação do administrador

Os administradores de recursos podem acessar o pedido de renovação da ligação na notificação por e-mail ou ao aceder ao PIM no portal do Azure e selecionar **aprovar pedidos** no painel à esquerda.

![Recursos do Azure - aprovar pedidos de página pedidos de listagem e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando o administrador seleciona **aprovar** ou **negar**, os detalhes do pedido são apresentados juntamente com um campo para fornecer a justificação para os registos de auditoria.

![Aprovar pedido de atribuição de função com o motivo de requerente, o tipo de atribuição, hora de início, hora de fim e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando a aprovar um pedido para renovar a atribuição de função, os administradores de recursos tem de introduzir uma nova data de início, a data de fim e o tipo de atribuição. 

### <a name="admin-renew"></a>Renovar Admin

Os administradores de recursos podem renovar as atribuições de funções expiradas do **membros** separador no menu de navegação à esquerda de um recurso. Eles também podem renovar as atribuições de funções expiradas de dentro do **expirado** separador de funções de uma função de recursos.

Para ver uma lista de todos os expirou atribuições de funções, à **membros** ecrã, selecione **expirado funções**.

![Recursos do Azure - página Membros listagem funções expiradas com ligações para renovar](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Passos Seguintes

- [Aprovar ou recusar pedidos para funções de recursos do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Configurar as definições de função de recursos do Azure no PIM](pim-resource-roles-configure-role-settings.md)

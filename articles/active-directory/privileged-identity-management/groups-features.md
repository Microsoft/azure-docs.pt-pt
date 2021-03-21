---
title: Gerir grupos Azure AD privilegiados em Gestão de Identidade Privilegiada (PIM) | Microsoft Docs
description: Como gerir membros e proprietários de grupos de acesso privilegiados em Gestão de Identidade Privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512397"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Capacidades de gestão para acesso privilegiado Grupos AD de acesso (pré-visualização)

Na Gestão de Identidade Privilegiada (PIM), pode agora atribuir elegibilidade para adesão ou propriedade de grupos de acesso privilegiados. A partir desta pré-visualização, pode atribuir funções incorporadas ao Azure Ative Directory (Azure AD) a grupos de nuvem e utilizar o PIM para gerir a elegibilidade e ativação do membro do grupo e do proprietário. Para obter mais informações sobre grupos atribuíveis por funções em Azure AD, consulte [use grupos de nuvem para gerir atribuições de funções no Azure Ative Directory (pré-visualização)](../roles/groups-concept.md).

>[!Important]
> Para atribuir um grupo privilegiado de acesso a uma função de acesso administrativo ao Centro de Intercâmbio, Segurança e Conformidade, ou SharePoint, utilize a experiência do portal AD AD **Ad Roles and Administrators** e não na experiência dos Grupos de Acesso Privilegiados para tornar o utilizador ou grupo elegível para ativação no grupo.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Requerem políticas diferentes para cada grupo atribuível

Algumas organizações usam ferramentas como a colaboração entre negócios e negócios (B2B) da Azure AD para convidar os seus parceiros como convidados para a sua organização Azure AD. Em vez de uma única política just-in-time para todas as atribuições a um papel privilegiado, você pode criar dois diferentes grupos de acesso privilegiado com as suas próprias políticas. Pode impor requisitos menos rigorosos para os seus colaboradores de confiança e requisitos mais rigorosos, como fluxo de trabalho de aprovação para os seus parceiros quando solicitam ativação no seu grupo designado.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Ativar várias atribuições de funções num único pedido

Com a pré-visualização dos grupos de acesso privilegiados, pode dar aos administradores específicos da carga de trabalho um acesso rápido a várias funções com um único pedido just-in-time. Por exemplo, os seus Administradores de Escritórios Tier 3 podem precisar de acesso just-in-time ao Exchange Admin, Office Apps Admin, Teams Admin e Search Admin para investigar minuciosamente os incidentes diariamente. Antes de hoje seriam necessários quatro pedidos consecutivos, que são um processo que demora algum tempo. Em vez disso, pode criar um grupo atribuível chamado "Tier 3 Office Admins", atribuí-lo a cada uma das quatro funções anteriormente mencionadas (ou quaisquer funções incorporadas a AD AZure) e capacitá-la para acesso privilegiado na secção de Atividade do grupo. Uma vez habilitado para acesso privilegiado, pode configurar as definições just-in-time para os membros do grupo e atribuir os seus administradores e proprietários como elegíveis. Quando os administradores se elevarem para o grupo, tornar-se-ão membros dos quatro papéis da AD Azure.

## <a name="extend-and-renew-group-assignments"></a>Alargar e renovar atribuições de grupo

Depois de configurar as suas atribuições de proprietário ou membro, a primeira pergunta que pode fazer é o que acontece se uma atribuição expirar? Nesta nova versão, oferecemos duas opções para este cenário:

- Prolongar – Quando uma atribuição de funções se aproxima do termo, o utilizador pode usar a Gestão de Identidade Privilegiada para solicitar uma extensão para a atribuição de funções
- Renovar – Quando uma atribuição de funções já tiver expirado, o utilizador pode utilizar a Gestão de Identidade Privilegiada para solicitar uma renovação para a atribuição de funções

Ambas as ações iniciadas pelo utilizador requerem uma aprovação de um administrador global ou administrador privilegiado. Os administradores deixarão de precisar de estar no negócio de gerir estas caducidades. Podem aguardar os pedidos de extensão ou renovação e aprová-los se o pedido for válido.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um proprietário ou membro do grupo de acesso privilegiado](groups-assign-member-owner.md)
- [Aprovar ou negar pedidos de ativação para membros e proprietários privilegiados do grupo de acesso](groups-approval-workflow.md)

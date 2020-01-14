---
title: Requisitos de licença para usar o Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Descreve os requisitos de licenciamento para usar o Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932323"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisitos de licença para usar o Privileged Identity Management

Para usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), um diretório deve ter uma licença válida. Além disso, as licenças devem ser atribuídas aos administradores e aos usuários relevantes. Este artigo descreve os requisitos de licença para usar o Privileged Identity Management.

## <a name="valid-licenses"></a>Licenças válidas

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Quantas licenças você precisa ter?

Certifique-se de que seu diretório tenha pelo menos tantas licenças Azure AD Premium P2, pois você tem funcionários que executarão as seguintes tarefas:

- Usuários atribuídos como qualificados para funções do Azure AD gerenciadas usando o PIM
- Usuários capazes de aprovar ou rejeitar solicitações de ativação no PIM
- Usuários atribuídos a uma função de recurso do Azure com atribuições just-in-time ou diretas (baseadas em tempo)  
- Usuários atribuídos a uma revisão de acesso
- Usuários que executam revisões de acesso

As licenças do Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Nenhuma licença é necessária para usuários com funções de administrador global ou de administrador de função com privilégios que configuram o PIM, configurar políticas, receber alertas e configurar revisões de acesso.

Para obter mais informações sobre licenças, consulte [atribuir ou remover licenças usando o portal de Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns exemplos de cenários de licença para ajudá-lo a determinar o número de licenças que você deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| O Woodgrove Bank tem 10 administradores para diferentes departamentos e 2 administradores globais que configuram e gerenciam o PIM. Eles tornam cinco administradores qualificados. | Cinco licenças para os administradores que estão qualificados | 5 |
| O design gráfico Institute tem 25 administradores dos quais 14 são gerenciados por meio do PIM. A ativação de função requer aprovação e há três usuários diferentes na organização que podem aprovar ativações. | 14 licenças para as funções qualificadas + três aprovadores | 17 |
| A contoso tem 50 administradores dos quais a 42 é gerenciada por meio do PIM. A ativação de função requer aprovação e há cinco usuários diferentes na organização que podem aprovar ativações. A contoso também faz revisões mensais de usuários atribuídos a revisores e funções de administrador são os gerentes dos usuários dos quais seis não estão em funções de administrador gerenciadas pelo PIM. | 42 licenças para as funções qualificadas + cinco aprovadores + seis revisores | 53 |

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se uma licença Azure AD Premium P2, EMS E5 ou de avaliação expirar, Privileged Identity Management recursos não estarão mais disponíveis em seu diretório:

- As atribuições de função permanentes às funções do Azure AD não serão afetadas.
- O serviço de Privileged Identity Management no portal do Azure, bem como os cmdlets do API do Graph e as interfaces do PowerShell do Privileged Identity Management, não estarão mais disponíveis para que os usuários ativem funções privilegiadas, gerenciem o acesso privilegiado ou executem revisões de acesso de funções com privilégios.
- As atribuições de função qualificadas das funções do Azure AD serão removidas, pois os usuários não poderão mais ativar funções privilegiadas.
- Todas as revisões de acesso em andamento das funções do Azure AD serão encerradas e Privileged Identity Management definições de configuração serão removidas.
- Privileged Identity Management não enviará mais emails sobre alterações de atribuição de função.

## <a name="next-steps"></a>Passos seguintes

- [Implantar Privileged Identity Management](pim-deployment-plan.md)
- [Começar a usar o Privileged Identity Management](pim-getting-started.md)
- [Funções que você não pode gerenciar no Privileged Identity Management](pim-roles.md)

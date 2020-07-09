---
title: Requisitos de licença para utilizar Gestão de Identidade Privilegiada - Diretório Ativo Azure Microsoft Docs
description: Descreve os requisitos de licenciamento para utilizar a Azure AD Privileged Identity Management (PIM).
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
ms.topic: how-to
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d931206e99516e0320d0cbedd0812389ee41aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742169"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisitos de licença para utilizar a Gestão de Identidade Privilegiada

Para utilizar o Azure Ative Directory (Azure AD) Privileged Identity Management (PIM), um diretório deve ter uma licença válida. Além disso, as licenças devem ser atribuídas aos administradores e utilizadores relevantes. Este artigo descreve os requisitos de licença para utilizar a Gestão de Identidade Privilegiada.

## <a name="valid-licenses"></a>Licenças válidas

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Quantas licenças tem?

Certifique-se de que o seu diretório tem pelo menos tantas licenças Azure AD Premium P2 como tem funcionários que irão executar as seguintes tarefas:

- Utilizadores atribuídos como elegíveis para funções AD Azure geridos através de PIM
- Utilizadores capazes de aprovar ou rejeitar pedidos de ativação em PIM
- Utilizadores afetados a uma função de recurso Azure com atribuições just-in-time ou diretas (baseadas no tempo)  
- Utilizadores designados para uma revisão de acesso
- Utilizadores que realizam comentários de acesso

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para utilizadores com funções de Administrador Global ou Administrador privilegiado que criem PIM, configuram políticas, recebam alertas e criem revisões de acesso.

Para obter mais informações sobre licenças, consulte [atribuir ou remover licenças utilizando o portal Azure Ative Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Exemplo de cenários de licença

Aqui estão alguns cenários de licença de exemplo para ajudá-lo a determinar o número de licenças que deve ter.

| Scenario | Cálculo | Número de licenças |
| --- | --- | --- |
| O Woodgrove Bank tem 10 administradores para diferentes departamentos e 2 Administradores Globais que configuram e gerem a PIM. Fazem cinco administradores elegíveis. | Cinco licenças para os administradores elegíveis | 5 |
| O Instituto de Design Gráfico tem 25 administradores dos quais 14 são geridos através da PIM. A ativação de funções requer aprovação e há três utilizadores diferentes na organização que podem aprovar ativações. | 14 licenças para as funções elegíveis + três aprovadores | 17 |
| A Contoso tem 50 administradores, dos quais 42 são geridos através da PIM. A ativação de funções requer aprovação e há cinco utilizadores diferentes na organização que podem aprovar ativações. A Contoso também faz revisões mensais dos utilizadores atribuídos a funções de administrador e os revisores são os gestores dos utilizadores dos quais seis não estão em funções de administrador geridos pela PIM. | 42 licenças para as funções elegíveis + cinco aprovadores + seis revisores | 53 |

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se um Azure AD Premium P2, EMS E5 ou licença de julgamento expirar, as funcionalidades de Gestão de Identidade Privilegiada deixarão de estar disponíveis no seu diretório:

- As atribuições de funções permanentes para as funções de AD Azure não serão afetadas.
- O serviço de Gestão de Identidade Privilegiada no portal Azure, bem como as interfaces de CmDlets e PowerShell da Gestão de Identidade Privilegiada, deixarão de estar disponíveis para os utilizadores ativarem funções privilegiadas, gerirem o acesso privilegiado ou realizarem revisões de acesso a funções privilegiadas.
- As atribuições de funções elegíveis das funções AD do Azure serão removidas, uma vez que os utilizadores deixarão de poder ativar funções privilegiadas.
- Quaisquer revisões de acesso em curso das funções de AD Azure terminarão e as definições de configuração de Gestão de Identidade Privilegiada serão removidas.
- A Gestão de Identidade Privilegiada deixará de enviar e-mails sobre alterações na atribuição de funções.

## <a name="next-steps"></a>Próximos passos

- [Implementar o Privileged Identity Management](pim-deployment-plan.md)
- [Começar a utilizar o Privileged Identity Management](pim-getting-started.md)
- [Funções que não pode gerir na Gestão de Identidade Privilegiada](pim-roles.md)

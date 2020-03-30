---
title: Requisitos de licença para utilização de Gestão de Identidade Privilegiada - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Descreve os requisitos de licenciamento para a utilização da Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932323"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisitos de licença para usar Gestão de Identidade Privilegiada

Para utilizar o Azure Ative Directory (Azure AD) Privileged Identity Management (PIM), um diretório deve ter uma licença válida. Além disso, as licenças devem ser atribuídas aos administradores e utilizadores relevantes. Este artigo descreve os requisitos de licença para usar a Gestão de Identidade Privilegiada.

## <a name="valid-licenses"></a>Licenças válidas

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Quantas licenças tem de ter?

Certifique-se de que o seu diretório tem pelo menos tantas licenças Azure AD Premium P2 como tem colaboradores que irão realizar as seguintes tarefas:

- Utilizadores atribuídos como elegíveis para funções Da D.A. Azure geridas com PIM
- Utilizadores capazes de aprovar ou rejeitar pedidos de ativação na PIM
- Utilizadores atribuídos a uma função de recurso Azure com atribuições justas ou diretas (baseadas no tempo)  
- Utilizadores atribuídos a uma revisão de acesso
- Utilizadores que realizam avaliações de acesso

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para utilizadores com as funções de Administrador Global ou Administrador de Papel Privilegiado que configuram pim, configuram políticas, recebem alertas e configuram avaliações de acesso.

Para mais informações sobre licenças, consulte [Atribuir ou remover licenças utilizando o portal de Diretório Ativo Azure](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns cenários de licença de exemplo para ajudá-lo a determinar o número de licenças que deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| O Woodgrove Bank tem 10 administradores para diferentes departamentos e 2 Administradores Globais que configuram e gerem a PIM. Tornam elegíveis cinco administradores. | Cinco licenças para os administradores elegíveis | 5 |
| O Instituto de Design Gráfico tem 25 administradores dos quais 14 são geridos através da PIM. A ativação de funções requer aprovação e há três utilizadores diferentes na organização que podem aprovar ativações. | 14 licenças para as funções elegíveis + três aprovadores | 17 |
| Contoso tem 50 administradores dos quais 42 são geridos através da PIM. A ativação de funções requer aprovação e há cinco utilizadores diferentes na organização que podem aprovar ativações. Contoso também faz revisões mensais de utilizadores atribuídos a funções de administrador e revisores são os gestores dos utilizadores dos quais seis não estão em funções de administrador geridos pela PIM. | 42 licenças para as funções elegíveis + cinco aprovadores + seis revisores | 53 |

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se expirar uma licença Azure AD Premium P2, EMS E5 ou licença experimental, as funcionalidades de Gestão de Identidade Privilegiada deixarão de estar disponíveis no seu diretório:

- As atribuições permanentes às funções da AD Azure não serão afetadas.
- O serviço de Gestão de Identidade Privilegiada no portal Azure, bem como as interfaces Graph API cmdlets e PowerShell de Gestão de Identidade Privilegiada, deixarão de estar disponíveis para os utilizadores ativarem funções privilegiadas, gerirem acessoprivilegiado ou realizarem avaliações de acesso de papéis privilegiados.
- As atribuições de funções elegíveis das funções Azure AD serão removidas, uma vez que os utilizadores deixarão de poder ativar funções privilegiadas.
- Quaisquer avaliações de acesso em curso das funções da AD Azure terminarão e as configurações de configuração de Gestão de Identidade Privilegiada serão removidas.
- A Gestão de Identidade Privilegiada deixará de enviar e-mails sobre alterações de atribuição de papéis.

## <a name="next-steps"></a>Passos seguintes

- [Implementar o Privileged Identity Management](pim-deployment-plan.md)
- [Começar a utilizar o Privileged Identity Management](pim-getting-started.md)
- [Funções que não pode gerir na Gestão de Identidade Privilegiada](pim-roles.md)

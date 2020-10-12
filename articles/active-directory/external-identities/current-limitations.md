---
title: Limitações da colaboração B2B - Azure Ative Directory Microsoft Docs
description: Limitações atuais para a colaboração B2B do Diretório Ativo Azure
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 127f05298483dcf155000c2eda8f590fc069a1e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909566"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações da colaboração Azure AD B2B
A colaboração B2B do Azure Ative Directory (Azure AD) está atualmente sujeita às limitações descritas neste artigo.

## <a name="possible-double-multi-factor-authentication"></a>Possível autenticação dupla de vários fatores
Com o Azure AD B2B, pode impor a autenticação de vários fatores na organização de recursos (a organização convidativa). As razões desta abordagem são detalhadas no [Acesso Condicional para utilizadores de colaboração B2B](conditional-access.md). Se um parceiro já tiver a autenticação multi-factor configurada e aplicada, os seus utilizadores poderão ter de realizar a autenticação uma vez na sua organização doméstica e depois novamente na sua.

## <a name="instant-on"></a>Instantaneamente
Nos fluxos de colaboração B2B, adicionamos os utilizadores ao diretório e atualizamo-los dinamicamente durante a redenção de convites, atribuição de aplicações, e assim por diante. As atualizações e as gravações normalmente acontecem num só diretório e devem ser replicadas em todos os casos. A replicação é concluída assim que todas as instâncias forem atualizadas. Por vezes, quando o objeto é escrito ou atualizado num caso e a chamada para recuperar este objeto é para outra instância, podem ocorrer latências de replicação. Se isso acontecer, refresque ou retentou ajudar. Se está a escrever uma app usando a nossa API, então as retração com algum back-off são uma boa prática defensiva para aliviar esta questão.

## <a name="azure-ad-directories"></a>Diretórios AD de Azure
O Azure AD B2B está sujeito aos limites do diretório de serviçoS Azure AD. Para obter detalhes sobre o número de diretórios que um utilizador pode criar e o número de diretórios a que um utilizador ou utilizador convidado pode pertencer, consulte [os limites e restrições de serviço AD AZure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Clouds nacionais
[Nuvens nacionais](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) são casos fisicamente isolados de Azure. A colaboração B2B não é suportada através das fronteiras nacionais da nuvem. Por exemplo, se o seu inquilino Azure estiver na nuvem global e pública, não pode convidar um utilizador cuja conta está numa nuvem nacional. Para colaborar com o utilizador, peça-lhes outro endereço de e-mail ou crie uma conta de utilizador membro para eles no seu diretório.

## <a name="azure-us-government-clouds"></a>Nuvens do Governo dos EUA Azure
Dentro da nuvem do Governo dos EUA, a colaboração B2B é apoiada entre inquilinos que estão dentro da nuvem do Governo dos EUA e que ambos apoiam a colaboração B2B. Os inquilinos do Governo dos EUA que apoiam a colaboração B2B também podem colaborar com utilizadores sociais usando contas da Microsoft ou do Google. Se convidar um utilizador para fora destes grupos (por exemplo, se o utilizador estiver num inquilino que não faça parte da nuvem do Governo dos EUA ou ainda não apoiar a colaboração B2B), o convite falhará ou o utilizador não poderá resgatar o convite. Para mais informações sobre outras limitações, consulte [as Variações Azure Ative Directory Premium P1 e P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Como posso saber se a colaboração B2B está disponível no meu inquilino do Governo Azure US?
Para saber se o seu inquilino em nuvem do Governo dos EUA Azure apoia a colaboração B2B, faça o seguinte:

1. Num browser, aceda ao seguinte URL, substituindo o nome do seu inquilino pelo * &lt; nome &gt; de inquilino:*

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Encontre `"tenant_region_scope"` na resposta JSON:

   - Se `"tenant_region_scope":"USGOV”` aparecer, b2B é suportado.
   - Se `"tenant_region_scope":"USG"` aparecer, o B2B não é suportado.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Convites de colaboração do delegado B2B](delegate-invitations.md)

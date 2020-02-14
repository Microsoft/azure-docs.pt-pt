---
title: Limitações da colaboração B2B - Diretório Ativo Azure / Microsoft Docs
description: Limitações atuais para a colaboração Azure Ative Directory B2B
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
ms.openlocfilehash: 1717897261404a2ab8df723c280c9be6a2dacea4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77196138"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações da colaboração Azure AD B2B
A colaboração do Azure Ative Directory (Azure AD) B2B está atualmente sujeita às limitações descritas neste artigo.

## <a name="possible-double-multi-factor-authentication"></a>Possível autenticação dupla de vários fatores
Com o Azure AD B2B, pode impor a autenticação de vários fatores na organização de recursos (a organização convidativa). As razões desta abordagem são detalhadas no Acesso Condicional aos utilizadores de [colaboração B2B.](conditional-access.md) Se um parceiro já tiver a autenticação multifactor configurada e executada, os seus utilizadores poderão ter de realizar a autenticação uma vez na sua organização doméstica e depois novamente na sua.

## <a name="instant-on"></a>Instant-on
Nos fluxos de colaboração B2B, adicionamos os utilizadores ao diretório e atualizamo-los dinamicamente durante o resgate de convites, atribuição de apps, e assim por diante. As atualizações e os escritos costumam acontecer num único exemplo de diretório e devem ser replicados em todos os casos. A replicação é concluída assim que todas as instâncias forem atualizadas. Por vezes, quando o objeto é escrito ou atualizado num caso e a chamada para recuperar este objeto é para outro caso, as tardios de replicação podem ocorrer. Se isso acontecer, refresque ou tente ajudar. Se está a escrever uma aplicação usando a nossa API, então a tentativa de retenção com algum back-off é uma boa prática defensiva para aliviar esta questão.

## <a name="azure-ad-directories"></a>Diretórios da AD Azure
O Azure AD B2B está sujeito aos limites de diretório de serviço da Azure AD. Para mais detalhes sobre o número de diretórios que um utilizador pode criar e o número de diretórios a que um utilizador ou utilizador convidado pode pertencer, consulte [os limites e restrições de serviço da Azure AD.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)

## <a name="national-clouds"></a>Nuvens nacionais
[Nuvens nacionais](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) são casos fisicamente isolados de Azure. A colaboração B2B não é apoiada através dos limites nacionais da nuvem. Por exemplo, se o seu inquilino Azure está na nuvem pública e global, não pode convidar um utilizador cuja conta está numa nuvem nacional. Para colaborar com o utilizador, peça-lhe outro endereço de e-mail ou crie uma conta de utilizador membro para eles no seu diretório.

## <a name="azure-us-government-clouds"></a>Nuvens do Governo dos EUA azure
Dentro da nuvem do Governo dos EUA, a colaboração B2B é atualmente apoiada apenas entre inquilinos que estão tanto dentro da nuvem do Governo dos EUA azure como que ambos apoiam a colaboração B2B. Se convidar um utilizador num inquilino que não faça parte da nuvem do Governo dos EUA ou que ainda não apoie a colaboração B2B, o convite falhará ou o utilizador não poderá resgatar o convite. Para mais detalhes sobre outras limitações, consulte [O Diretório Ativo Azure Premium P1 e As Variações P2.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Como posso saber se a colaboração B2B está disponível no meu inquilino do Governo dos EUA?
Para saber se o seu inquilino de nuvem azure us apoia a colaboração B2B, faça o seguinte:

1. Num browser, vá ao seguinte URL, substituindo o nome do seu inquilino por *&lt;nome de inquilino&gt;:*

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Encontre `"tenant_region_scope"` na resposta da JSON:

   - Se `"tenant_region_scope":"USGOV”` aparecer, o B2B é apoiado.
   - Se `"tenant_region_scope":"USG"` aparecer, o B2B não é suportado.
 

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Convites de colaboração B2B delegados](delegate-invitations.md)


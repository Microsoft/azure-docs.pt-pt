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
ms.openlocfilehash: b45277c89193c51f70836bcef8a21636fc9c7973
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
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

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Convites de colaboração B2B delegados](delegate-invitations.md)


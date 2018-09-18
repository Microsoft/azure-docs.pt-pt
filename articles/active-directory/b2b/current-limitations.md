---
title: Limitações de colaboração do Azure Active Directory B2B | Documentos da Microsoft
description: Limitações atuais para a colaboração do Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 4e997e33eaccc6938209937d3157498db8bf4781
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982345"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações da colaboração B2B do Azure AD
Colaboração do Azure Active Directory (Azure AD) B2B está sujeito às limitações descritas neste artigo.

## <a name="possible-double-multi-factor-authentication"></a>Autenticação multifator dupla possível
Com o Azure AD B2B, pode impor autenticação multifator na organização de recursos (a organização de convite). Os motivos para essa abordagem são detalhados no [acesso condicional para utilizadores de colaboração B2B](conditional-access.md). Se um parceiro já tiver a autenticação multifator, configurar e impostas, seus usuários poderão ter de efetuar a autenticação uma vez na sua organização da página principal e, em seguida, novamente no mesmo seu.

## <a name="instant-on"></a>Instant-on
Os fluxos de colaboração do B2B, podemos adicionar utilizadores ao diretório e atualizá-los dinamicamente durante o resgate de convite, atribuição de aplicações e assim por diante. As atualizações e as gravações normalmente acontecem na instância de um diretório e têm de ser replicadas em todas as instâncias. Concluída a replicação depois de todas as instâncias são atualizadas. Por vezes, quando o objeto é escrito ou atualizado numa instância e a chamada para recuperar este objeto é para outra instância, podem ocorrer latências de replicação. Se isto acontecer, atualize ou Repita para ajudar. Se estiver escrevendo uma aplicação com a nossa API, em seguida, é repetida com um término é uma prática de bom e defensiva para diminuir este problema.

## <a name="azure-ad-directories"></a>Diretórios do Azure AD
O Azure AD B2B está sujeito a do Azure AD limites de serviço diretório. Para obter detalhes sobre o número de diretórios, pode criar um utilizador e o número de diretórios para que um utilizador ou o utilizador convidado pode pertencer, veja [restrições e limites do serviço Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Delegar convites de colaboração B2B](delegate-invitations.md)


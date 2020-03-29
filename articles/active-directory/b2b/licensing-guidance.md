---
title: Orientação de licenciamento de colaboração B2B - Azure Ative Directory [ Azure Ative Directory ] Microsoft Docs
description: A colaboração Azure Ative Directory B2B não requer licenças Azure AD pagas, mas também pode receber funcionalidades pagas para utilizadores convidados B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74868852"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Orientação de licenciamento de colaboração azure Ative Directory B2B

Com a colaboração azure Ative Directory (Azure AD) business-to-business (B2B), pode convidar utilizadores externos (ou "utilizadores convidados") a utilizarem os seus serviços de AD Azure pagos. Algumas funcionalidades são gratuitas, mas para quaisquer funcionalidades pagas do Azure AD, pode convidar até cinco utilizadores convidados para cada licença de edição da Azure AD que possui para um empregado ou utilizador não convidado no seu inquilino.

> [!NOTE]
> Consulte [os preços do Diretório Ativo azure](https://azure.microsoft.com/pricing/details/active-directory/) para obter detalhes sobre os preços da AD Azure e as funcionalidades de colaboração B2B.

O licenciamento do utilizador convidado B2B é automaticamente calculado e reportado com base na razão 1:5. 

Além disso, os utilizadores podem utilizar funcionalidades gratuitas do Azure AD sem requisitos adicionais de licenciamento. Os utilizadores de hóspedes têm acesso a funcionalidades gratuitas da Azure AD mesmo que não tenha licenças Azure AD pagas. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemplos: Calcular as licenças dos utilizadores de hóspedes
Assim que determinar quantos utilizadores de hóspedes precisam de aceder aos seus serviços ad ad saqueados pagos, certifique-se de que tem licenças pagas a Azure AD suficientes para cobrir os utilizadores convidados na proporção de 1:5 exigida. Eis alguns exemplos:

- Pretende convidar 100 utilizadores convidados para as suas aplicações ou serviços Da AD Azure e fornecer gestão e provisionamento de acesso. Para 50 desses utilizadores, também pretende requerer MFA e Acesso Condicional, pelo que para essas funcionalidades necessitará de 10 licenças Azure AD Premium P1. Se planeia utilizar funcionalidades de Proteção de Identidade junto dos seus utilizadores convidados, precisará de licenças Azure AD Premium P2 na mesma proporção de 1:5 para cobrir os utilizadores convidados.
- Você quer convidar 60 utilizadores convidados que todos necessitam de MFA, por isso você deve ter pelo menos 12 licenças Azure AD Premium P1. Tem 10 colaboradores com licenças Azure AD Premium P1, o que permitiria até 50 utilizadores convidados ao abrigo do rácio de licenciamento 1:5. Terá de adquirir duas licenças Premium P1 adicionais para cobrir 10 utilizadores adicionais.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes recursos sobre a colaboração Azure AD B2B:

* [Preços do Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/)
* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)

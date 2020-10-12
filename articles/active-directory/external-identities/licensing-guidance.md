---
title: Orientação de licenciamento de colaboração B2B - Azure Ative Directory / Microsoft Docs
description: A colaboração do Azure Ative Directory B2B não requer licenças Azure AD pagas, mas também pode receber funcionalidades pagas para utilizadores de hóspedes B2B
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909303"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Orientação de licenciamento de colaboração do Azure Ative Directory B2B

Com a colaboração business-to-business (B2B) do Azure Ative Directory (Azure AD), pode convidar utilizadores externos (ou "utilizadores convidados") a utilizarem os seus serviços AD Azure pagos. Algumas funcionalidades são gratuitas, mas para quaisquer funcionalidades pagas da Azure AD, pode convidar até cinco utilizadores convidados para cada licença de edição AD Azure que possui para um empregado ou um utilizador não convidado no seu inquilino.

> [!NOTE]
> Consulte os [preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/) para obter detalhes sobre os preços da AZure AD e funcionalidades de colaboração B2B.

O licenciamento do utilizador de hóspedes B2B é automaticamente calculado e reportado com base na relação 1:5. 

Além disso, os utilizadores convidados podem usar funcionalidades AZure AD gratuitas sem requisitos adicionais de licenciamento. Os utilizadores de hóspedes têm acesso a funcionalidades Azure AD gratuitas, mesmo que não tenha licenças AZure AD pagas. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemplos: Calcular as licenças de utilizador convidado
Assim que determinar quantos utilizadores de hóspedes precisam de aceder aos seus serviços Azure AD pagos, certifique-se de que tem licenças pagas suficientes para cobrir os utilizadores convidados na relação de 1:5 exigida. Eis alguns exemplos:

- Você deseja convidar 100 utilizadores convidados para suas aplicações ou serviços AD Azure e fornecer gestão de acesso e fornecimento. Para 50 desses utilizadores convidados, também pretende necessitar de MFA e Acesso Condicional, pelo que para essas funcionalidades necessitará de 10 licenças Azure AD Premium P1. Se planeia utilizar funcionalidades de Proteção de Identidade com os seus utilizadores convidados, necessitará de licenças Azure AD Premium P2 no mesmo rácio de 1:5 para cobrir os utilizadores convidados.
- Você quer convidar 60 utilizadores convidados que todos precisam de MFA, por isso você deve ter pelo menos 12 licenças Azure AD Premium P1. Tem 10 colaboradores com licençaS Azure AD Premium P1, o que permitiria até 50 utilizadores convidados ao abrigo do rácio de licenciamento de 1:5. Você precisará comprar duas licenças Premium P1 adicionais para cobrir 10 utilizadores convidados adicionais.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes recursos na colaboração Azure AD B2B:

* [Preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)

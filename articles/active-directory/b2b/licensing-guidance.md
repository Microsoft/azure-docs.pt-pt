---
title: Colaboração B2B do Azure Active Directory - documentação de orientação de licenciamento | Documentos da Microsoft
description: O Azure Active Directory B2B não necessita de colaboração pago licenças do Azure AD, mas pode também obter funcionalidades pagas para utilizadores convidados B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fa01a6bf522061e54e9622cb9201f81c699a8ec
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668457"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Orientação de licenciamento de colaboração do Azure Active Directory B2B

Com a colaboração de empresa-empresa (B2B) do Azure Active Directory (Azure AD), pode convidar utilizadores externos (ou "utilizadores") para utilizar seu paga serviços do Azure AD. Para cada pagas licença do Azure AD que são atribuídas a um utilizador, pode convidar até cinco utilizadores convidados sob a permissão de utilizador externo.

Licenciamento de utilizadores de convidados B2B é automaticamente calculada e reportada com base no rácio de 1:5. Atualmente, não é possível atribuir B2B licenças de utilizador convidado diretamente para os utilizadores convidados.

Além disso, o convidado, os utilizadores podem utilizar gratuito funcionalidades do Azure AD sem necessidade de licenciamento adicionais. Os utilizadores convidados têm acesso gratuito a funcionalidades do Azure AD, mesmo se não tiver nenhum pago licenças do Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemplos: Cálculo de licenças de utilizador convidado
Depois de determinar o número de utilizadores convidados precisa acessar seu paga serviços do Azure AD, verifique se tem suficiente paga do Azure AD licenças para abranger os utilizadores convidados na proporção de 1:5 necessária. Eis alguns exemplos:

- Pretende convidar 100 utilizadores para as suas aplicações do Azure AD ou serviços, e que pretende atribuir a gestão de acesso e aprovisionamento para todos os utilizadores convidados. Também queira exigir a MFA e o acesso condicional para 50 desses usuários de convidado. Para cobrir essa combinação, precisará 10 licenças do Azure AD básico e 10 licenças do Azure AD Premium P1. Se planeia utilizar funcionalidades de proteção de identidade com os seus utilizadores convidados, terá de licenças do Azure AD Premium P2 na mesma proporção de 1:5 para abranger todos os utilizadores convidados.
- Deseja convidar 60 utilizadores que exigem a MFA, pelo que tem de ter, pelo menos, 12 licenças do Azure AD Premium P1. Tem 10 funcionários com licenças do Azure AD Premium P1, que permitiria que o máximo de 50 utilizadores convidados em 1:5 rácio de licenciamento. Terá de comprar licenças de Premium P1 adicionais duas para cobrir a 10 utilizadores adicionais.

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes recursos sobre a colaboração B2B do Azure AD:

* [Os preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)

---
title: Autenticação multi-factor Azure AD para a sua organização - Azure Ative Directory
description: Conheça as funcionalidades disponíveis da Autenticação Multi-Factor AD Azure para a sua organização com base no seu modelo de licença
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836747"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>Visão geral da autenticação multi-factor Azure AD para a sua organização

Existem várias formas de ativar a autenticação multi-factor Azure AD para os seus utilizadores do Azure Ative Directory (AD) com base nas licenças que a sua organização possui. 

![Investigue sinais e aplique MFA se necessário](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Com base nos nossos estudos, a sua conta tem menos 99,9% de probabilidade suscetível de ser comprometida se utilizar a autenticação multi-factor (MFA).

Então, como é que a sua organização recorre à MFA mesmo de graça, antes de se tornar uma estatística?

## <a name="free-option"></a>Opção gratuita

Os clientes que estão a utilizar os benefícios gratuitos do Azure AD podem utilizar falhas de segurança para permitir a autenticação de [vários](../fundamentals/concept-fundamentals-security-defaults.md) fatores no seu ambiente.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 ou E5

Para clientes com Microsoft 365, existem duas opções:

* A autenticação multi-factor Azure AD está ativada ou desativada para todos os utilizadores, para todos os eventos de inscrição. Não existe capacidade de permitir apenas a autenticação de vários fatores para um subconjunto de utilizadores, ou apenas em determinados cenários. A gestão é através do portal do Office 365. 
* Para uma melhor experiência do utilizador, atualize para Azure AD Premium P1 ou P2 e utilize acesso condicional. Para obter mais informações, consulte recursos seguros da Microsoft 365 com autenticação multi-factor.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Para clientes com Azure AD Premium P1 ou licenças similares que incluem esta funcionalidade como Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3: 

Utilize [o Azure AD Conditional Acesso](../authentication/tutorial-enable-azure-mfa.md) a utilizadores solicitados para autenticação de vários fatores durante determinados cenários ou eventos que se adaptem aos requisitos do seu negócio.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Para clientes com Azure AD Premium P2 ou licenças similares que incluem esta funcionalidade como Mobilidade Empresarial + Segurança E5 ou Microsoft 365 E5: 

Proporciona a posição de segurança mais forte e uma melhor experiência do utilizador. Adiciona [acesso condicional baseado em risco](../conditional-access/howto-conditional-access-policy-risk.md) às funcionalidades Azure AD Premium P1 que se adaptam aos padrões do utilizador e minimizam as solicitações de autenticação de vários fatores.

## <a name="authentication-methods"></a>Métodos de autenticação

| Método | Predefinições de segurança | Todos os outros métodos |
| --- | --- | --- |
| Notificação através de aplicação móvel | X | X |
| Código de verificação a partir de aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Chamada para telefone |   | X |

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o tutorial para [garantir eventos de entrada de utilizador com autenticação multi-factor Ad Azure](../authentication/tutorial-enable-azure-mfa.md).

Para obter mais informações sobre o licenciamento, consulte [funcionalidades e licenças para autenticação multi-factor Azure AD](../authentication/concept-mfa-licensing.md).

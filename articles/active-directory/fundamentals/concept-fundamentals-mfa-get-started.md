---
title: Autenticação Azure Multi-Factor para a sua organização - Diretório Ativo Azure
description: Conheça as funcionalidades disponíveis da Autenticação Multi-Factor Azure para a sua organização com base no seu modelo de licença
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530398"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Visão geral da autenticação multi-factor Azure para a sua organização

Existem várias formas de ativar a autenticação de multi-factores Azure para os seus utilizadores de Diretório Ativo Azure (AD) com base nas licenças que a sua organização detém. 

![Investigar sinais e impor MFA se necessário](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Com base nos nossos estudos, a sua conta tem mais de 99,9% menos probabilidade de ser comprometida se utilizar a autenticação multifactor (MFA).

Então, como é que a sua organização liga o MFA mesmo de graça, antes de se tornar uma estatística?

## <a name="free-option"></a>Opção gratuita

Os clientes que utilizam os benefícios gratuitos da Azure AD podem utilizar falhas de [segurança](../fundamentals/concept-fundamentals-security-defaults.md) para permitir a autenticação de vários fatores no seu ambiente.

## <a name="office-365-business-premium-e3-or-e5"></a>Escritório 365 Business Premium, E3 ou E5

Para clientes com escritório 365, existem duas opções:

* A autenticação de multi-factores Azure está ativada ou desativada para todos os utilizadores, para todos os eventos de entrada. Não existe capacidade de permitir apenas a autenticação de vários fatores para um subconjunto de utilizadores, ou apenas em determinados cenários. A gerência é através do portal do Office 365. 
* Para uma melhor experiência do utilizador, atualize para O Azure AD Premium P1 ou P2 e utilize o Acesso Condicional. Para mais informações, consulte os recursos do Office 365 com autenticação multifactor.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Para clientes com Azure AD Premium P1 ou licenças similares que incluam esta funcionalidade como Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3: 

Utilize o [Acesso Condicional Azure AD](../conditional-access/overview.md) para solicitar aos utilizadores a autenticação de vários fatores durante determinados cenários ou eventos para se adaptarem aos seus requisitos de negócio.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Para clientes com Azure AD Premium P2 ou licenças similares que incluam esta funcionalidade como Enterprise Mobility + Security E5 ou Microsoft 365 E5: 

Proporciona a posição de segurança mais forte e a melhoria da experiência do utilizador. Adiciona [acesso condicional baseado no risco](../conditional-access/howto-conditional-access-policy-risk.md) às funcionalidades Azure AD Premium P1 que se adaptam aos padrões do utilizador e minimizam os pedidos de autenticação de vários fatores.

## <a name="authentication-methods"></a>Métodos de autenticação

|   | Predefinições de segurança | Todos os outros métodos |
| --- | --- | --- |
| Notificação através de aplicação móvel | X | X |
| Código de verificação de aplicativo móvel ou ficha de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Chamada para telefone |   | X |

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o tutorial para [garantir eventos de inscrição do utilizador com autenticação multi-factor Azure](../authentication/tutorial-enable-azure-mfa.md).

Para obter mais informações sobre o licenciamento, consulte [Funcionalidades e licenças para autenticação multi-factor Azure](../authentication/concept-mfa-licensing.md).

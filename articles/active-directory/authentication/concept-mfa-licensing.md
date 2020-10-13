---
title: Versões de autenticação multi-factor Azure e planos de consumo
description: Saiba mais sobre o cliente Azure Multi-factor Authentication e diferentes métodos e versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb0f46595be0aae68c9bcf15bcd4f087e8faaeba
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965221"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funcionalidades e licenças para autenticação multi-factor Azure

Para proteger as contas dos utilizadores na sua organização, deve ser utilizada a autenticação de vários fatores. Esta funcionalidade é especialmente importante para contas que tenham acesso privilegiado aos recursos. As funcionalidades básicas de autenticação multi-factor estão disponíveis para os administradores do Microsoft 365 e do Azure Ative Directory (Azure AD) sem custos adicionais. Se pretender atualizar as funcionalidades para os seus administradores ou estender a autenticação de vários fatores para o resto dos seus utilizadores, pode adquirir a autenticação multi-factor Azure de várias formas.

> [!IMPORTANT]
> Este artigo detalha as diferentes formas de Azure Multi-Factor Authentication poder ser licenciado e utilizado. Para obter detalhes específicos sobre preços e faturação, consulte a [página de preços de autenticação multi-factor Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da Autenticação Multi-Factor Azure

A Azure Multi-Factor Authentication pode ser usada e licenciada, de várias maneiras, dependendo das necessidades da sua organização. Pode já ter o direito de utilizar a autenticação multi-factor Azure, dependendo da licença Azure AD, EMS ou Microsoft 365 que tem atualmente. A tabela seguinte detalha as diferentes formas de obter a Autenticação Multi-Factor Azure e algumas das funcionalidades e casos de utilização para cada um.

| Se você é um utilizador de | Capacidades e casos de utilização |
| --- | --- |
| Microsoft 365 Business Premium e EMS ou Microsoft 365 E3 e E5 | EMS E3, Microsoft 365 E3 e Microsoft 365 Business Premium inclui Azure AD Premium P1. O EMS E5 ou o Microsoft 365 E5 inclui o Azure AD Premium P2. Pode utilizar as mesmas funcionalidades de Acesso Condicional nas seguintes secções para fornecer autenticação multi-factor aos utilizadores. |
| Azure AD Premium P1 | Pode utilizar [o Azure AD Conditional Access](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) para solicitar aos utilizadores a autenticação de vários fatores durante determinados cenários ou eventos que se adaptem aos requisitos do seu negócio. |
| Azure AD Premium P2 | Proporciona a posição de segurança mais forte e uma melhor experiência do utilizador. Adiciona [acesso condicional baseado em risco](../conditional-access/howto-conditional-access-policy-risk.md) às funcionalidades Azure AD Premium P1 que se adaptam aos padrões do utilizador e minimizam as solicitações de autenticação de vários fatores. |
| Todos os planos da Microsoft 365 | A autenticação multi-factor Azure pode ser [ativada por utilizador,](howto-mfa-userstates.md)ou ativada ou desativada para todos os utilizadores que utilizem [falhas de segurança.](../fundamentals/concept-fundamentals-security-defaults.md) A gestão da autenticação multi-factor Azure é através do portal Microsoft 365. Para uma melhor experiência do utilizador, atualize para Azure AD Premium P1 ou P2 e utilize acesso condicional. Para obter mais informações, consulte [recursos seguros da Microsoft 365 com autenticação multi-factor.](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication) |
| Azure Active Directory gratuito | Pode utilizar falhas de segurança para permitir a autenticação de [vários](../fundamentals/concept-fundamentals-security-defaults.md) fatores para todos os utilizadores. Não tem controlo granular de utilizadores ou cenários habilitados, mas fornece esse passo adicional de segurança.<br /> Mesmo quando os padrão de segurança não são usados para permitir a autenticação de vários fatores para todos, os utilizadores atribuídos a função *de Administrador Global AD AD Azure* podem ser configurados para usar a autenticação de vários fatores. Esta característica do nível livre garante que as contas de administrador crítico estão protegidas por autenticação de vários fatores. |

## <a name="feature-comparison-of-versions"></a>Comparação de recursos de versões

A tabela seguinte fornece uma lista das funcionalidades que estão disponíveis nas várias versões da Autenticação Multi-Factor Azure. Planeie as suas necessidades para garantir a autenticação do utilizador e, em seguida, determine qual a abordagem que satisfaz esses requisitos. Por exemplo, embora o Azure AD Free forneça falhas de segurança que forneçam autenticação multi-factor Azure, apenas a aplicação de autenticação móvel pode ser usada para o pedido de autenticação, não para uma chamada telefónica ou SMS. Esta abordagem pode ser uma limitação se não conseguir garantir que a aplicação de autenticação móvel está instalada no dispositivo pessoal de um utilizador.

| Funcionalidade | Azure AD Free - Incumprimentos de segurança | Azure AD Free - Administradores Globais AZURE AD | Aplicações do Microsoft 365 | Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| Proteja as contas de administradores de inquilinos da Azure com MFA | ● | ● *(Apenas contas de Administrador Global AD AD)* | ● | ● |
| Aplicativo móvel como segundo fator | ● | ● | ● | ● |
| Chamada telefónica como segundo fator | | ● | ● | ● |
| SMS como segundo fator | | ● | ● | ● |
| Controlo administrativo sobre os métodos de verificação | | ● | ● | ● |
| Alerta de fraudes | | | | ● |
| Relatórios do MFA | | | | ● |
| Saudações personalizadas para chamadas telefónicas | | | | ● |
| ID de chamada personalizada para chamadas telefónicas | | | | ● |
| IPs Fidedignos | | | | ● |
| Memorizar MFA para dispositivos fidedignos | | ● | ● | ● |
| MFA para aplicações no local | | | | ● |

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Comprar e ativar a autenticação multi-factor Azure

Para utilizar a autenticação multi-factor Azure, registe-se ou compre um nível AD Azure elegível. O Azure AD vem em quatro edições — Aplicações gratuitas, Microsoft 365, Premium P1 e Premium P2.

A edição Gratuita está incluída com uma subscrição do Azure. Consulte [a secção abaixo](#azure-ad-free-tier) para obter informações sobre como utilizar incumprimentos de segurança ou proteger contas com a função *de Administrador Global AD Azure.*

As edições Azure AD Premium estão disponíveis através do seu representante da Microsoft, do [Programa de Licenças de Volume Aberto](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)e do programa Cloud Solution [Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Os subscritores Azure e Microsoft 365 também podem comprar online O Azure Ative Directory Premium P1 e P2. [Inscreva-se](https://portal.office.com/Commerce/Catalog.aspx) para comprar.

Depois de ter adquirido o nível AD AD azure necessário, [planeie e implemente a autenticação multi-factor Azure](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Azure Ad Free Tier

Todos os utilizadores de um inquilino Azure AD Free podem utilizar a autenticação Azure Multi-Factor através da utilização de falhas de segurança. A aplicação de autenticação móvel é o único método que pode ser usado para autenticação multi-factor Azure quando utilizar padrão de segurança Azure AD Free.

* [Saiba mais sobre os incumprimentos de segurança da AZure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Ativar incumprimentos de segurança para utilizadores em Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se não pretender ativar a autenticação multi-factor Azure para todos os utilizadores, pode optar por proteger apenas as contas do utilizador com a função *de Administrador Global AD Azure.* Esta abordagem fornece indicações de autenticação adicionais para contas críticas do administrador. Ativa a autenticação multi-factor Azure de uma das seguintes formas, dependendo do tipo de conta que utiliza:

* Se utilizar uma Conta Microsoft, [registe-se para autenticação multi-factor](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Se não estiver a utilizar uma Conta Microsoft, [ligue a autenticação de vários fatores para um utilizador ou grupo em Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre os custos, consulte [os preços de autenticação multi-factor Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [O que é acesso condicional](../conditional-access/overview.md)


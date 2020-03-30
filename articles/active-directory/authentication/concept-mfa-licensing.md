---
title: Versões de autenticação multi-factor Azure e planos de consumo
description: Conheça o cliente de autenticação multifactor Azure e diferentes métodos e versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648007"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funcionalidades e licenças para autenticação multi-factor Azure

Para proteger as contas dos utilizadores na sua organização, deve ser utilizada a autenticação de vários fatores. Esta funcionalidade é especialmente importante para contas que têm acesso privilegiado aos recursos. As funcionalidades básicas de autenticação multifactor estão disponíveis para os administradores do Office 365 e do Azure Ative Directory (Azure AD) sem custos adicionais. Se pretender atualizar as funcionalidades para os seus administradores ou estender a autenticação de vários fatores aos restantes utilizadores, pode adquirir a Autenticação Multifactor Azure de várias formas.

> [!IMPORTANT]
> Este artigo detalha as diferentes formas de a autenticação de Multi-Factor si pode ser licenciada e utilizada. Para obter detalhes específicos sobre preços e faturação, consulte a página de preços de [autenticação multi-factor Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da Autenticação Multi-Factor Azure

A autenticação de multi-factores Azure pode ser usada e licenciada, de várias maneiras, dependendo das necessidades da sua organização. Pode já ter o direito de utilizar a Autenticação Multi-Factor Azure, dependendo da licença Azure AD, Office 365, EMS ou Microsoft 365 que tem atualmente. A tabela seguinte detalha as diferentes formas de obter a Autenticação Multi-Factor Azure e algumas das funcionalidades e utilização de casos para cada um.

| Se é um utilizador de | Capacidades e casos de utilização |
| --- | --- |
| EMS ou Microsoft 365 E3 e E5 | Ems E3 ou Microsoft 365 E3 (que inclui EMS e Office 365), inclui Azure AD Premium P1. Ems E5 ou Microsoft 365 E5 inclui Azure AD Premium P2. Pode utilizar as mesmas funcionalidades de Acesso Condicional observadas nas seguintes secções para fornecer a autenticação de vários fatores aos utilizadores. |
| Azure AD Premium P1 | Pode utilizar o [Azure AD Conditional Access](../conditional-access/overview.md) para solicitar aos utilizadores a autenticação de vários fatores durante determinados cenários ou eventos para se adaptarem aos seus requisitos de negócio. |
| Azure AD Premium P2 | Proporciona a posição de segurança mais forte e a melhoria da experiência do utilizador. Adiciona [acesso condicional baseado no risco](../conditional-access/howto-conditional-access-policy-risk.md) às funcionalidades Azure AD Premium P1 que se adaptam aos padrões do utilizador e minimizam os pedidos de autenticação de vários fatores. |
| Escritório 365 Business Premium, E3 ou E5 | A autenticação de multi-factores Azure está ativada ou desativada para todos os utilizadores, para todos os eventos de entrada. Não existe capacidade de permitir apenas a autenticação de vários fatores para um subconjunto de utilizadores, ou apenas em determinados cenários. A gerência é através do portal do Office 365. Para uma melhor experiência do utilizador, atualize para O Azure AD Premium P1 ou P2 e utilize o Acesso Condicional. Para mais informações, consulte os [recursos do Office 365 com autenticação multifactor.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6) |
| Azure Active Directory gratuito | Pode utilizar falhas de [segurança](../fundamentals/concept-fundamentals-security-defaults.md) para permitir a autenticação de vários fatores para todos os utilizadores, sempre que for feito um pedido de autenticação. Não tem controlo granular de utilizadores ou cenários habilitados, mas fornece esse passo de segurança adicional.<br /> Mesmo quando os incumprimentos de segurança não são usados para permitir a autenticação de vários fatores para todos, os utilizadores designados a função *de Administrador Global Azure AD* podem ser configurados para usar a autenticação de vários fatores. Esta funcionalidade do nível livre garante que as contas críticas do administrador estão protegidas por autenticação de vários fatores. |

## <a name="feature-comparison-of-versions"></a>Comparação de funcionalidades de versões

A tabela seguinte apresenta uma lista das funcionalidades que estão disponíveis nas várias versões da Autenticação Multi-Factor Azure. Planeie as suas necessidades para garantir a autenticação do utilizador e, em seguida, determine qual a abordagem que satisfaz esses requisitos. Por exemplo, embora o Azure AD Free forneça falhas de segurança que fornecem autenticação Multi-Factor Azure, apenas a aplicação de autenticação móvel pode ser usada para o pedido de autenticação, e não para uma chamada telefónica ou SMS. Esta abordagem pode ser uma limitação se não conseguir garantir que a aplicação de autenticação móvel está instalada no dispositivo pessoal de um utilizador.

| Funcionalidade | Azure AD Free - Falhas de segurança | Azure AD Free - Administradores Globais da Azure AD | Escritório 365 Business Premium, E3 ou E5 | Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| Proteja as contas de administrador adinaminária da Azure AD com mfa | ● | ● (apenas contas de*Administrador Global da AD)* | ● | ● |
| Aplicativo móvel como segundo fator | ● | ● | ● | ● |
| Chamada telefónica como segundo fator | | ● | ● | ● |
| SMS como segundo fator | | ● | ● | ● |
| Controlo administrativo sobre métodos de verificação | | ● | ● | ● |
| Alerta de fraudes | | | | ● |
| Relatórios do MFA | | | | ● |
| Saudações personalizadas para chamadas telefónicas | | | | ● |
| ID de chamada personalizada para chamadas telefónicas | | | | ● |
| IPs Fidedignos | | | | ● |
| Memorizar MFA para dispositivos fidedignos | | ● | ● | ● |
| MFA para aplicações no local | | | | ● |

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefónica deixaram de estar disponíveis para autenticação multi-factor e para os utilizadores de reset de senha de autosserviço Azure em inquilinos de Anúncio Sem AD/teste Azure. As mensagens SMS não são afetadas por esta mudança. As chamadas telefónicas continuam a estar disponíveis para os utilizadores em inquilinos Ou 1 Ou P2 Premium Azure Ou P2 ou usos ou Office 365 Business Premium, E3 ou E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Comprar e ativar a autenticação de vários fatores do Azure

Para utilizar a Autenticação Multi-Factor Azure, registe-se ou adquira um nível AD Azure elegível. A Azure AD vem em quatro edições — Free, Office 365 apps edition (para o Office 365 Business Premium E3, ou clientes E5), Premium P1 e Premium P2.

A edição Gratuita está incluída com uma subscrição Azure. Consulte a [secção abaixo](#azure-ad-free-tier) para obter informações sobre como utilizar incumprimentos de segurança ou proteger contas com a função de Administrador Global *Da AD Azure.*

As edições Azure AD Premium estão disponíveis através do seu representante da Microsoft, do Programa de Licença seletiva de [Volume Aberto](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)e do programa Cloud Solution [Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Os subscritores do Azure e do Office 365 também podem comprar online o Azure Ative Directory Premium P1 e P2. [Inscreva-se](https://portal.office.com/Commerce/Catalog.aspx) para comprar.

> [!IMPORTANT]
> O licenciamento baseado no consumo já não está disponível para novos clientes a partir de 1 de setembro de 2018. Os clientes existentes que utilizam o modelo baseado no consumo podem continuar a utilizar por utilizador ativado ou por faturação de autenticação.

Depois de ter adquirido o nível AD Azure necessário, [planeie e implemente a autenticação Azure Multi-Factor](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Nível Azure AD Free

Todos os utilizadores de um inquilino Free Azure AD podem utilizar a autenticação Azure Multi-Factor através da utilização de incumprimentos de segurança. Estas falhas de segurança permitem a autenticação Azure Multi-Factor para todos os utilizadores, sempre que iniciarem a sua inscrição. A aplicação de autenticação móvel é o único método que pode ser usado para autenticação de multi-factores Azure quando utilizar falhas de segurança Free Azure AD.

* [Saiba mais sobre falhas de segurança da AD Azure](../fundamentals/concept-fundamentals-security-defaults.md)
* [Ativar falhas de segurança para utilizadores em Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Se não pretender ativar a Autenticação Multi-Factor Azure para todos os utilizadores e todos os eventos de entrada, pode optar por proteger apenas as contas dos utilizadores com a função de *Administrador Global Azure AD.* Esta abordagem fornece indicações adicionais de autenticação para contas de administrador críticos. Ativa a autenticação de multifactor estoque azure de uma das seguintes formas, dependendo do tipo de conta que utiliza:

* Se utilizar uma Conta Microsoft, [registe-se para autenticação de vários fatores.](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* Se não estiver a utilizar uma Conta Microsoft, ligue a [autenticação de vários fatores para um utilizador ou grupo em Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os custos, consulte o preço de [autenticação de multi-factors azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

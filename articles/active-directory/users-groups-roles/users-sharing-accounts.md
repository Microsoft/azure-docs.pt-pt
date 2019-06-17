---
title: Partilhar contas e credenciais - Azure Active Directory | Documentos da Microsoft
description: Descreve como o Azure Active Directory permite às organizações partilhar em segurança as contas de aplicações no local e serviços de cloud do consumidor.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 902696e35a2447b67dab74b73794b80ad5e26511
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083739"
---
# <a name="sharing-accounts-with-azure-ad"></a>Partilhar contas com o Azure AD
## <a name="overview"></a>Descrição geral
Por vezes, as organizações precisam de utilizar um único nome de utilizador e palavra-passe para várias pessoas, que normalmente ocorre em dois casos:

* Ao aceder a aplicações que requerem um início de sessão único e a palavra-passe para cada utilizador, se aplicações no local ou consumidor de serviços cloud (por exemplo, contas de mídia social corporativa).
* Durante a criação de ambientes de vários utilizadores. Poderá ter uma conta de local única que tem privilégios elevados e é utilizada para núcleos de atividades de configuração, administração e a recuperação. Por exemplo, o local "administrador global" conta do Office 365 ou a conta de raiz no Salesforce.

Tradicionalmente, estas contas são compartilhadas por distribuir as credenciais (nome de utilizador e palavra-passe) para os indivíduos de certos ou armazená-los num local compartilhado onde vários agentes fidedignos podem aceder aos mesmos.

O modelo tradicional de partilha tem várias desvantagens:

* Permitir o acesso a novos aplicativos exige que distribuir as credenciais para todos os utilizadores que precisa de acesso.
* Cada aplicação partilhadas pode exigir o seu próprio conjunto exclusivo de credenciais partilhadas, exigir que os usuários não se esqueça de vários conjuntos de credenciais. Quando os utilizadores tenham de memorizar credenciais muitos, aumenta o risco a que eles recorrer a práticas de risco. (por exemplo, escrever palavras-passe).
* É impossível dizer, quem tem acesso a uma aplicação.
* É impossível dizer, quem tem *acedidos* um aplicativo.
* Quando deseja remover o acesso a uma aplicação, terá de atualizar as credenciais e redistribuí-los para todos os utilizadores que precisa de acesso a essa aplicação.

## <a name="azure-active-directory-account-sharing"></a>Partilha e conta do Azure Active Directory
O Azure AD fornece uma nova abordagem para utilizar as contas partilhadas, que elimina essas desvantagens.

O administrador do Azure AD configura as aplicações que um utilizador pode aceder ao utilizar o painel de acesso e escolher o tipo de melhor de início de sessão único adequam para essa aplicação. Um desses tipos *baseado em palavra-passe de início de sessão único*, permite que o Azure AD atuar como um tipo de "Mediador" durante o processo de início de sessão para essa aplicação.

Os utilizadores iniciar sessão uma vez com suas contas institucionais. Esta conta é a mesma que utilizam regularmente para acessar sua área de trabalho ou e-mail. Podem detetar e aceder apenas os aplicativos que estão atribuídos. Com as contas partilhadas, esta lista de aplicativos pode incluir qualquer número de credenciais partilhadas. O utilizador final não precisa se lembrar ou anote as várias contas que talvez estejam usando.

As contas partilhadas não apenas aumentam de supervisão e aumentar a usabilidade, eles também melhoram a segurança. Os utilizadores com permissões para utilizar as credenciais não vir a palavra-passe partilhada, mas em vez disso, obtém permissões para utilizar a palavra-passe como parte de um fluxo de autenticação organizada. Além disso, alguns aplicativos de SSO de palavra-passe dão-lhe a opção de utilizar o Azure AD para periodicamente as senhas de rollover (atualização). O sistema utiliza as palavras-passe grandes e complexas, que aumenta a segurança da conta. O administrador pode conceder ou revogar o acesso facilmente a uma aplicação, sabe quem tem acesso à conta e quem acessou-lo no passado.

O Azure AD suporta as contas partilhadas para qualquer Enterprise Mobility Suite (EMS), Premium ou Basic licenciado a utilizadores, em todos os tipos de palavra-passe único início de sessão em aplicações. Pode partilhar contas de qualquer uma das milhares de aplicações previamente integradas na Galeria de aplicações e pode adicionar sua própria aplicação de autenticação de palavra-passe com [aplicações personalizadas de SSO](../manage-apps/configure-single-sign-on-portal.md).

As funcionalidades do Azure AD que ativar a partilha de conta incluem:

* [Palavra-passe início de sessão único](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Palavra-passe único início de sessão no agente
* [Atribuição de grupo](groups-self-service-management.md)
* Aplicações de palavra-passe personalizada
* [Dashboard de utilização de aplicação/reports](../active-directory-passwords-get-insights.md)
* Portais de acesso do utilizador final
* [Proxy de aplicações](../manage-apps/application-proxy.md)
* [Marketplace do Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Uma conta de partilha
Para utilizar o Azure AD para partilhar uma conta, terá de:

* Adicionar uma aplicação [Galeria de aplicações](https://azure.microsoft.com/marketplace/active-directory/) ou [aplicação personalizada](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Configurar a aplicação para a palavra-passe único início de sessão (SSO)
* Uso [atribuição baseada em grupo](groups-saasapps.md) e selecione a opção para introduzir uma credencial partilhada
* Opcional: em alguns aplicativos, como o Facebook, Twitter ou LinkedIn, pode ativar a opção para [automatizada de rollover de palavra-passe do Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/)

Pode também tornar a sua conta partilhada mais segura com multi-factor Authentication (MFA) (Saiba mais sobre [protegendo aplicativos com o Azure AD](../authentication/concept-mfa-whichversion.md)) e pode delegar a capacidade de gerir quem tem acesso para a aplicação utilizando [ Personalização do AD Azure](groups-self-service-management.md) gestão de grupo.

## <a name="related-articles"></a>Artigos relacionados
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Proteger aplicações com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gestão/SSAA de grupos self-service](groups-self-service-management.md)

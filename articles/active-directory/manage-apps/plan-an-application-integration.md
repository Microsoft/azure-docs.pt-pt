---
title: Começar a integrar o Azure AD com apps Microsoft Docs
description: Este artigo é um guia de arranque para integrar o Azure Ative Directory (AD) com aplicações no local e aplicações na nuvem.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89b16a8479f8975d101b8a4e26dcb1885d9730bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063385"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrando o Diretório Ativo Azure com candidaturas a serem iniciadas guia

Este tópico resume o processo de integração de aplicações com o Azure Ative Directory (AD). Cada uma das secções abaixo contém um breve resumo de um tópico mais detalhado para que possa identificar quais as partes deste guia inicial que são relevantes para si.

Para descarregar planos de implantação aprofundados, consulte [os próximos passos](#next-steps).

## <a name="take-inventory"></a>Faça o inventário
Antes de integrar aplicações com a Azure AD, é importante saber onde está e para onde quer ir.  As seguintes questões destinam-se a ajudá-lo a pensar no seu projeto de integração de aplicações Azure AD.

### <a name="application-inventory"></a>Inventário de aplicações
* Onde estão todas as suas candidaturas? Quem é o dono deles?
* Que tipo de autenticação exigem as suas aplicações?
* Quem precisa de acesso a que aplicações?
* Quer implementar uma nova aplicação?
  * Vai construí-lo internamente e implantá-lo num caso de computação Azure?
  * Utilizará um que esteja disponível na Galeria de Aplicações Do Azure?

### <a name="user-and-group-inventory"></a>Inventário de utilizador e grupo
* Onde residem as suas contas de utilizador?
  * Active Directory no local
  * Azure AD
  * Dentro de uma base de dados de aplicações separada que possui
  * Em pedidos não sancionados
  * Todos os anteriores
* Que permissões e atribuições de funções têm atualmente os utilizadores individuais? Precisa de rever o seu acesso ou tem a certeza de que o acesso ao utilizador e as atribuições de papéis são apropriados agora?
* Os grupos já estão estabelecidos no seu Diretório Ativo no local?
  * Como estão organizados os seus grupos?
  * Quem são os membros do grupo?
  * Que permissões/atribuições de funções têm os grupos atualmente?
* Terá de limpar as bases de dados do utilizador/grupo antes de se integrar?  (Esta é uma questão muito importante. Lixo dentro, lixo para fora.)

### <a name="access-management-inventory"></a>Inventário de gestão de acesso
* Como gere atualmente o acesso dos utilizadores às aplicações? Isso tem de mudar?  Já considerou outras formas de gerir o acesso, como por exemplo com o [RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Quem precisa de acesso ao quê?

Talvez não tenhas as respostas para todas estas perguntas, mas não faz mal.  Este guia pode ajudá-lo a responder a algumas dessas perguntas e a tomar algumas decisões informadas.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Encontre aplicações em nuvem não autorizadas com Cloud Discovery

Como mencionado acima, pode haver aplicações que não foram geridas pela sua organização até agora.  Como parte do processo de inventário, é possível encontrar aplicações em nuvem não autorizadas. Ver [Configurar a Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrara aplicações com o Azure AD
Os seguintes artigos discutem as diferentes formas como as aplicações se integram com a AD Azure, e fornecem alguma orientação.

* [Determinar qual o Diretório Ativo a utilizar](../fundamentals/active-directory-administer.md)
* [Utilização de aplicações na galeria de aplicações do Azure](what-is-single-sign-on.md)
* [Integrando a lista de tutoriais de aplicações saaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Tipos de autenticação
Cada uma das suas aplicações pode ter diferentes requisitos de autenticação. Com o Azure AD, os certificados de assinatura podem ser utilizados com aplicações que utilizam protocolos SAML 2.0, WS-Federation ou OpenID Connect, bem como Password Single Sign On. Para obter mais informações sobre os tipos de autenticação da aplicação para utilização com o Azure AD consulte [AD de gestão de certificados de inscrição única federada em Diretório Ativo Azure](manage-certificates-for-federated-single-sign-on.md) e [sinal único baseado em palavra-passe em](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Habilitar o SSO com a Aplicação AD Azure Proxy
Com o Microsoft Azure AD Application Proxy, pode fornecer acesso a aplicações localizadas dentro da sua rede privada de forma segura, a partir de qualquer lugar e em qualquer dispositivo. Depois de ter instalado um conector proxy de aplicação dentro do seu ambiente, pode ser facilmente configurado com AD Azure.

### <a name="integrating-custom-applications"></a>Integração de aplicações personalizadas
Se está a escrever uma nova aplicação e quer ajudar os desenvolvedores a alavancar o poder do Azure AD, consulte [os desenvolvedores orientadores.](../active-directory-applications-guiding-developers-for-lob-applications.md)

Se quiser adicionar a sua aplicação personalizada à Galeria de Aplicações Azure, consulte ["Bring your own app" com a configuração SAML de Self-Service Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Gerir o acesso a aplicações
Os seguintes artigos descrevem formas de gerir o acesso a aplicações uma vez que tenham sido integradas com a Azure AD utilizando conectores Azure AD e Azure AD.

* [Gerir o acesso a apps usando o Azure AD](what-is-access-management.md)
* [Automatização com Conectores AD Azure](../app-provisioning/user-provisioning.md)
* [Atribuir utilizadores a uma aplicação](../active-directory-applications-guiding-developers-assigning-users.md)
* [Atribuir grupos a uma aplicação](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Partilhar contas](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Passos seguintes
Para obter informações aprofundadas, pode baixar os planos de implementação do Azure Ative Directory a partir do [GitHub](https://aka.ms/deploymentplans). Para aplicações de galeria, pode descarregar planos de implementação para um único sinal, acesso condicional e fornecimento de utilizadores através do [portal Azure](https://portal.azure.com). 

Para descarregar um plano de implementação do portal Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Aplicações empresariais** | Escolha um Plano de**Implementação**de**Aplicações** | .

Por favor, forneça feedback sobre os planos de implantação, fazendo o levantamento do plano de [implantação.](https://aka.ms/DeploymentPlanFeedback)

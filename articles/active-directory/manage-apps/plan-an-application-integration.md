---
title: Começar a integrar a Azure AD com apps
description: Este artigo é um guia de arranque para integrar o Azure Ative Directory (AD) com aplicações no local e aplicações em nuvem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 8b321acb00e6e9b4b6cca117afba8bf0c9432719
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258471"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrar o Azure Ative Directory com candidaturas a iniciar guia

Este tópico resume o processo de integração de aplicações com o Azure Ative Directory (AD). Cada uma das secções abaixo contém um breve resumo de um tópico mais detalhado para que possa identificar quais as partes deste guia que começa a ser iniciado.

Para descarregar planos de implementação aprofundados, consulte [os próximos passos](#next-steps).

## <a name="take-inventory"></a>Faça o inventário
Antes de integrar as aplicações com a Azure AD, é importante saber onde está e para onde quer ir.  As seguintes questões destinam-se a ajudá-lo a pensar no seu projeto de integração de aplicações AZure.

### <a name="application-inventory"></a>Inventário de aplicações
* Onde estão todas as suas candidaturas? Quem é o dono?
* Que tipo de autenticação as suas aplicações requerem?
* Quem precisa de acesso a que aplicações?
* Deseja implementar uma nova aplicação?
  * Vai construí-lo internamente e implantá-lo em uma instância computacional do Azure?
  * Utilizará um disponível na Galeria de Aplicações Azure?

### <a name="user-and-group-inventory"></a>Inventário de utilizador e grupo
* Onde residem as suas contas de utilizador?
  * Active Directory no local
  * Azure AD
  * Dentro de uma base de dados de aplicações separada que possui
  * Em aplicações não divulgadas
  * Todas as opções acima
* Que permissões e atribuições de funções têm atualmente os utilizadores individuais? Precisa de rever o acesso deles ou tem a certeza de que o acesso ao utilizador e as atribuições de funções são apropriados agora?
* Os grupos já estão estabelecidos no seu Ative Directory?
  * Como estão organizados os seus grupos?
  * Quem são os membros do grupo?
  * Que permissões/atribuições de funções os grupos têm atualmente?
* Precisa de limpar as bases de dados de utilizador/grupo antes de se integrar?  (Esta é uma pergunta muito importante. Lixo, lixo para fora.)

### <a name="access-management-inventory"></a>Inventário de gestão de acessos
* Como gere atualmente o acesso dos utilizadores às aplicações? Isto tem de mudar?  Já pensou noutras formas de gerir o acesso, como por exemplo com [o Azure RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Quem precisa de acesso a quê?

Talvez não tenhas as respostas para todas estas perguntas, mas não faz mal.  Este guia pode ajudá-lo a responder a algumas dessas perguntas e a tomar algumas decisões informadas.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Encontre aplicações de nuvem não solicitadas com Cloud Discovery

Como mencionado acima, pode haver aplicações que não foram geridas pela sua organização até agora.  Como parte do processo de inventário, é possível encontrar aplicações em nuvem não higides. Consulte [a Configuração Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrara aplicações com o Azure AD
Os seguintes artigos discutem as diferentes formas de integração das aplicações com a Azure AD, e fornecem alguma orientação.

* [Determinar qual diretório ativo utilizar](../fundamentals/active-directory-whatis.md)
* [Utilização de aplicações na galeria de aplicações Azure](what-is-single-sign-on.md)
* [Integrar lista de tutoriais de aplicações do SaaS](../saas-apps/tutorial-list.md)

### <a name="authentication-types"></a>Tipos de autenticação
Cada uma das suas aplicações pode ter diferentes requisitos de autenticação. Com a Azure AD, os certificados de assinatura podem ser utilizados com aplicações que utilizam protocolos SAML 2.0, WS-Federação ou OpenID Connect Protocols, bem como signo único de palavra-passe. Para obter mais informações sobre os tipos de autenticação de aplicações para utilização com Azure AD consulte [os Certificados de Gestão de Sign-On Federados em Diretório Ativo Azure](manage-certificates-for-federated-single-sign-on.md) e [sinal de palavra-passe com base em](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Ativar SSO com Proxy app AD AZure
Com o Microsoft Azure AD Application Proxy, pode fornecer acesso a aplicações localizadas dentro da sua rede privada de forma segura, a partir de qualquer lugar e em qualquer dispositivo. Depois de ter instalado um conector proxy de aplicação dentro do seu ambiente, pode ser facilmente configurado com Azure AD.

### <a name="integrating-custom-applications"></a>Integração de aplicações personalizadas
Se pretender adicionar a sua aplicação personalizada à Galeria de Aplicações Azure, consulte [a publicação da sua aplicação na galeria de aplicações AZure AD.](../develop/v2-howto-app-gallery-listing.md)

## <a name="managing-access-to-applications"></a>Gerir o acesso às aplicações
Os seguintes artigos descrevem formas de gerir o acesso às aplicações depois de terem sido integrados com a AZure AD utilizando conectores AD Ad Azure e Azure AD.

* [Gerir o acesso a apps usando Azure AD](what-is-access-management.md)
* [Automatização com Conectores AD AZURE](../app-provisioning/user-provisioning.md)
* [Atribuir utilizadores a uma aplicação](./assign-user-or-group-access-portal.md)
* [Atribuir grupos a uma aplicação](./assign-user-or-group-access-portal.md)
* [Partilhar contas](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Passos seguintes
Para obter informações aprofundadas, você pode baixar os planos de implementação do Azure Ative Directory a partir do [GitHub](../fundamentals/active-directory-deployment-plans.md). Para aplicações de galeria, pode descarregar planos de implementação para um único sign-on, Acesso Condicional e fornecimento de utilizadores através do [portal Azure.](https://portal.azure.com) 

Para descarregar um plano de implementação a partir do portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **aplicações empresariais**  |  Escolha um plano de implementação de  |  **aplicativos.**

Por favor, forneça feedback sobre os planos de implantação, realizando o inquérito do [plano de implantação](https://aka.ms/DeploymentPlanFeedback).
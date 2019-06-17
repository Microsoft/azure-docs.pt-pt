---
title: Introdução ao integrar o Azure AD com as aplicações | Documentos da Microsoft
description: Este artigo é um guia de introdução para a integração do Azure Active Directory (AD) com aplicações no local e aplicações na cloud.
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
ms.openlocfilehash: 11453ad9132664313df2b56cb3664512628a16ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108228"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guia de introdução de integração do Azure Active Directory com aplicativos de introdução

Este tópico resume o processo de integração de aplicativos com o Azure Active Directory (AD). Cada uma das secções abaixo contêm um breve resumo sobre um tópico mais detalhado para que possa identificar quais partes deste guia de introdução são relevantes para si.

Para transferir os planos de implementação detalhados, consulte [próximos passos](#next-steps).

## <a name="take-inventory"></a>Faça o inventário
Antes de integração de aplicativos com o Azure AD, é importante saber onde está e onde pretende ir.  As perguntas seguintes destinam-se para o ajudar a pensar sobre o seu projeto de integração de aplicações do Azure AD.

### <a name="application-inventory"></a>Inventário de aplicativos
* Onde estão todas as suas aplicações? Quem é o proprietário-los?
* Que tipo de autenticação precisam seus aplicativos?
* Quem precisa de acesso a quais aplicativos?
* Deseja implantar uma nova aplicação?
  * Será criado internamente e implementá-la numa instância de computação do Azure?
  * Irá utilizar um que está disponível na Galeria de aplicações do Azure?

### <a name="user-and-group-inventory"></a>Inventário de utilizador e grupo
* Onde residem as suas contas de utilizador?
  * Active Directory no local
  * Azure AD
  * Dentro de uma base de dados de aplicativo separado que é proprietário
  * Em aplicações não aprovadas
  * Todas as opções acima
* Quais permissões e as atribuições de funções de utilizadores individuais têm atualmente? Precisa para rever o acesso ou tem a certeza de que as atribuições de acesso e a função de utilizador são adequadas agora?
* Grupos já estabelecidos no Active Directory no local?
  * Como os seus grupos estão organizados?
  * Quem são os membros do grupo?
  * As atribuições de permissões/funções os grupos têm atualmente?
* Será necessário limpar as bases de dados de utilizador/grupo antes de integração?  (Esta é uma pergunta muito importante. Lixo no lixo out).

### <a name="access-management-inventory"></a>Inventário de gestão de acesso
* Como atualmente gerir o acesso de utilizador a aplicações? É necessário alterar?  Consideraram outras formas de gerir o acesso, por exemplo, com [RBAC](../../role-based-access-control/role-assignments-portal.md) por exemplo?
* Quem precisa de acesso para qual?

Talvez não tenha as respostas a essas perguntas com antecedência, mas isso é muito bem.  Este guia pode ajudá-lo a responder a algumas dessas perguntas e tomar algumas decisões informadas.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Localizar aplicações na cloud não aprovadas com a Cloud Discovery

Conforme mencionado acima, pode haver aplicativos que ainda não foram geridos pela sua organização até agora.  Como parte do processo de inventário, é possível localizar aplicações na cloud não aprovadas. Ver [configurar a Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrar aplicações com o Azure AD
Os seguintes artigos descrevem as diferentes formas de aplicativos integram com o Azure AD e fornece algumas orientações.

* [Determinar qual do Active Directory para utilizar](../fundamentals/active-directory-administer.md)
* [Utilizar aplicações na Galeria de aplicações do Azure](what-is-single-sign-on.md)
* [Integrar a lista de tutoriais de aplicações de SaaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Tipos de autenticação
Cada uma das suas aplicações pode ter requisitos de autenticação diferente. Com o Azure AD, podem ser utilizado certificados de assinatura com aplicações que utilizam SAML 2.0, WS-Federation, ou OpenID Connect protocolos bem como de palavra-passe de início de sessão único. Para obter mais informações sobre a aplicação veja os tipos de autenticação para utilização com o Azure AD [gestão de certificados para federado início de sessão único no Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) e [palavra-passe de início de sessão único com base em](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Ativar o SSO com o Proxy de aplicações do Azure AD
Com o Proxy de aplicações do Microsoft Azure AD, pode fornecer acesso às aplicações localizadas no interior da rede privada com segurança, de qualquer lugar e em qualquer dispositivo. Depois de instalar um conector de proxy de aplicações no seu ambiente, ele pode ser facilmente configurado com o Azure AD.

### <a name="integrating-custom-applications"></a>Integração de aplicativos personalizados
Se estiver escrevendo um aplicativo novo e quiser para ajudar os desenvolvedores a tirar partido do poder do Azure AD, consulte [Guiding desenvolvedores](../active-directory-applications-guiding-developers-for-lob-applications.md).

Se pretende adicionar a sua aplicação personalizada para a Galeria de aplicações do Azure, veja ["Traga a sua própria aplicação" com a configuração SAML do Self-Service do Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Gerir o acesso a aplicações
Os seguintes artigos descrevem formas que pode gerir o acesso a aplicações assim que tiver sido integrados com o Azure AD através do Azure AD conectores e o Azure AD.

* [Gerir o acesso a aplicações com o Azure AD](what-is-access-management.md)
* [Automatizar com conectores do Azure AD](user-provisioning.md)
* [Atribuir utilizadores a uma aplicação](../active-directory-applications-guiding-developers-assigning-users.md)
* [Atribuir grupos a uma aplicação](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Partilhar contas](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Passos Seguintes
Para obter informações aprofundadas, pode baixar os planos de implementação do Azure Active Directory do [GitHub](https://aka.ms/deploymentplans). Para aplicações de galeria, pode baixar os planos de implantação para início de sessão único, acesso condicional e por meio de provisionamento de usuários a [portal do Azure](https://portal.azure.com). 

Para transferir um plano de implementação do portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **aplicações empresariais** | **escolher uma aplicação** | **plano de implantação**.

Forneça comentários sobre planos de implantação, efetuando a [inquérito de plano de implementação](https://aka.ms/DeploymentPlanFeedback).

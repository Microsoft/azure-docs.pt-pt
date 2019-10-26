---
title: Introdução à integração do Azure AD com aplicativos | Microsoft Docs
description: Este artigo é um guia de introdução para a integração do Azure Active Directory (AD) com aplicativos locais e aplicativos de nuvem.
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
ms.openlocfilehash: 1291db8cd895a1104183ae2b5388eb6a23305874
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895925"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guia de introdução à integração de Azure Active Directory com aplicativos

Este tópico resume o processo de integração de aplicativos com o Azure Active Directory (AD). Cada uma das seções abaixo contém um breve resumo de um tópico mais detalhado para que você possa identificar quais partes deste guia de introdução são relevantes para você.

Para baixar planos de implantação detalhados, consulte [próximas etapas](#next-steps).

## <a name="take-inventory"></a>Fazer inventário
Antes de integrar aplicativos com o Azure AD, é importante saber onde você está e onde deseja ir.  As perguntas a seguir destinam-se a ajudá-lo a pensar em seu projeto de integração de aplicativos do Azure AD.

### <a name="application-inventory"></a>Inventário de aplicativos
* Onde estão todos os seus aplicativos? Quem as possui?
* Que tipo de autenticação os aplicativos exigem?
* Quem precisa de acesso a quais aplicativos?
* Deseja implantar um novo aplicativo?
  * Você irá criá-lo internamente e implantá-lo em uma instância de computação do Azure?
  * Você usará um que está disponível na Galeria de Aplicativo Azure?

### <a name="user-and-group-inventory"></a>Inventário de usuários e grupos
* Onde residem as suas contas de usuário?
  * Active Directory no local
  * Azure AD
  * Em um banco de dados de aplicativo separado que você possui
  * Em aplicativos não aprovados
  * Todos os itens acima
* Quais permissões e atribuições de função os usuários individuais têm atualmente? Você precisa revisar seu acesso ou tem certeza de que o acesso do usuário e as atribuições de função são apropriadas agora?
* Os grupos já estão estabelecidos no Active Directory local?
  * Como os grupos são organizados?
  * Quem são os membros do grupo?
  * Quais permissões/atribuições de função os grupos têm atualmente?
* Será necessário limpar os bancos de dados de usuário/grupo antes de integrar?  (Essa é uma pergunta muito importante. Entrada de lixo, saída de lixo.)

### <a name="access-management-inventory"></a>Inventário de gerenciamento de acesso
* Como você atualmente gerencia o acesso do usuário aos aplicativos? Isso precisa ser alterado?  Você considerou outras maneiras de gerenciar o acesso, como com o [RBAC](../../role-based-access-control/role-assignments-portal.md) , por exemplo?
* Quem precisa de acesso ao quê?

Talvez você não tenha as respostas para todas essas perguntas com antecedência, mas tudo bem.  Este guia pode ajudá-lo a responder a algumas dessas perguntas e tomar algumas decisões informadas.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Encontre aplicativos de nuvem não aprovados com Cloud Discovery

Conforme mencionado acima, pode haver aplicativos que ainda não foram gerenciados pela sua organização até agora.  Como parte do processo de inventário, é possível encontrar aplicativos de nuvem não aprovados. Consulte [configurar Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrando aplicativos com o Azure AD
Os artigos a seguir discutem as diferentes maneiras como os aplicativos se integram ao Azure AD e fornecem algumas diretrizes.

* [Determinando qual Active Directory usar](../fundamentals/active-directory-administer.md)
* [Usando aplicativos na Galeria de aplicativos do Azure](what-is-single-sign-on.md)
* [Integração da lista de tutoriais de aplicativos SaaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Tipos de autenticação
Cada um de seus aplicativos pode ter requisitos de autenticação diferentes. Com o Azure AD, certificados de autenticação podem ser usados com aplicativos que usam protocolos SAML 2,0, WS-Federation ou OpenID Connect, bem como logon único com senha. Para obter mais informações sobre os tipos de autenticação de aplicativo para uso com o AD do Azure, consulte [gerenciando certificados para logon único federado em Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) e [logon único baseado em senha](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Habilitando o SSO com Aplicativo Azure AD proxy
Com o proxy de aplicativo Microsoft Azure AD, você pode fornecer acesso a aplicativos localizados dentro de sua rede privada com segurança, de qualquer lugar e em qualquer dispositivo. Depois de instalar um conector de proxy de aplicativo em seu ambiente, ele pode ser facilmente configurado com o Azure AD.

### <a name="integrating-custom-applications"></a>Integrando aplicativos personalizados
Se você estiver escrevendo um novo aplicativo e quiser ajudar os desenvolvedores a aproveitar o poder do Azure AD, consulte [orientando os desenvolvedores](../active-directory-applications-guiding-developers-for-lob-applications.md).

Se você quiser adicionar seu aplicativo personalizado à galeria de Aplicativo Azure, consulte ["Traga seu próprio aplicativo" com a configuração do SAML de autoatendimento do Azure ad](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Gerenciando o acesso a aplicativos
Os artigos a seguir descrevem maneiras de gerenciar o acesso a aplicativos depois que eles tiverem sido integrados ao Azure AD usando os conectores do Azure AD e o Azure AD.

* [Gerenciando o acesso a aplicativos usando o Azure AD](what-is-access-management.md)
* [Automatizando com conectores do Azure AD](user-provisioning.md)
* [Atribuir utilizadores a uma aplicação](../active-directory-applications-guiding-developers-assigning-users.md)
* [Atribuir grupos a uma aplicação](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Compartilhando contas](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas, você pode baixar Azure Active Directory planos de implantação do [GitHub](https://aka.ms/deploymentplans). Para aplicativos da galeria, você pode baixar planos de implantação para logon único, acesso condicional e provisionamento de usuário por meio do [portal do Azure](https://portal.azure.com). 

Para baixar um plano de implantação do portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **aplicativos empresariais** | **escolha um aplicativo** | **plano de implantação**.

Forneça comentários sobre os planos de implantação fazendo a [pesquisa do plano de implantação](https://aka.ms/DeploymentPlanFeedback).

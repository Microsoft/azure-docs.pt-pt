---
title: Adicionar uma aplicação multi-inquilino à Galeria de aplicações do Azure AD | Documentos da Microsoft
description: Explica como pode listar a sua aplicação multi-inquilino personalizada na Galeria de aplicações do Azure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0434ec889a791079e2019bcb1d04f5ce0fceb731
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545563"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Adicionar uma aplicação multi-inquilino à Galeria de aplicações do Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de aplicações do Azure AD?

Azure Active Directory (Azure AD) é um serviço de identidade com base na cloud. O [Galeria de aplicações do Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) é na loja de aplicações do Azure Marketplace, onde todos os conectores de aplicações são publicados para início de sessão único e o aprovisionamento de utilizadores. Os clientes que utilizam o Azure AD como fornecedor de identidade encontrar os conectores de aplicações SaaS diferentes publicados aqui. Os administradores de TI adicionar conectores a partir da Galeria de aplicações e, em seguida, configurar e utilizam os conectores para início de sessão único e o aprovisionamento. O Azure AD suporta todos os protocolos de Federação principal, incluindo o SAML 2.0, OpenID Connect, OAuth e WS-Fed para início de sessão único. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se a aplicação suporta SAML ou OpenIDConnect
Se tiver uma aplicação multi-inquilino que pretende que sejam apresentados na Galeria de aplicações do Azure AD, deve primeiro certificar-se de que seu aplicativo oferece suporte a uma das seguintes únicas início de sessão em tecnologias:

- **OpenID Connect**: Para ter a aplicação listada, crie a aplicação multi-inquilino no Azure AD e implementar o [framework de consentimento do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) para a sua aplicação. Envie o pedido de início de sessão para um ponto de extremidade comum para que todos os clientes podem fornecer consentimento para a aplicação. Pode controlar o acesso de um utilizador com base no ID de inquilino e o UPN do utilizador recebido no token. Submeter a aplicação utilizando o processo descrito na [listar a sua aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Se a sua aplicação suporta SAML 2.0, a aplicação pode ser apresentada na galeria. Siga as instruções em [listar a sua aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se a sua aplicação não suporta SAML ou OpenIDConnect
Aplicações que não suportam SAML ou OpenIDConnect ainda podem ser integradas numa galeria de aplicações através de palavra-passe único início de sessão em tecnologia.

Palavra-passe início de sessão único, também chamado de cofres de palavras de palavra-passe, permite-lhe gerir o acesso de utilizador e palavras-passe para aplicações web que não suportam o Federação de identidades. Também é útil para cenários em que vários utilizadores tem de partilhar uma única conta, tal como para contas de aplicação de comunicação social da sua organização. 

Se pretende apresentar a sua aplicação com esta tecnologia:
1. Criar uma aplicação web que tem uma página de início de sessão do HTML para configurar [palavra-passe início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Submeter o pedido, conforme descrito em [listar a sua aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Escalonamento

Para qualquer escalonamentos, envie um e-mail para [equipa de integração do SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e voltaremos a logo que possível.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [inclua a sua aplicação na Galeria de aplicações do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

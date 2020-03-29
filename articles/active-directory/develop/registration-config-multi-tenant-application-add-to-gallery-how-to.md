---
title: Adicione aplicativo multiinquilino à galeria de aplicações da Azure AD Microsoft Docs
description: Explica como pode listar a sua aplicação multiarrendatária desenvolvida sob medida na galeria de aplicações da AD Azure.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: be660ad42c1336d479f1793b20d2994682db1225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702764"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Adicione uma aplicação multiarrendatária à galeria de aplicações da AD Azure

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicações da AD Azure?

Azure Ative Directory (Azure AD) é um serviço de identidade baseado na nuvem. A [galeria de aplicações Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) está na loja de aplicações Azure Marketplace, onde todos os conectores de aplicações são publicados para um único fornecimento de inscrição e utilizador. Os clientes que utilizam a Azure AD como fornecedor de identidade encontram os diferentes conectores de aplicações SaaS publicados aqui. Os administradores de TI adicionam conectores da galeria de aplicações e, em seguida, configuram e utilizam os conectores para um único sinal e provisionamento. A Azure AD apoia todos os principais protocolos da federação, incluindo SAML 2.0, OpenID Connect, OAuth e WS-Fed para um único sign-on. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se a sua aplicação apoiar sAML ou OpenIDConnect
Se tem uma aplicação multiarrendatária que deseja listada na galeria de aplicações da AD Azure, deve primeiro certificar-se de que a sua aplicação suporta uma das seguintes tecnologias de inscrição única:

- **OpenID Connect**: Para ter a sua aplicação listada, crie a aplicação multiarrendatária em Azure AD e implemente o quadro de [consentimento da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) para a sua aplicação. Envie o pedido de login para um ponto final comum para que qualquer cliente possa dar o seu consentimento à aplicação. Pode controlar o acesso de um utilizador com base no ID do inquilino e na UPN do utilizador recebida no token. Envie a candidatura utilizando o processo delineado na Listagem da sua aplicação na galeria de aplicações do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

- **SAML**: Se a sua aplicação apoiar o SAML 2.0, a aplicação pode ser listada na galeria. Siga as instruções em Listar a sua candidatura na galeria de aplicações do [Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se a sua aplicação não suportar saml ou OpenIDConnect
As aplicações que não suportam o SAML ou o OpenIDConnect ainda podem ser integradas na galeria de aplicações através da tecnologia de assinatura única de senha.

O insessão individual de senha, também chamado de abóbada de senha, permite-lhe gerir o acesso do utilizador e as palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil para cenários em que vários utilizadores precisam de partilhar uma única conta, como nas contas de aplicações de redes sociais da sua organização. 

Se quiser listar a sua aplicação com esta tecnologia:
1. Crie uma aplicação web que tenha uma página de início de sessão HTML para configurar o início de sessão individual da [palavra-passe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Envie o pedido conforme descrito na Listagem da sua candidatura na galeria de aplicações do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

## <a name="escalations"></a>Escaladas

Para qualquer escalada, envie e-mail para a Equipa de [Integração SSO da AD Azure](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e entraremos em contacto consigo o mais rápido possível.

## <a name="next-steps"></a>Passos seguintes
Saiba como listar a sua candidatura na galeria de aplicações do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

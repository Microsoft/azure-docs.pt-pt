---
title: Adicione app multitenante à galeria de aplicações AD Azure
description: Explica como pode listar a sua aplicação multitenante desenvolvida sob medida na galeria de aplicações AZure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ROBOTS: NOINDEX
ms.openlocfilehash: de0231a49c4f806660ea0cb305fdc1a70e2b36d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063132"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Adicionar uma aplicação multi-inquilino à galeria da aplicação do Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicações AD AZure?

Azure Ative Directory (Azure AD) é um serviço de identidade baseado na nuvem. A [galeria de aplicações AZure AD](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) encontra-se na loja de aplicações Azure Marketplace, onde todos os conectores de aplicações são publicados para um único sign-on e provisionamento do utilizador. Os clientes que utilizam o Azure AD como fornecedor de identidade encontram os diferentes conectores de aplicação SaaS publicados aqui. Os administradores de TI adicionam conectores da galeria de aplicações e, em seguida, configuram e utilizam os conectores para um único sinal e provisionamento. A Azure AD suporta todos os principais protocolos da federação, incluindo SAML 2.0, OpenID Connect, OAuth e WS-Fed para uma única sação. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se a sua aplicação suportar SAML ou OpenIDConnect
Se tiver uma aplicação multitenante que pretenda listada na galeria de aplicações AZure AD, deve primeiro certificar-se de que a sua aplicação suporta uma das seguintes tecnologias de inscrição única:

- **OpenID Connect**: Para ter a sua aplicação listada, crie a aplicação multitenante em AZure AD e implemente o [quadro de consentimento Azure AD](./consent-framework.md) para a sua aplicação. Envie o pedido de login para um ponto final comum para que qualquer cliente possa fornecer consentimento para a aplicação. Pode controlar o acesso de um utilizador com base no ID do inquilino e na UPN do utilizador recebida no token. Submeta o pedido utilizando o processo delineado na [Listagem da sua candidatura na galeria de candidaturas Azure Ative.](./v2-howto-app-gallery-listing.md)

- **SAML**: Se a sua aplicação suportar SAML 2.0, a aplicação pode ser listada na galeria. Siga as instruções na [Listagem do seu pedido na galeria de candidaturas do Azure Ative.](./v2-howto-app-gallery-listing.md)

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se a sua aplicação não suportar SAML ou OpenIDConnect
As aplicações que não suportam SAML ou OpenIDConnect ainda podem ser integradas na galeria de aplicações através da tecnologia de assinatura única.

O sismo único de palavra-passe, também chamado de cofre de palavras-passe, permite-lhe gerir o acesso ao utilizador e as palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil para cenários em que vários utilizadores precisam de partilhar uma única conta, como é o caso das contas de aplicações de redes sociais da sua organização. 

Se quiser listar a sua aplicação com esta tecnologia:
1. Crie uma aplicação web que tenha uma página de entrada HTML para configurar [o sign-in único da palavra-passe](../manage-apps/what-is-single-sign-on.md). 
2. Submeta o pedido conforme descrito na [Listagem do seu pedido na galeria de candidaturas Azure Ative.](./v2-howto-app-gallery-listing.md)

## <a name="escalations"></a>Escaladas

Para qualquer escalada, envie um e-mail para a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e entraremos em conversações consigo o mais rapidamente possível.

## <a name="next-steps"></a>Passos seguintes
Saiba como listar a [sua candidatura na galeria de candidaturas do Azure Ative Directory.](./v2-howto-app-gallery-listing.md)
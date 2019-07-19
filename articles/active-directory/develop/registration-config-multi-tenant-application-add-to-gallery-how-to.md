---
title: Adicionar um aplicativo multilocatário à galeria de aplicativos do Azure AD | Microsoft Docs
description: Explica como você pode listar o aplicativo multilocatário personalizado desenvolvido na Galeria de aplicativos do Azure AD.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8024130b986d111abe3b470d6b06e86ed6419dc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320923"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Adicionar um aplicativo multilocatário à galeria de aplicativos do Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de aplicativos do Azure AD?

O Azure Active Directory (AD do Azure) é um serviço de identidade baseado em nuvem. A [Galeria de aplicativos do Azure ad](https://azure.microsoft.com/marketplace/active-directory/all/) está na loja de aplicativos do Azure Marketplace, onde todos os conectores de aplicativos são publicados para logon único e provisionamento de usuário. Os clientes que usam o Azure AD como um provedor de identidade localizam os diferentes conectores de aplicativos SaaS publicados aqui. Os administradores de ti adicionam conectores da Galeria de aplicativos e, em seguida, configuram e usam os conectores para logon único e provisionamento. O Azure AD dá suporte a todos os principais protocolos de Federação, incluindo SAML 2,0, OpenID Connect, OAuth e WS-enalimentado para logon único. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se seu aplicativo der suporte a SAML ou OpenIDConnect
Se você tiver um aplicativo multilocatário que você deseja listar na Galeria de aplicativos do Azure AD, deverá primeiro certificar-se de que seu aplicativo dá suporte a uma das seguintes tecnologias de logon único:

- **OpenID Connect**: Para que seu aplicativo seja listado, crie o aplicativo multilocatário no Azure AD e implemente a [estrutura de consentimento do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) para seu aplicativo. Envie a solicitação de logon para um ponto de extremidade comum para que qualquer cliente possa fornecer consentimento para o aplicativo. Você pode controlar o acesso de um usuário com base na ID do locatário e o UPN do usuário recebido no token. Envie o aplicativo usando o processo descrito em [listando seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Se seu aplicativo der suporte a SAML 2,0, o aplicativo poderá ser listado na galeria. Siga as instruções em [listando seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se seu aplicativo não der suporte a SAML ou OpenIDConnect
Os aplicativos que não dão suporte a SAML ou OpenIDConnect ainda podem ser integrados à galeria de aplicativos por meio da tecnologia de logon único de senha.

O logon único com senha, também chamado de cofre de senha, permite que você gerencie o acesso do usuário e as senhas para aplicativos Web que não dão suporte à Federação de identidade. Ele também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como as contas de aplicativo de mídia social da sua organização. 

Se você quiser listar seu aplicativo com essa tecnologia:
1. Crie um aplicativo Web que tenha uma página de entrada HTML para configurar o [logon único com senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Envie a solicitação conforme descrito em [listando seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Escalonamentos

Para qualquer escalonamento, envie um email para a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e voltaremos a você assim que possível.

## <a name="next-steps"></a>Passos seguintes
Saiba como [listar seu aplicativo na Galeria de aplicativos Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
title: Aplicativos únicos e multilocatários no Azure AD
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os recursos e as diferenças entre aplicativos de locatário único e multilocatário no Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0cef2ed24eb242629aa5547391c0d2a27344d8b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919485"
---
# <a name="tenancy-in-azure-active-directory"></a>Aluguel em Azure Active Directory

O Azure Active Directory (AD do Azure) organiza objetos como usuários e aplicativos em grupos chamados *locatários*. Os locatários permitem que um administrador defina políticas nos usuários dentro da organização e os aplicativos que a organização possui para atender às políticas operacionais e de segurança. 

## <a name="who-can-sign-in-to-your-app"></a>Quem pode entrar em seu aplicativo?

Quando se trata de desenvolver aplicativos, os desenvolvedores podem optar por configurar seu aplicativo para ser um locatário único ou multilocatário durante o registro do aplicativo no [portal do Azure](https://portal.azure.com).
* Os aplicativos de locatário único só estão disponíveis no locatário em que foram registrados, também conhecidos como seu locatário inicial.
* Aplicativos multilocatários estão disponíveis para usuários em ambos os locatários domésticos e outros locatários.

No portal do Azure, você pode configurar seu aplicativo para ser de locatário único ou multilocatário definindo o público como a seguir.

| Audiência | Único/multilocatário | Quem pode entrar | 
|----------|--------| ---------|
| Contas somente neste diretório | Inquilino único | Todas as contas de usuário e convidado em seu diretório podem usar seu aplicativo ou API.<br>*Use esta opção se o público-alvo for interno à sua organização.* |
| Contas em qualquer diretório do AD do Azure | Multi-inquilino | Todos os usuários e convidados com uma conta corporativa ou de estudante da Microsoft podem usar seu aplicativo ou API. Isso inclui escolas e empresas que usam o Office 365.<br>*Use esta opção se o público-alvo for cliente corporativo ou educativo.* |
| Contas em qualquer diretório do Azure AD e contas pessoais da Microsoft (como Skype, Xbox, Outlook.com) | Multi-inquilino | Todos os usuários com um trabalho ou escola ou conta Microsoft pessoal podem usar seu aplicativo ou API. Ele inclui escolas e empresas que usam o Office 365, bem como contas pessoais que são usadas para entrar em serviços como Xbox e Skype.<br>*Use esta opção para direcionar o conjunto mais amplo de contas da Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Práticas recomendadas para aplicativos multilocatários

A criação de aplicativos ótimos de vários locatários pode ser desafiadora devido ao número de políticas diferentes que os administradores de ti podem definir em seus locatários. Se você optar por criar um aplicativo multilocatário, siga estas práticas recomendadas:

* Teste seu aplicativo em um locatário que tenha configurado [políticas de acesso condicional](conditional-access-dev-guide.md).
* Siga o princípio do acesso mínimo do usuário para garantir que seu aplicativo solicite apenas as permissões de que realmente precisa. Evite solicitar permissões que exijam consentimento de administrador, pois isso pode impedir que os usuários adquiram seu aplicativo em algumas organizações. 
* Forneça nomes e descrições apropriados para quaisquer permissões que você expor como parte de seu aplicativo. Isso ajuda os usuários e administradores a saber o que eles estão concordando ao tentarem usar as APIs do seu aplicativo. Para obter mais informações, consulte a seção práticas recomendadas no [Guia de permissões](v1-permissions-and-consent.md).

## <a name="next-steps"></a>Passos seguintes

* [Como converter um aplicativo para ser multilocatário](howto-convert-app-to-be-multi-tenant.md)

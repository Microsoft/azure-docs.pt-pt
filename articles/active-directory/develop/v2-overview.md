---
title: Sobre o v2.0 | Azure
description: Saiba mais sobre a plataforma e o ponto final do v2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb269df035bcc11583ebb7cff7d1ee2c3f6d8bca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250303"
---
# <a name="about-v20"></a>Sobre o v2.0

A plataforma e o ponto final do v2.0 estão em pré-visualização e são continuamente melhorados. Hoje em dia, os cenários de aplicação de página única (SPA) do JavaScript incluem todas as funcionalidades e convidamo-lo a utilizar o MSAL.js para criar aplicações baseadas no browser e enviar-nos comentários, para que possamos atualizar o estado da pré-visualização para disponibilidade geral (GA).

> [!NOTE]
> A MSAL Android, iOS e .NET ainda têm funcionalidades em desenvolvimento. Pode utilizá-los para criar aplicações e enviar-nos comentários.

O portal do Azure [registos de aplicações (pré-visualização)](quickstart-register-app.md) experiência foi significativamente atualizada para incluir todas as suas aplicações criadas com o ADAL ou MSAL e para melhorar a usabilidade.

No passado, os programadores de aplicações que queriam suportar as contas pessoais e profissionais da Microsoft do Azure Active Directory (Azure AD) tinham de se integrar em dois sistemas separados. A plataforma e o ponto final do v2.0 oferece uma versão da API de autenticação que simplifica este processo. Permite inícios de sessão de ambos os tipos de contas, através de uma única integração. As aplicações que utilizam o ponto final do v2.0 também podem consumir as APIs REST do [Microsoft Graph API](https://developer.microsoft.com/graph) ao utilizar ambos os tipos de contas.

## <a name="getting-started"></a>Introdução

Escolha a sua plataforma favorita na lista seguinte para criar uma aplicação com o Microsoft abrir estruturas e bibliotecas de origem:

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Saiba mais sobre a plataforma e o ponto final do v2.0

Saiba mais sobre o que pode fazer com o ponto final do Azure AD v2.0:

* Detete os [tipos de aplicações que pode criar com o ponto de final do Azure AD v2.0](v2-app-types.md).
* Compreenda os [limites e restrições](active-directory-v2-limitations.md) com o ponto final do Azure AD v2.0.

## <a name="additional-resources"></a>Recursos adicionais

Explore informações aprofundadas sobre o v2.0:

* [Sobre a plataforma de identidade da Microsoft](about-microsoft-identity-platform.md)
* [Referência de protocolos do v2.0](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação d v2.0](reference-v2-libraries.md)
* [Permissões e consentimento na v2.0](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)

> [!NOTE]
> Se apenas tiver de iniciar sessão nas contas profissionais e escolares do Azure Active Directory, comece com o [Guia do programador do Azure AD](v1-overview.md). O ponto final do v2.0 destina-se a programadores que precisam explicitamente de iniciar sessão em contas pessoais da Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

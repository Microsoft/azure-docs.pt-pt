---
title: Criar uma API Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar uma API Web que chama APIs da Web downstream (visão geral).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b357def86b77d4bbb294e2253dacfbd129998ec
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965131"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Cenário: API Web que chama APIs da Web

Saiba tudo o que você precisa para criar uma API Web que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

Esse cenário, a API Web protegida que chama APIs da Web, se baseia no cenário "proteger uma API Web". Para saber mais sobre esse cenário básico, confira [API Web protegida-cenário](scenario-protected-web-api-overview.md) primeiro.

## <a name="overview"></a>Visão geral

- Um cliente (Web, desktop, móvel ou aplicativo de página única) – não representado no diagrama abaixo – chama uma API Web protegida e fornece um token de portador JWT em seu cabeçalho http "Authorization".
- A API Web protegida valida o token e usa o método de `AcquireTokenOnBehalfOf` MSAL para solicitar (do Azure AD) outro token para que ele possa, por sua vez, chamar uma segunda API da Web (chamada de API da Web downstream) em nome do usuário.
- A API Web protegida usa esse token para chamar uma API downstream. Ele também pode chamar `AcquireTokenSilent`mais tarde para solicitar tokens para outras APIs de downstream (mas ainda em nome do mesmo usuário). `AcquireTokenSilent` atualiza o token quando necessário.

![API Web chamando uma API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Especificações

A parte do registro de aplicativo relacionada às permissões de API é clássica. A configuração do aplicativo envolve o uso do fluxo em nome de do OAuth 2,0 para trocar o token de portador JWT em relação a um token para uma API downstream. Esse token é adicionado ao cache de token, onde está disponível nos controladores da API Web e pode adquirir um token silenciosamente para chamar as APIs de downstream.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-api-call-api-app-registration.md)

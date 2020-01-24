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
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701744"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Cenário: uma API Web que chama APIs da Web

Saiba o que você precisa saber para criar uma API Web que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

Esse cenário, no qual uma API Web protegida chama APIs da Web, baseia-se no cenário "proteger uma API Web". Para saber mais sobre esse cenário básico, consulte [cenário: API Web protegida](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Visão geral

- Um cliente de aplicativo Web, de área de trabalho, móvel ou de página única (não representado no diagrama que o acompanha) chama uma API Web protegida e fornece um token de portador JWT (token Web JSON) em seu cabeçalho HTTP "Authorization".
- A API Web protegida valida o token e usa o método de `AcquireTokenOnBehalfOf` da MSAL (biblioteca de autenticação da Microsoft) para solicitar outro token do Azure Active Directory (AD do Azure) para que a API da Web protegida possa chamar uma segunda API da Web ou uma API da Web downstream, em nome do usuário.
- A API Web protegida também pode chamar `AcquireTokenSilent`mais tarde para solicitar tokens para outras APIs downstream em nome do mesmo usuário. `AcquireTokenSilent` atualiza o token quando necessário.

![Diagrama de uma API Web chamando uma API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Especificações

A parte de registro do aplicativo relacionada às permissões de API é clássica. A configuração do aplicativo envolve o uso do fluxo em nome de do OAuth 2,0 para trocar o token de portador JWT em relação a um token para uma API downstream. Esse token é adicionado ao cache de token, onde está disponível nos controladores da API Web, e ele pode adquirir um token silenciosamente para chamar as APIs de downstream.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-api-call-api-app-registration.md)

---
title: API Web que chama a jusante web APIs (descrição geral) - a plataforma de identidade da Microsoft
description: Saiba como compilar uma web API web a jusante esse chamadas APIs (descrição geral).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075404"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Cenário: API Web que chama a APIs web

Saiba tudo o que precisa para criar uma API web que chama APIs web.

## <a name="prerequisites"></a>Pré-requisitos

Neste cenário, a API web protegida que chamadas de web APIs, baseia-se na parte superior do cenário de "Proteger uma API web". Para saber mais sobre este cenário fundamental, veja [API Web protegida - cenário](scenario-protected-web-api-overview.md) primeiro.

## <a name="overview"></a>Descrição geral

- Um cliente (web, área de trabalho, aplicação móvel ou de página única) - não representado no diagrama abaixo - chama uma API web protegida e fornece um token de portador do JWT no cabeçalho Http "Autorização".
- A API web protegida valida o token e utiliza a MSAL `AcquireTokenOnBehalfOf` método para solicitar outro token (a partir do Azure AD) para que ele pode, por si só, chamar uma API (com o nome da API web a jusante) de web segundo em nome do utilizador.
- A API web protegida utiliza esse token para chamar uma API de downstream. Ele também pode chamar `AcquireTokenSilent`posterior para pedir tokens para outras APIs de downstream (mas ainda em nome do utilizador mesmo). `AcquireTokenSilent` Atualiza o token quando necessário.

![Chamar uma API web de API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Informações específicas

A parte do registo de aplicações relacionadas com as permissões de API é clássicas. A configuração de aplicativos envolve o uso o fluxo de em-nome-do OAuth 2.0 para o exchange o token de portador do JWT em relação a um token para uma API de downstream. Este token é adicionado à cache de token, onde ele está disponível nos controladores da API web e pode adquirir um token silenciosamente para chamar as APIs de downstream.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-api-call-api-app-registration.md)

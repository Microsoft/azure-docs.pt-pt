---
title: Construa uma aplicação web que chama APIs web - plataforma de identidade microsoft / Azure
description: Saiba como construir uma aplicação web que chama APIs web (visão geral)
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
ms.openlocfilehash: 9e123195205bb0eb88f0edd4e2dff2e0da9d84ce
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701659"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Cenário: Web app que chama APIs web

Saiba como construir uma aplicação web que assina nos utilizadores na plataforma de identidade da Microsoft e depois liga para a Web APIs em nome do utilizador inscrito.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Este cenário supõe que passou pelo seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicação web que inscreve utilizadores](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Visão geral

Adiciona autenticação à sua Web App, que pode, portanto, inscrever-se nos utilizadores e chama uma API web em nome do utilizador inscrito.

![Aplicação Web que chama as APIs Web](./media/scenario-webapp/web-app.svg)

Web Apps que chama APIs web:

- São pedidos confidenciais de clientes.
- É por isso que registaram um segredo (senha de aplicação ou certificado) com a AD Azure. Este segredo é passado durante a chamada para Azure AD para obter um símbolo

## <a name="specifics"></a>Especificações

> [!NOTE]
> Adicionar sessão a uma Aplicação Web não utiliza as bibliotecas MSAL, uma vez que se trata de proteger a Aplicação Web. Proteger bibliotecas é alcançado por bibliotecas chamadas Middleware. Este foi o objeto do cenário anterior [Os utilizadores de sessão para uma Web App](scenario-web-app-sign-user-overview.md)
>
> Ao ligar para a Web APIs a partir de uma Aplicação Web, terá de obter fichas de acesso para estas APIs web. Pode usar bibliotecas MSAL para adquirir estes tokens.

O fim da experiência final dos desenvolvedores para este cenário tem, portanto, aspetos específicos como:

- Durante o registo da [Candidatura,](scenario-web-app-call-api-app-registration.md)terá de fornecer um, ou vários (se implementar a sua aplicação em vários locais) Responder URIs, segredos ou certificados tem de ser partilhado com a Azure AD.
- A configuração da [Aplicação](scenario-web-app-call-api-app-configuration.md) precisa de fornecer credenciais de cliente partilhadas com a Azure AD durante o registo de candidaturas

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-app-call-api-app-registration.md)

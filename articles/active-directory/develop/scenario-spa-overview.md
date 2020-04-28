---
title: Cenário de aplicação de uma página única javaScript - plataforma de identidade da Microsoft / Azure
description: Saiba como construir uma aplicação de uma página única (visão geral do cenário) utilizando a plataforma de identidade da Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885179"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicação de página única

Saiba tudo o que precisa para construir uma aplicação de uma única página (SPA).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Pode criar a sua primeira aplicação seguindo o quickstart javaScript SPA:

> [!div class="nextstepaction"]
> [Quickstart: Aplicação de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Descrição geral

Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente. Os desenvolvedores escrevem-nos usando javaScript ou uma estrutura SPA como Angular, Vue.js e React.js. Estas aplicações funcionam num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor. 

A plataforma de identidade da Microsoft permite que as aplicações de uma só página sinuosem nos utilizadores e acedam a serviços de back-end ou APIs web utilizando o [fluxo implícito OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). O fluxo implícito permite que a aplicação obtenha fichas de identificação para representar o utilizador autenticado e também aceder a fichas necessárias para ligar para APIs protegidos.

![Aplicações de página única](./media/scenarios/spa-app.svg)

Este fluxo de autenticação não inclui cenários de aplicação que utilizam quadros javaScript de plataforma cruzada, tais como Eletrão e React-Native. Exigem mais capacidades de interação com as plataformas nativas.

## <a name="specifics"></a>Especificidades

Para ativar este cenário para a sua aplicação, precisa de:

* Inscrição de candidatura com Diretório Ativo Azure (Azure AD). Este registo envolve permitir o fluxo implícito e definir um URI redirecionado para o qual os tokens são devolvidos.
* Configuração da aplicação com as propriedades de aplicação registadas, como id de aplicação.
* Utilizando a Microsoft Authentication Library (MSAL) para fazer o fluxo de autenticação para iniciar sessão e adquirir fichas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-spa-app-registration.md)

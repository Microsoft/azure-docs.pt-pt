---
title: Cenário de aplicação de página única JavaScript - plataforma de identidade da Microsoft Rio Azure
description: Saiba como construir uma aplicação de uma página (visão geral do cenário) utilizando a plataforma de identidade da Microsoft.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885179"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicação de página única

Aprenda tudo o que precisa para construir uma aplicação de uma página (SPA).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Pode criar a sua primeira aplicação seguindo o arranque rápido do JavaScript SPA:

> [!div class="nextstepaction"]
> [Quickstart: Aplicação de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Descrição geral

Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente. Os desenvolvedores escrevem-nos utilizando o JavaScript ou uma estrutura SPA como angular, Vue.js e React.js. Estas aplicações são executadas num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor. 

A plataforma de identidade da Microsoft permite que aplicações de uma página única assinem nos utilizadores e obtenham fichas para aceder a serviços de back-end ou APIs web utilizando o [fluxo implícito OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). O fluxo implícito permite que a aplicação obtenha fichas de identificação para representar o utilizador autenticado e também acesso a fichas necessárias para chamar APIs protegidas.

![Aplicações de uma página única](./media/scenarios/spa-app.svg)

Este fluxo de autenticação não inclui cenários de aplicação que utilizem quadros javaScript de plataforma cruzada, tais como Electrn e React-Native. Exigem mais capacidades de interação com as plataformas nativas.

## <a name="specifics"></a>Especificidades

Para ativar este cenário para a sua aplicação, precisa:

* Inscrição de candidatura no Azure Ative Directory (Azure AD). Este registo envolve permitir o fluxo implícito e definir um URI de redirecionamento para o qual os tokens são devolvidos.
* Configuração de aplicação com as propriedades de aplicação registada, como iD de aplicação.
* Utilizar a Microsoft Authentication Library (MSAL) para fazer o fluxo de autenticação para iniciar súblio e adquirir fichas.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Registo de aplicações](scenario-spa-app-registration.md)

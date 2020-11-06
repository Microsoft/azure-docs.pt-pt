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
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 11a295402c925f5b8244eb3294097ff2271b5d68
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335269"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicação de página única

Aprenda tudo o que precisa para construir uma aplicação de uma página (SPA).

## <a name="getting-started"></a>Introdução

Se ainda não o fez, crie a sua primeira aplicação completando o arranque rápido javaScript SPA:

[Quickstart: Aplicação de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Descrição geral

Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente. Os desenvolvedores escrevem-nos utilizando o JavaScript ou uma estrutura SPA como Angular, Vue e React. Estas aplicações são executadas num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor.

A plataforma de identidade da Microsoft fornece **duas** opções para permitir que aplicações de uma página única assinem nos utilizadores e obtenham fichas para aceder a serviços de back-end ou APIs web:

- [OAuth 2.0 Fluxo de código de autorização (com PKCE)](./v2-oauth2-auth-code-flow.md). O fluxo de código de autorização permite que a aplicação troque um código de autorização para fichas **de identificação** para representar o utilizador autenticado e fichas de **acesso** necessárias para chamar APIs protegidas. Além disso, devolve tokens **Refresh** que fornecem acesso a longo prazo aos recursos em nome dos utilizadores sem necessidade de interação com esses utilizadores. Esta é a abordagem **recomendada.**

![Aplicações de uma só página-auth](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 fluxo implícito](./v2-oauth2-implicit-grant-flow.md). O fluxo de subvenção implícita permite que a aplicação obtenha **ID** e tokens de **acesso.** Ao contrário do fluxo de código de autorização, o fluxo de subvenção implícita não devolve um **token Refresh**.

![Aplicações de uma página única implícitas](./media/scenarios/spa-app.svg)

Este fluxo de autenticação não inclui cenários de aplicação que utilizem quadros javaScript de plataforma cruzada, tais como Electrn e React-Native. Exigem mais capacidades de interação com as plataformas nativas.

## <a name="specifics"></a>Especificidades

Para ativar este cenário para a sua aplicação, precisa:

* Inscrição de candidatura no Azure Ative Directory (Azure AD). As etapas de registo diferem entre o fluxo implícito de concessão e o fluxo de código de autorização.
* Configuração de aplicação com as propriedades de aplicação registada, como o ID da aplicação.
* Utilizar a Microsoft Authentication Library para JavaScript (MSAL.js) para fazer o fluxo de autenticação para iniciar súblio e adquirir fichas.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passos seguintes

[Registo de aplicações](scenario-spa-app-registration.md)

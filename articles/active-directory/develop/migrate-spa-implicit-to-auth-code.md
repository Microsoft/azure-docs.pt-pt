---
title: Migrar a aplicação de uma página única JavaScript de subvenção implícita para o fluxo de código de autorização | Rio Azure
titleSuffix: Microsoft identity platform
description: Como atualizar um Spa JavaScript utilizando MSAL.js 1.x e o fluxo implícito de concessão para MSAL.js 2.x e o fluxo de código de autorização com suporte PKCE e CORS.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3c11334fe1b4d77be6e64febfc1d3de6efa302c3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365945"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Migrar uma aplicação de página única JavaScript de subvenção implícita para fluxo de código de auth

A Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) v2.0 traz suporte para o fluxo de código de autorização com PKCE e CORS para aplicações de uma única página na plataforma de identidade da Microsoft. Siga os passos nas secções abaixo para migrar a sua aplicação MSAL.js 1.x utilizando a concessão implícita para MSAL.js 2.0+ (doravante *2.x)* e o fluxo de código de auth.

MSAL.js 2.x melhora em MSAL.js 1.x, suportando o fluxo de código de autorização no navegador em vez do fluxo de concessão implícito. MSAL.js 2.x **NÃO** suporta o fluxo implícito.

## <a name="migration-steps"></a>Passos de migração

Para atualizar a sua aplicação para MSAL.js 2.x e o fluxo de código de auth, existem três passos primários:

1. Mude o seu registo de [aplicações](#switch-redirect-uris-to-spa-platform) redirecionando URI(s) da plataforma **Web** para a plataforma **de aplicações de página única.**
1. Atualize o seu [código](#switch-redirect-uris-to-spa-platform) de MSAL.js 1.x a **2.x**.
1. Desativar a [subvenção implícita](#disable-implicit-grant-settings) no registo da sua aplicação quando todas as aplicações que partilham o registo tiverem sido atualizadas para MSAL.js 2.x e o fluxo de código auth.

As seguintes secções descrevem cada passo em detalhes adicionais.

## <a name="switch-redirect-uris-to-spa-platform"></a>Altere uris redirecionando para plataforma SPA

Se quiser continuar a utilizar o registo de aplicações existente para as suas aplicações, utilize o portal Azure para atualizar os URIs redirecionados do registo para a plataforma SPA. Ao fazê-lo, permite o fluxo de código de autorização com suporte A PKCE e CORS para aplicações que utilizam o registo (ainda é necessário atualizar o código da sua aplicação para MSAL.js v2.x).

Siga estes passos para registos de aplicações que estão atualmente configurados com URIs de redirecionamento de plataforma **Web:**

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal Azure</a> e selecione o seu inquilino **Azure Ative Directory.**
1. Nas **inscrições da App,** selecione a sua candidatura e, em seguida, **autenticação.**
1. No azulejo da plataforma **Web** em **Redirecionar URIs,** selecione o banner de aviso indicando que deve migrar os uris.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Banner de aviso de fluxo implícito no azulejo da aplicação web no portal Azure":::
1. Selecione *apenas* os URIs de redirecionamento cujas aplicações utilizarão MSAL.js 2.x e, em seguida, selecione **Configure**.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Selecione redirecionar o painel URI no painel DE SPA no portal Azure":::

Estes URIs de redireccionamento devem agora aparecer no azulejo da plataforma **de aplicação de página única,** mostrando que o suporte do CORS com o fluxo de código de autorização e pkce está ativado para estes URIs.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Azulejo de aplicação de uma página no registo de aplicações no portal Azure":::

Também pode [criar um novo registo de aplicações](scenario-spa-app-registration.md) em vez de atualizar os URIs redirecionados no seu registo existente.

## <a name="update-your-code-to-msaljs-2x"></a>Atualize o seu código para MSAL.js 2.x

No MSAL 1.x, criou uma instância de aplicação inicializando uma [Aplicação userAgent da][msal-js-useragentapplication] seguinte forma:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

No MSAL 2.x, inicialize em vez de um [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Para obter um walk-through completo da adição de MSAL 2.x à sua aplicação, consulte [Tutorial: Inscreva-se nos utilizadores e ligue para a API do Microsoft Graph a partir de uma aplicação de página única JavaScript (SPA) utilizando o fluxo de código auth](tutorial-v2-javascript-auth-code.md).

Para alterações adicionais que poderá ter de fazer ao seu código, consulte o [guia de migração](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) no GitHub.

## <a name="disable-implicit-grant-settings"></a>Desativar as definições implícitas de subvenções

Uma vez atualizado todas as suas aplicações de produção que utilizam este registo de aplicações e o seu ID do cliente para MSAL 2.x e o fluxo de código de autorização, deverá desmarcar as definições implícitas de subvenção no registo da aplicação.

Quando desmarque as definições implícitas de subvenção no registo da aplicação, o fluxo implícito é desativado para todas as aplicações utilizando o registo e o seu ID do cliente.

**Não** desative o fluxo de subvenção implícito antes de atualizar todas as suas aplicações para MSAL.js 2.x e a [PublicClientApplication][msal-js-publicclientapplication].

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o fluxo de código de autorização, incluindo as diferenças entre os fluxos implícitos e de código de auth, consulte a plataforma de identidade da Microsoft e o [fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

Se quiser mergulhar mais profundamente no desenvolvimento de aplicações de página única javaScript na plataforma de identidade da Microsoft, o cenário multi-partes: série de aplicações de uma página única pode [ajudá-lo](scenario-spa-overview.md) a começar.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html

---
title: Registar aplicações de uma só página (SPA) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de uma página (registo de aplicações)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9366bb5b2bb5820245ec1b699bbf2ddda0dd9f9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103180"
---
# <a name="single-page-application-app-registration"></a>Aplicação de uma página única: Registo de aplicações

Para registar uma aplicação de uma página (SPA) na plataforma de identidade da Microsoft, complete os seguintes passos. As etapas de registo diferem entre MSAL.js 1.0, que suporta o fluxo implícito de subvenções, e MSAL.js 2.0, que suporta o fluxo de código de autorização com o PKCE.

## <a name="create-the-app-registration"></a>Criar o registo da aplicação

Para aplicações com base em MSAL.js 1.0 e 2.0, comece por completar as seguintes etapas para criar o registo inicial da aplicação.

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Escolha os **tipos de conta suportados** para a aplicação. **NÃO** introduza um **URI de redirecionamento**. Para obter uma descrição dos diferentes tipos de conta, consulte o [Registo de um pedido](quickstart-register-app.md).
1. Selecione **Registar-se** para criar o registo da aplicação.

Em seguida, configurar o registo da aplicação com um **Redirect URI** para especificar onde a plataforma de identidade da Microsoft deve redirecionar o cliente juntamente com quaisquer fichas de segurança. Utilize os passos adequados para a versão de MSAL.js que está a utilizar na sua aplicação:

- [MSAL.js 2.0 com fluxo de código de auth](#redirect-uri-msaljs-20-with-auth-code-flow) (recomendado)
- [MSAL.js 1.0 com fluxo implícito](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Redirecionamento URI: [MSAL.js 2.0 com fluxo de código auth](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Siga estes passos para adicionar um URI de redirecionamento para uma aplicação que utiliza MSAL.js 2.0 ou mais tarde. MSAL.js 2.0+ suporta o fluxo de código de autorização com PKCE e CORS em resposta às restrições de [cookies de terceiros do navegador](reference-third-party-cookies-spas.md). O fluxo de subvenção implícita não é suportado em MSAL.js 2.0+.

1. No portal Azure, selecione o registo de aplicações que criou anteriormente no [Registo da aplicação.](#create-the-app-registration)
1. Em **Gestão**, **selecione Autenticação**  >  **Adicione uma plataforma**.
1. Nas **aplicações Web,** selecione o azulejo **de aplicação de página única.**
1. Em **Redirecionar URIs**, [insira um URI de redirecionamento](reply-url.md). **NÃO** selecione uma caixa de verificação sob **subvenção implícita e fluxos híbridos**.
1. Selecione **Configurar** para terminar a adição do URI de redirecionamento.

Já concluiu o registo da sua aplicação de uma página única (SPA) e configura um URI de redirecionamento para o qual o cliente será redirecionado e quaisquer fichas de segurança serão enviadas. Ao configurar o seu URI de redirecionamento utilizando o azulejo **de aplicação de página única** no painel de plataforma Add **a,** o seu registo de candidatura está configurado para suportar o fluxo de código de autorização com PKCE e CORS.

Siga o [tutorial](tutorial-v2-javascript-auth-code.md) para mais orientação.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Redirecionamento URI: [MSAL.js 1.0 com fluxo implícito](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Siga estes passos para adicionar um URI de redirecionamento para uma aplicação de uma página que utiliza MSAL.js 1.3 ou mais cedo e o fluxo de concessão implícito. As aplicações que utilizam MSAL.js 1.3 ou mais cedo não suportam o fluxo de código auth.

1. No portal Azure, selecione o registo de aplicações que criou anteriormente no [Registo da aplicação.](#create-the-app-registration)
1. Em **Gestão**, **selecione Autenticação**  >  **Adicione uma plataforma**.
1. Nas **aplicações Web,** selecione **azulejo de aplicação de página única.**
1. Em **Redirecionar URIs**, [insira um URI de redirecionamento](reply-url.md).
1. Permitir a **concessão implícita e os fluxos híbridos:**
    - Se a sua aplicação assinar nos utilizadores, selecione **fichas de identificação**.
    - Se a sua aplicação também precisar de chamar uma API web protegida, selecione **Tokens de acesso**. Para obter mais informações sobre estes tipos [simbólicos, consulte fichas de identificação](id-tokens.md) e [fichas de acesso.](access-tokens.md)
1. Selecione **Configurar** para terminar a adição do URI de redirecionamento.

Já concluiu o registo da sua aplicação de uma página única (SPA) e configura um URI de redirecionamento para o qual o cliente será redirecionado e quaisquer fichas de segurança serão enviadas. Ao selecionar um ou ambos os **tokens** de ID e **tokens de acesso,** você ativou o fluxo de subvenção implícita.

Siga o [tutorial](tutorial-v2-javascript-spa.md) para mais orientação.

## <a name="note-about-authorization-flows"></a>Nota sobre fluxos de autorização

Por predefinição, um registo de aplicações criado através da configuração da plataforma de aplicação de uma página única permite o fluxo de código de autorização. Para tirar partido deste fluxo, a sua aplicação deve utilizar MSAL.js 2.0 ou posterior.

Como mencionado anteriormente, as aplicações de uma página única que utilizam MSAL.js 1.3 estão restritas ao fluxo implícito de subvenção. As [boas práticas atuais do OAuth 2.0](v2-oauth2-auth-code-flow.md) recomendam a utilização do fluxo de código de autorização em vez do fluxo implícito para as A SPAs. Ter tokens de atualização de vida limitada também ajuda a sua aplicação a adaptar-se às [limitações modernas](reference-third-party-cookies-spas.md)de privacidade de cookies do navegador , como o Safari ITP.

Quando todas as aplicações de produção de uma página única representadas por um registo de aplicações estiverem a utilizar MSAL.js 2.0 e o fluxo de código de autorização, desmarque as definições implícitas do painel de **autenticação** do registo da aplicação no portal Azure. As aplicações que utilizam MSAL.js 1.x e o fluxo implícito podem continuar a funcionar, no entanto, se deixar o fluxo implícito ativado (verificado).

## <a name="next-steps"></a>Passos seguintes

Configure o código da sua aplicação para utilizar o registo de aplicações que criou nos passos anteriores: [configuração de código da App](scenario-spa-app-configuration.md).

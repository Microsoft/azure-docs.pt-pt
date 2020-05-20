---
title: Registe candidaturas de uma página única (SPA) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de uma única página (registo de aplicações)
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
ms.openlocfilehash: 9dc5b446e2ab26ca43c2a300e1af1237353325a3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682388"
---
# <a name="single-page-application-app-registration"></a>Aplicação de página única: Registo de aplicativos

Para registar uma aplicação de uma única página (SPA) na plataforma de identidade da Microsoft, complete os seguintes passos. As etapas de registo diferem entre mSAL.js 1.0, que suporta o fluxo implícito de subvenção, e MSAL.js 2.0, que suporta o fluxo de código de autorização com pKCE.

## <a name="create-the-app-registration"></a>Criar o registo da aplicação

Para aplicações baseadas em MSAL.js 1.0 e 2.0, comece por completar os seguintes passos para criar o registo inicial da aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se a sua conta tiver acesso a vários inquilinos, selecione o filtro **de subscrição Do Diretório +** No menu superior e, em seguida, selecione o inquilino que deve conter o registo da aplicação que está prestes a criar.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gerir**, selecione **Registos de aplicações**.
1. Selecione **Novo registo,** insira um **Nome** para a aplicação e escolha os tipos de **conta Suportada** para a aplicação. **NÃO** introduza um **Uri redirecionamento**. Para obter uma descrição dos diferentes tipos de conta, consulte o [Registo uma nova aplicação utilizando o portal Azure](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).
1. Selecione **Register** para criar o registo da aplicação.

Em seguida, configure o registo da aplicação com um **Redirect URI** para especificar onde a plataforma de identidade da Microsoft deve redirecionar o cliente juntamente com quaisquer fichas de segurança. Utilize os passos adequados para a versão de MSAL.js que está a utilizar na sua aplicação:

- [MSAL.js 2.0 com fluxo de código auth](#redirect-uri-msaljs-20-with-auth-code-flow) (recomendado)
- [MSAL.js 1.0 com fluxo implícito](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Redirecionamento URI: MSAL.js 2.0 com fluxo de código auth

Siga estes passos para adicionar um URI redirecionado para uma aplicação que utiliza MSAL.js 2.0 ou mais tarde. MSAL.js 2.0+ suporta o fluxo de código de autorização com PKCE e CORS em resposta às restrições de cookies de terceiros do [navegador.](reference-third-party-cookies-spas.md) O fluxo implícito de subvenção não é suportado em MSAL.js 2.0+.

1. No portal Azure, selecione o registo da aplicação que criou anteriormente no [Registo da aplicação](#create-the-app-registration).
1. Em **'Gerir',** **selecione Autenticação**, e depois selecione **Adicionar uma plataforma**.
1. Em **aplicações Web,** selecione o azulejo **de aplicação de página única.**
1. Em **Redirecione as URIs,** introduza um [URI redireccionador](reply-url.md). **NÃO** selecione qualquer caixa de verificação sob **subvenção implícita**.
1. **Selecione Configurar** para terminar adicionando o URI redirecionamento.

Já concluiu o registo da sua aplicação de uma página única (SPA) e configurou um URI redirecionado para o qual o cliente será redirecionado e quaisquer fichas de segurança serão enviadas. Ao configurar o seu URI redirecionamento utilizando o azulejo **de aplicação de uma página única** no painel de plataforma Adicionar uma **plataforma,** o registo da sua aplicação está configurado para suportar o fluxo de código de autorização com PKCE e CORS.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Redirecionamento URI: MSAL.js 1.0 com fluxo implícito

Siga estes passos para adicionar um URI redirecionado para uma aplicação de uma única página que utiliza MSAL.js 1.3 ou anterior e o fluxo implícito de subvenção. As aplicações que utilizam MSAL.js 1.3 ou anteriores não suportam o fluxo de código auth.

1. No portal Azure, selecione o registo da aplicação que criou anteriormente no [Registo da aplicação](#create-the-app-registration).
1. Em **'Gerir',** **selecione Autenticação**, e depois selecione **Adicionar uma plataforma**.
1. Em **aplicações Web,** selecione azulejode **aplicação de página única.**
1. Em **Redirecione as URIs,** introduza um [URI redireccionador](reply-url.md).
1. Ativar o **fluxo implícito:**
    - Se a sua aplicação assinar nos utilizadores, selecione **fichas de identificação**.
    - Se a sua aplicação também necessitar de chamar uma API web protegida, selecione **fichas**de acesso . Para obter mais informações sobre estes tipos de [fichas, consulte fichas de identificação](id-tokens.md) e [fichas](access-tokens.md)de acesso .
1. **Selecione Configurar** para terminar adicionando o URI redirecionamento.

Já concluiu o registo da sua aplicação de uma página única (SPA) e configurou um URI redirecionado para o qual o cliente será redirecionado e quaisquer fichas de segurança serão enviadas. Ao selecionar um ou ambos os **tokens** de ID e fichas de **acesso,** permitiu o fluxo implícito de subvenção.

## <a name="note-about-authorization-flows"></a>Nota sobre fluxos de autorização

Por padrão, um registo de aplicação criado através da configuração da plataforma de aplicação de uma página única permite o fluxo de código de autorização. Para aproveitar este fluxo, a sua aplicação deve utilizar MSAL.js 2.0 ou mais tarde.

Como mencionado anteriormente, as aplicações de uma só página utilizando MSAL.js 1.3 limitam-se ao fluxo implícito de subvenção. As [atuais boas práticas da OAuth 2.0](v2-oauth2-auth-code-flow.md) recomendam a utilização do fluxo do código de autorização em vez do fluxo implícito para as SPAs. Ter fichas de atualização de vida limitada também ajuda a sua aplicação a adaptar-se às [limitações modernas](reference-third-party-cookies-spas.md)de privacidade dos cookies do navegador , como o Safari ITP.

Quando todas as suas aplicações de uma única página de produção representadas por um registo de aplicações estiverem a utilizar mSAL.js 2.0 e o fluxo de código de autorização, desmarque as definições implícitas da subvenção do painel de autenticação do registo da **aplicação** no portal Azure. As aplicações que utilizam mSAL.js 1.x e o fluxo implícito podem continuar a funcionar, no entanto, se deixar o fluxo implícito ativado (verificado).

## <a name="next-steps"></a>Passos seguintes

Em seguida, configure o código da sua aplicação para utilizar o registo da aplicação que criou nos passos anteriores:.

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-spa-app-configuration.md)

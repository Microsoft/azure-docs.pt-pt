---
title: Mover app de uma página para a produção
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de uma página (passe para a produção)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0c1e9e6da02478f5e4703676b74fc8247a4f619b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753522"
---
# <a name="single-page-application-move-to-production"></a>Aplicação de uma página: Mover-se para a produção

Agora que sabe como adquirir um símbolo para chamar APIs web, aprenda a mudar-se para a produção.

## <a name="improve-your-app"></a>Melhore a sua aplicação

[Ativar](msal-logging.md) o registo para preparar a produção da sua aplicação.

## <a name="test-your-integration"></a>Teste a sua integração

Teste a sua integração seguindo a lista de verificação de [integração da plataforma de identidade da Microsoft.](identity-platform-integration-checklist.md)

## <a name="deploy-your-app"></a>Implemente a sua aplicação

Consulte uma [amostra de implementação](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) para aprender a implementar os seus projetos DE SPA e Web API com Azure Storage e Azure App Services, respectivamente. 

## <a name="next-steps"></a>Próximos passos

- Mergulho profundo da amostra quickstart, que explica o código de como iniciar sing nos utilizadores e obter um token de acesso para ligar para a **Microsoft Graph API** utilizando **MSAL.js**: [Tutorial JavaScript SPA](./tutorial-v2-javascript-spa.md).

- Amostra que demonstra como obter fichas para a sua própria API web de back-end (ASP.NET Core) utilizando **MSAL.js**: [SPA com um back-end ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi).

- Amostra que demonstra como validar fichas de acesso para a sua API web de back-end (Node.js) utilizando **o passaporte-azure-ad**: [Node.js Web API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2).

- Amostra que mostra como usar **MSAL.js** para assinar nos utilizadores numa aplicação registada no **Azure Ative Directory B2C** (Azure AD B2C): [SPA com Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- Amostra que mostra como usar **o anúncio de passaporte-azure-ad** para validar fichas de acesso para aplicações registadas no **Azure Ative Directory B2C** (Azure AD B2C): [Node.js Web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

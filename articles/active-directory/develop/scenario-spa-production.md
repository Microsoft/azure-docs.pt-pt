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
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954970"
---
# <a name="single-page-application-move-to-production"></a>Aplicação de uma página: Mover-se para a produção

Agora que sabe como adquirir um token para chamar APIs web, aqui ficam algumas coisas a considerar ao mover a sua aplicação para a produção.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Implemente a sua aplicação

Consulte uma [amostra de implementação](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) para aprender a implementar os seus projetos DE SPA e Web API com Azure Storage e Azure App Services, respectivamente. 

## <a name="code-samples"></a>Exemplos de código

Estas amostras de código demonstram várias operações-chave para uma aplicação de uma página.
- [SPA com um back-end ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): Como obter fichas para a sua própria API web de back-end (ASP.NET Core) utilizando **MSAL.js**.

- [Node.js Web API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): Como validar fichas de acesso para a sua API web de back-end (Node.js) utilizando **o passport-azure-ad**.

- [SPA com Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): Como usar **MSAL.js** para assinar nos utilizadores numa aplicação registada no **Azure Ative Directory B2C** (Azure AD B2C).

- [Node.js Web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): Como utilizar **o anúncio de passaporte-azure-azure para** validar fichas de acesso para aplicações registadas no **Azure Ative Directory B2C** (Azure AD B2C).

## <a name="next-steps"></a>Passos seguintes

- [Tutorial JavaScript SPA](./tutorial-v2-javascript-spa.md): Mergulho profundo em como iniciar seduca nos utilizadores e obter um token de acesso para ligar para a API do **Microsoft Graph** utilizando **MSAL.js**.
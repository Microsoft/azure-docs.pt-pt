---
title: Aplicação de página única (passar para produção) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (passar para produção)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075014"
---
# <a name="single-page-application---move-to-production"></a>Aplicação de página única - mover para produção

Agora que sabe como adquirir um token para chamar as APIs da Web, saiba como mover para produção.

## <a name="improve-your-app"></a>Melhorar a sua aplicação

Siga os passos necessários para tornar sua produção de aplicação pronto.

- [Ativar o registo](msal-logging.md) em seu aplicativo.

## <a name="test-your-integration"></a>Testar a sua integração

- Testar a sua integração ao seguir a [lista de verificação do Microsoft identity platform integração](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Passos Seguintes

Aqui estão alguns outros exemplos/tutoriais:

- Para uma descrição aprofundada do início rápido de exemplo que explica o código para saber como iniciar sessão dos utilizadores e obter um token de acesso para chamar a API de gráfico de MS com msal

    > [!div class="nextstepaction"]
    > [Tutorial do SPA do JavaScript](./tutorial-v2-javascript-spa.md)

- Exemplo que demonstra como obter tokens para seu próprio web API de back-end com msal

     > [!div class="nextstepaction"]
     > [SPA com um back-end ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Exemplo para mostrar como utilizar a msal para iniciar sessão dos utilizadores numa aplicação registada no Azure AD B2C

    > [!div class="nextstepaction"]
    > [SPA com o Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

---
title: Mova a aplicação web que assina nos utilizadores para a produção - plataforma de identidade Microsoft / Azure
description: Saiba como construir uma aplicação web que assina nos utilizadores (mude para a produção)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88ad32a289d499cd385f8f3095113a4b329b80f4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701540"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplicação web que assina nos utilizadores: Mover-se para a produção

Agora que sabes como obter um símbolo para ligar para a Web APIs, aprende a movê-lo para a produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

### <a name="scenario-for-calling-web-apis"></a>Cenário para chamar APIs web

Depois de a sua aplicação web ter assinado nos utilizadores, pode ligar para a Web APIs em nome dos utilizadores inscritos. Chamar APIs web da aplicação web é o objeto do seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicação Web que chama as APIs Web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Mergulho profundo: tutorial de aplicação web ASP.NET Core

Saiba mais sobre outras formas de assinar nos utilizadores com este tutorial ASP.NET Core: 

> [!div class="nextstepaction"]
> [Ative as suas aplicações web para iniciar sessão de utilizadores e ligue para APIs com a plataforma de identidade da Microsoft para programadores](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Este tutorial progressivo tem código pronto para a produção para uma aplicação web, incluindo como adicionar sessão com contas em:

- A sua organização
- Múltiplas organizações
- Trabalho ou contas escolares, ou contas pessoais da Microsoft
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nuvens nacionais

### <a name="sample-code-java-web-app"></a>Código da amostra: Java web app

Saiba mais sobre a aplicação web java a partir desta amostra no GitHub: 

> [!div class="nextstepaction"]
> [Uma aplicação Java Web que assina em utilizadores com a plataforma de identidade da Microsoft e chama microsoft graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

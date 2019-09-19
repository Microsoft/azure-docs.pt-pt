---
title: Aplicativo Web que faz logon de usuários (mover para produção)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que faz logon de usuários (mover para produção)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086556"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Aplicativo Web que entra em usuários – mover para produção

Agora que você sabe como adquirir um token para chamar APIs da Web, saiba como movê-la para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos Seguintes

### <a name="calling-web-apis-scenario"></a>Chamando cenário de APIs Web

Depois que o aplicativo Web entrar em usuários, ele poderá chamar APIs da Web em nome dos usuários conectados. Chamar APIs da Web do aplicativo Web é o objeto do seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicação Web que chama as APIs Web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Aprofundamento – tutorial do aplicativo Web ASP.NET Core

Saiba mais sobre outras maneiras de usuários de entrada com o tutorial de ASP.NET Core: [MS-Identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Este exemplo é um tutorial progressivo com código pronto para produção para um aplicativo Web, incluindo como adicionar entrada com contas no:

- sua organização,
- várias organizações,
- contas corporativas ou de estudante ou conta Microsoft pessoais,
- com [Azure ad B2C](https://aka.ms/aadb2c),
- ou em nuvens nacionais.

### <a name="sample-code---java-web-app"></a>Código de exemplo – aplicativo Web Java

Saiba mais sobre o aplicativo Web Java do exemplo no GitHub: [Um aplicativo Web Java que conecta usuários com a plataforma de identidade da Microsoft e chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

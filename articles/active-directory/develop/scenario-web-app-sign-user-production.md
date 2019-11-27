---
title: Aplicativo Web que conecta os usuários (mover para produção)-plataforma de identidade da Microsoft
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
ms.openlocfilehash: efcc7ed4f80d0e3e9750d19ff95d010052a08b87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481889"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplicativo Web que conecta usuários: mover para produção

Agora que você sabe como obter um token para chamar APIs da Web, saiba como movê-la para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos Seguintes

### <a name="scenario-for-calling-web-apis"></a>Cenário para chamar APIs Web

Depois que seu aplicativo Web entra em usuários, ele pode chamar APIs da Web em nome dos usuários conectados. Chamar APIs da Web do aplicativo Web é o objeto do seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicação Web que chama as APIs Web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Aprofunde-se: tutorial do aplicativo Web ASP.NET Core

Saiba mais sobre outras maneiras de conectar usuários com este ASP.NET Core tutorial: 

> [!div class="nextstepaction"]
> [Habilite seus aplicativos Web para conectar usuários e chamar APIs com a plataforma de identidade da Microsoft para desenvolvedores](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Este tutorial progressivo tem código pronto para produção para um aplicativo Web, incluindo como adicionar entrada com contas no:

- Sua organização
- Várias organizações
- Contas corporativas ou de estudante ou contas pessoais da Microsoft
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nuvens nacionais

### <a name="sample-code-java-web-app"></a>Código de exemplo: aplicativo Web Java

Saiba mais sobre o aplicativo Web Java deste exemplo no GitHub: 

> [!div class="nextstepaction"]
> [Um aplicativo Web Java que conecta usuários com a plataforma de identidade da Microsoft e chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

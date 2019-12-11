---
title: Mover o aplicativo Web que conecta os usuários à produção – plataforma de identidade da Microsoft | Azure
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
ms.openlocfilehash: c49782a6e1e86320b508875e2bf931cc1cc19b4e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964775"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplicativo Web que conecta usuários: mover para produção

Agora que você sabe como obter um token para chamar APIs da Web, saiba como movê-la para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

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

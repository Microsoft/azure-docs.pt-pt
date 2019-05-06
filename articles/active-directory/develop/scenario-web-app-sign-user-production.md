---
title: Aplicação Web que inicia sessão os usuários (passar para produção) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação web que inicia sessão dos utilizadores (passar para produção)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074714"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Aplicação Web que inicia sessão utilizadores - mover para produção

Agora que sabe como adquirir um token para chamar as APIs web, saiba como movê-lo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos Seguintes

### <a name="calling-web-apis-scenario"></a>Cenário APIs de web de chamar

Uma vez web inicia sessão utilizadores da sua aplicação, pode chamar as APIs web em nome dos utilizadores com sessão iniciada. Chamar APIs para a web da aplicação web é o objeto o seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicação Web que chamadas de APIs web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Aprofunde-se-tutorial da aplicação web

Saiba mais sobre outras formas de utilizadores de início de sessão com o tutorial de ASP.NET Core: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Este é um tutorial progressivo com o código de produção pronto para uma aplicação web, incluindo como adicionar o início de sessão.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->

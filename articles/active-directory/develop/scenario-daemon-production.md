---
title: Aplicativo de daemon chamando APIs Web (mover para produção)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama APIs da Web (mover para produção)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05418cde7b31392c1a55f64945764e978daba1bf
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175406"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplicativo daemon que chama APIs da Web – mover para produção

Agora que você sabe como adquirir e usar um token para uma chamada de serviço a serviço, saiba como mover seu aplicativo para produção.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Implantação-caso de aplicativos de daemon multilocatário

Se você for um ISV criando um aplicativo daemon que pode ser executado em vários locatários, precisará certificar-se de que os administradores de locatário:

- Provisiona uma entidade de serviço para o aplicativo
- Concede consentimento ao aplicativo

Você precisará explicar aos seus clientes como executar essas operações. Para obter mais informações, consulte [solicitando consentimento para um locatário inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

Aqui estão alguns links para saber mais:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Se você ainda não fez isso, experimente o início rápido [adquire um token e chame Microsoft Graph API de um aplicativo de console usando a identidade do aplicativo](./quickstart-v2-netcore-daemon.md).
- Documentação de referência para:
  - Instanciando [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Chamando [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Outros exemplos/tutoriais:
  - [Microsoft-Identity-Platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) apresenta um simples aplicativo de console do daemon do .NET Core que exibe os usuários de um locatário consultando o Microsoft Graph.

    ![topology](media/scenario-daemon-app/daemon-app-sample.svg)

    O mesmo exemplo também ilustra a variação com certificados.

    ![topology](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) apresenta um aplicativo Web ASP.NET MVC que sincroniza dados de Microsoft Graph usando a identidade do aplicativo em vez de em nome de um usuário. O exemplo também ilustra o processo de consentimento do administrador.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console do Python usando a identidade do aplicativo](./quickstart-v2-python-daemon.md).

# <a name="javatabjava"></a>[Java](#tab/java)

O MSAL Java está atualmente em visualização pública. Para obter mais informações, consulte [exemplos de desenvolvimento do MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---

---
title: Mova uma app daemon que chama APIs web para produção | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como mover uma app daemon que chama APIs web à produção
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d903f04055d1607ee782bd502d99a8fd9cde87ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578452"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon app que chama APIs web - passe para a produção

Agora que sabe adquirir e usar um símbolo para uma chamada de serviço ao serviço, aprenda a mover a sua app para a produção.

## <a name="deployment---multitenant-daemon-apps"></a>Implementação - aplicativos multitenant Daemon

Se você é um ISV criando uma aplicação daemon que pode funcionar em vários inquilinos, certifique-se de que o administrador do inquilino:

- Fornece um principal de serviço para a aplicação.
- Concede consentimento ao pedido.

Terá de explicar aos seus clientes como realizar estas operações. Para mais informações, consulte [Solicitação de Consentimento para um inquilino inteiro.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Exemplos de código

# <a name="net"></a>[.NET](#tab/dotnet)

- Documentação de referência para:
  - ConfidencialIzação [ConfidencialApplicação](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)de Pensão .
  - Chamando [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Outras amostras/tutoriais:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) apresenta uma pequena aplicação de consola .NET Core daemon que exibe os utilizadores de um inquilino consultando o Microsoft Graph.

    ![Topologia da app daemon amostra](media/scenario-daemon-app/daemon-app-sample.svg)

    A mesma amostra ilustra ainda uma variação com certificados:

    ![Topologia da app daemon - certificados](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) apresenta uma aplicação web MVC ASP.NET que sincroniza dados do Microsoft Graph utilizando a identidade da aplicação em vez de em nome de um utilizador. Esta amostra também ilustra o processo de consentimento administrativo.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

Experimente o quickstart [Adquirir um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Java usando a identidade da aplicação](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Para obter mais informações, consulte:
  - Compreensão [da configuração](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)
  - Confidencialização [ConfidencialApplicação](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)
  - [FAQ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- Outras amostras/tutoriais:
  - [Amostra de daemon da consola MSAL](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

Experimente o quickstart [Adquirir um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Python usando a identidade da aplicação.](quickstart-v2-python-daemon.md)

---

## <a name="next-steps"></a>Passos seguintes

Aqui estão alguns links para ajudá-lo a aprender mais:

# <a name="net"></a>[.NET](#tab/dotnet)

Experimente o quickstart [Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola .NET Core utilizando a identidade da aplicação](quickstart-v2-netcore-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Experimente o quickstart [Adquirir um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Java usando a identidade da aplicação](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Experimente o quickstart [Adquirir um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Node.js usando a identidade da app.](quickstart-v2-nodejs-console.md)

# <a name="python"></a>[Python](#tab/python)

Experimente o quickstart [Adquirir um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Python usando a identidade da aplicação.](quickstart-v2-python-daemon.md)

---

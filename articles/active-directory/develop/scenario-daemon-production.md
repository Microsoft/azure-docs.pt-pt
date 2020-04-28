---
title: Mova uma app daemon que chama APIs web para a produção - plataforma de identidade Microsoft / Azure
description: Saiba como mover uma app daemon que chama APIs web para a produção
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
ms.openlocfilehash: e5d25169aa3daee33a9f02e605cbff91776dc7f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885434"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>App Daemon que chama APIs web - mude para a produção

Agora que sabe adquirir e usar um símbolo para uma chamada de serviço ao serviço, aprenda a mover a sua app para a produção.

## <a name="deployment---multitenant-daemon-apps"></a>Implementação - aplicações de daemon multiarrendatário

Se você é um ISV criando um pedido de daemon que pode ser executado em vários inquilinos, você precisa ter certeza de que o administrador inquilino:

- Provisões como diretor de serviço para a aplicação.
- Concede o consentimento ao pedido.

Terá de explicar aos seus clientes como realizar estas operações. Para mais informações, consulte [Pedir consentimento para um inquilino inteiro.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

Aqui estão alguns links para ajudá-lo a aprender mais:

# <a name="net"></a>[.NET](#tab/dotnet)

- Quickstart: [Adquira um símbolo e ligue para o Microsoft Graph API a partir de uma aplicação](./quickstart-v2-netcore-daemon.md)de consola utilizando a identidade da aplicação .
- Documentação de referência para:
  - Instantaneamente [A Aplicação Confidencial do Cliente](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Chamando [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Outras amostras/tutoriais:
  - [Microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) apresenta uma aplicação simples de consola da ememon .NET Core que exibe os utilizadores de um inquilino consultando o Microsoft Graph.

    ![Topologia da aplicação daemon da amostra](media/scenario-daemon-app/daemon-app-sample.svg)

    A mesma amostra ilustra também uma variação com certificados:

    ![Topologia de aplicações daemon da amostra - certificados](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) apresenta uma aplicação web ASP.NET MVC que sincroniza dados do Microsoft Graph utilizando a identidade da aplicação em vez de em nome de um utilizador. Esta amostra também ilustra o processo de consentimento da administração.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Experimente o quickstart [Adquira um token e ligue para o Microsoft Graph API a partir de uma aplicação](./quickstart-v2-python-daemon.md)de consola Python usando a identidade da app .

# <a name="java"></a>[Java](#tab/java)

A MSAL Java está atualmente em pré-visualização pública. Para mais informações, consulte [as amostras de dev da MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)

---

---
title: APIs (passar para produção) - de web de chamar de aplicação de daemon, plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de daemon que chamadas de web APIs (passar para produção)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545408"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplicação de daemon que chama o web APIs - mover para produção

Agora que sabe como adquirir e utilizar um token para uma chamada de serviços, saiba como mover a sua aplicação para produção.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Implementação - caso de aplicações de daemon de multi-inquilino

Se for um ISV, criando um aplicativo de daemon que pode ser executadas em vários inquilinos, terá de certificar-se de que os administradores de inquilinos:

- Aprovisiona um principal de serviço para a aplicação
- Concessões de autorizam a aplicação

Precisará explicar aos seus clientes, como executar estas operações. Para mais informações, veja [pedir consentimento para um inquilino todo](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos Seguintes

Seguem-se algumas ligações para saber mais:

### <a name="net"></a>.NET

- Se ainda não tiver, tente o início rápido [adquirir um token e chamar o Microsoft Graph API a partir de uma aplicação de consola com a identidade da aplicação](./quickstart-v2-netcore-daemon.md).
- Documentação de referência para:
  - Criar uma instância [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Chamar [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Outros exemplos/tutoriais:
  - [Microsoft-identity-plataforma-consola-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) apresenta um aplicativo de console daemon simple .NET Core apresenta os utilizadores de um inquilino consultar o Microsoft Graph.

    ![topology](media/scenario-daemon-app/daemon-app-sample.svg)

    Do mesmo exemplo também ilustra a variação com certificados.

    ![topology](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-WebApp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) apresenta um aplicativo da web de ASP.NET MVC que se sincroniza os dados do Microsoft Graph com a identidade da aplicação em vez de nome de um utilizador. O exemplo também ilustra o processo de consentimento de administrador.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

Python de MSAL está atualmente em pré-visualização pública. Para mais informações, veja [exemplo de no repositório de credenciais de cliente de Python de MSAL](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

Python de MSAL está atualmente em pré-visualização pública. Para mais informações, veja [exemplos de no repositório de Java de MSAL](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).
---
title: O daemon aplicação chamadas das APIs web (chamadas das APIs web) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de daemon que chamadas de web APIs (chamadas das APIs web)
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076274"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplicação de daemon que chamadas de web APIs - chamar uma API web a partir da aplicação

Uma aplicação de daemon pode chamar uma API web a partir de uma aplicação de daemon de .NET ou chamar as APIs web pré-aprovados vários.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Chamar uma API web a partir de uma aplicação de daemon de .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Chamar várias APIs

Para aplicações de daemon, APIs que chamar web tem de ser aprovado previamente. Não haverá qualquer consentimento incremental com aplicações de daemon (não há nenhuma interação do usuário). O administrador de inquilino tem previamente consentir a aplicação e todas as permissões de API. Se deseja chamar várias APIs, terá de adquirir um token para cada recurso, cada chamada de tempo `AcquireTokenForClient`. A MSAL irá utilizar a cache de token de aplicação para evitar chamadas de serviço desnecessários.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicação de daemon - mover para produção](./scenario-daemon-production.md)
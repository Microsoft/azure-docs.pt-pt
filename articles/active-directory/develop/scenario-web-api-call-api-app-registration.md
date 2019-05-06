---
title: API web a jusante esse chamadas APIs (registo da aplicação) - a plataforma de identidade do Microsoft Web
description: Saiba como criar uma web API web a jusante esse chamadas APIs (registo da aplicação)
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075389"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>API que chamadas de web APIs - registo de aplicações Web

Uma API de web em que chama a jusante APIs da web tem o mesmo registo como uma API web protegida. Por conseguinte, terá de seguir as instruções em [API Web protegida - registo de aplicações](scenario-protected-web-api-app-registration.md).

No entanto, desde a aplicação web agora chamadas de web APIs, torna-se uma aplicação de cliente confidencial. É por isso que há informações de registo extra necessário: a aplicação precisa compartilhar segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Aplicativos Web chamam APIs em nome do utilizador para o qual o token de portador foi recebido. Eles precisam solicitar permissões delegadas. Para obter detalhes, consulte [adicionar permissões para aceder a web APIs](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-web-api-call-api-app-configuration.md)

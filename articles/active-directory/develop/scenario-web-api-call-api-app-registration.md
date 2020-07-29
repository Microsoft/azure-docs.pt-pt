---
title: Registe uma API web que chama APIs web - Plataforma de identidade microsoft / Rio Azure
description: Saiba como construir uma API web que chama APIs web a jusante (registo de aplicações).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885128"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API web que chama APIs web: Registo de aplicações

Uma API web que chama APIs web a jusante tem o mesmo registo que uma API web protegida. Por isso, tem de seguir as instruções na [Protected web API: Registo de aplicações](scenario-protected-web-api-app-registration.md).

Como a aplicação web agora chama APIs web, torna-se uma aplicação confidencial do cliente. É por isso que são necessárias informações extra de registo: a app precisa de partilhar segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

As aplicações web chamam APIs em nome dos utilizadores para os quais o token do portador foi recebido. As aplicações web precisam solicitar permissões delegadas. Para obter mais informações, consulte [adicionar permissões para aceder a APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Uma API web que chama APIs web: configuração de código](scenario-web-api-call-api-app-configuration.md)

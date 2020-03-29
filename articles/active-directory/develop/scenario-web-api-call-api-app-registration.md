---
title: Registe uma API web que chama APIs web - plataforma de identidade da Microsoft Azure
description: Saiba como construir uma API web que chama APIs web a jusante (registo de aplicações).
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701795"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API web que chama APIs web: Registo de aplicativos

Uma API web que chama APIs web a jusante tem o mesmo registo que uma API web protegida. Por isso, tem de seguir as instruções na [Web API protegida: registo](scenario-protected-web-api-app-registration.md)de aplicações .

Como a aplicação web agora chama APIs web, torna-se uma aplicação de cliente confidencial. É por isso que são necessárias informações extra de registo: a aplicação precisa de partilhar segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

As aplicações web ligam para APIs em nome dos utilizadores para os quais o token portador foi recebido. As aplicações web precisam de solicitar permissões delegadas. Para mais informações, consulte [Adicionar permissões para aceder a APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Um API web que chama APIs web: Configuração de código](scenario-web-api-call-api-app-configuration.md)

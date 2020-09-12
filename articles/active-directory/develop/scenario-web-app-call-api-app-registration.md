---
title: Registe uma aplicação web que chama APIs web - Plataforma de identidade da Microsoft Rio Azure
description: Saiba como registar uma aplicação web que chama APIs web
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
ms.openlocfilehash: f94a3da96243e30faa90277ce86efec037f54672
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436476"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Uma aplicação web que chama APIs web: Registo de aplicações

Uma aplicação web que chama APIs web tem o mesmo registo que uma aplicação web que assina os utilizadores. Assim, siga as instruções numa [aplicação web que assina nos utilizadores: Registo de aplicações.](scenario-web-app-sign-user-app-registration.md)

No entanto, como a aplicação web agora também chama APIs web, torna-se uma aplicação confidencial do cliente. É por isso que é necessário um registo extra. A aplicação deve partilhar credenciais de cliente, ou *segredos,* com a plataforma de identidade da Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

As aplicações web chamam APIs em nome do utilizador inscrito. Para isso, devem solicitar *permissões delegadas.* Para mais informações, consulte [permissões adicionais para aceder à sua API web.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Uma aplicação web que chama APIs web: configuração de código](scenario-web-app-call-api-app-configuration.md)

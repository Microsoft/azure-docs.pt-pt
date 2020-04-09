---
title: Registe uma aplicação web que ligue para a web APIs - plataforma de identidade da Microsoft Azure
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881881"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Uma aplicação web que chama APIs web: Registo de aplicações

Uma aplicação web que chama APIs web tem o mesmo registo que uma aplicação web que assina os utilizadores dentro Então, siga as instruções em [Uma aplicação web que assina nos utilizadores: Registo](scenario-web-app-sign-user-app-registration.md)de aplicações .

No entanto, como a aplicação web agora também chama APIs web, torna-se uma aplicação confidencial do cliente. É por isso que é necessário um registo extra. A aplicação deve partilhar credenciais de clientes, ou *segredos,* com a plataforma de identidade da Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

As aplicações web ligam para APIs em nome do utilizador inscrito. Para isso, devem solicitar *permissões delegadas.* Para mais detalhes, consulte [Adicionar permissões para aceder a APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma aplicação web que chama APIs web: Configuração de código](scenario-web-app-call-api-app-configuration.md)

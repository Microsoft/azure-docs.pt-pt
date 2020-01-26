---
title: Registe uma aplicação web que ligue para a web APIs - plataforma de identidade da Microsoft Azure
description: Saiba como registar uma aplicação web que chama APIs web
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759062"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Uma aplicação web que chama APIs web: Registo de aplicações

Uma aplicação web que chama APIs web tem o mesmo registo que uma aplicação web que assina os utilizadores dentro Então, siga as instruções em [Uma aplicação web que assina nos utilizadores: Registo](scenario-web-app-sign-user-app-registration.md)de aplicações .

No entanto, como a aplicação web agora também chama APIs web, torna-se uma aplicação confidencial do cliente. É por isso que é necessário um registo extra. A aplicação deve partilhar credenciais de clientes, ou *segredos,* com a plataforma de identidade da Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões DaPI

As aplicações web ligam para APIs em nome do utilizador inscrito. Para isso, devem solicitar *permissões delegadas.* Para obter detalhes, consulte [adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma aplicação web que chama APIs web: Configuração de código](scenario-web-app-call-api-app-configuration.md)

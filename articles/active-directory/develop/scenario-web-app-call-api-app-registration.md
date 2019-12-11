---
title: Registrar um aplicativo Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como registrar um aplicativo Web que chama APIs da Web
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
ms.openlocfilehash: 784de823e94aace6f91222c19c1ff8130c3f995f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962938"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Aplicativo Web que chama APIs Web-registro de aplicativo

Um aplicativo Web chamando APIs da Web tem o mesmo registro que os usuários de entrada de um aplicativo Web. Portanto, você precisará seguir as instruções em [aplicativo Web que faz logon de usuários – registro de aplicativo](scenario-web-app-sign-user-app-registration.md)

No entanto, como o aplicativo Web agora chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que há um pouco de registro extra necessário: ele precisa compartilhar segredos (credenciais do cliente) com a plataforma Microsoft Identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos Web chamam APIs em nome do usuário conectado. Eles precisam solicitar permissões delegadas. Para obter detalhes, consulte [adicionar permissões para acessar APIs Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-web-app-call-api-app-configuration.md)

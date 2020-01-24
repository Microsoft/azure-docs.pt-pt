---
title: Registrar uma API Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar uma API Web que chama APIs da Web downstream (registro de aplicativo).
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701795"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API Web que chama APIs da Web: registro de aplicativo

Uma API da Web que chama APIs da Web downstream tem o mesmo registro de uma API Web protegida. Portanto, você precisa seguir as instruções em [API Web protegida: registro de aplicativo](scenario-protected-web-api-app-registration.md).

Como o aplicativo Web agora chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que as informações de registro extras são necessárias: o aplicativo precisa compartilhar segredos (credenciais de cliente) com a plataforma Microsoft Identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos Web chamam APIs em nome dos usuários para os quais o token de portador foi recebido. Os aplicativos Web precisam solicitar permissões delegadas. Para obter mais informações, consulte [adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: configuração de código](scenario-web-api-call-api-app-configuration.md)

---
title: Registe uma API web que chama APIs web | Rio Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 8349b7a53eb7b03b27c695bb24c8cb9bc665a1ac
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756365"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API web que chama APIs web: Registo de aplicações

Uma API web que chama APIs web a jusante tem o mesmo registo que uma API web protegida. Por isso, tem de seguir as instruções na [Protected web API: Registo de aplicações](scenario-protected-web-api-app-registration.md).

Como a aplicação web agora chama APIs web, torna-se uma aplicação confidencial do cliente. É por isso que são necessárias informações extra de registo: a app precisa de partilhar segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

As aplicações web chamam APIs em nome dos utilizadores para os quais o token do portador foi recebido. As aplicações web precisam solicitar permissões delegadas. Para mais informações, consulte [Adicionar permissões para aceder à sua API web.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="next-steps"></a>Próximos passos

Passe para o próximo artigo neste cenário, [configuração do Código de Aplicação](scenario-web-api-call-api-app-configuration.md).

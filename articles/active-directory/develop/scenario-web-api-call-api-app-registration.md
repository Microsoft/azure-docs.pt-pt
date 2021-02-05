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
ms.openlocfilehash: af1047c5f890b1b88ae6d043a30704e84b8dc079
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584319"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API web que chama APIs web: Registo de aplicações

Uma API web que chama APIs web a jusante tem o mesmo registo que uma API web protegida. Siga as instruções em [Protected web API: Registo de aplicações](scenario-protected-web-api-app-registration.md).

Como a aplicação web agora chama APIs web, torna-se uma aplicação confidencial do cliente. É por isso que são necessárias informações extra de registo: a app precisa de partilhar segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

As aplicações web chamam APIs em nome dos utilizadores para os quais o token do portador foi recebido. As aplicações web precisam solicitar permissões delegadas. Para mais informações, consulte [Adicionar permissões para aceder à sua API web.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [configuração do Código de Aplicação](scenario-web-api-call-api-app-configuration.md).

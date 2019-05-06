---
title: Aplicação Web que chamadas de web APIs (registo da aplicação) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação web que chamadas de web APIs (registo da aplicação)
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075194"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Aplicação Web que chamadas de web APIs - registo de aplicações

Uma Web app chamadas das APIs web tem o mesmo registo como um utilizadores de início de sessão nas aplicações da Web. Precisará, por conseguinte, siga as instruções em [aplicação Web que inicia sessão os utilizadores - registo de aplicações](scenario-web-app-sign-user-app-registration.md)

No entanto desde a aplicação Web agora chamadas de web APIs, torna-se uma aplicação de cliente confidencial. É por isso que é um pouco de registo extra necessário: tem de partilhar os segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Aplicativos Web chamam APIs em nome do utilizador com sessão iniciada. Eles precisam solicitar permissões delegadas. Para obter detalhes consulte [adicionar permissões para aceder a web APIs](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-web-app-call-api-app-configuration.md)

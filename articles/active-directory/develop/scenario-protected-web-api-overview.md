---
title: API web protegida - visão geral
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web protegida (visão geral).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537207"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

Neste cenário, aprende-se a expor uma API web. Também aprende a proteger a API web para que apenas os utilizadores autenticados possam aceder à sua rede.

Para utilizar a sua API web, precisa de ativar utilizadores autenticados com contas de trabalho e escola ou ativar contas pessoais da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Especificidades

Aqui estão informações específicas que precisa de saber para proteger as APIs da web:

- O registo da sua aplicação deve expor pelo menos um âmbito. A versão simbólica aceite pela sua API web depende do público de inscrição.
- A configuração de código para a API web deve validar o token utilizado quando a API web é chamada.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Registo de aplicações](scenario-protected-web-api-app-registration.md)

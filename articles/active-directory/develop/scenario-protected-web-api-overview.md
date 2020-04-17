---
title: API da web protegida - visão geral
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web protegida (visão geral).
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537207"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

Neste cenário, aprende-se a expor uma API web. Também aprende como proteger a API web para que apenas os utilizadores autenticados possam aceder a ela.

Para utilizar a sua API web, precisa de ativar utilizadores autenticados com contas de trabalho e de escola ou ativar contas pessoais da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Especificidades

Aqui está uma informação específica que precisa de saber para proteger as APIs web:

- O registo da sua aplicação deve expor pelo menos um âmbito. A versão simbólica aceite pela sua Web API depende do público de inscrição.
- A configuração do código para a Web API deve validar o token utilizado quando a Web API é chamada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-protected-web-api-app-registration.md)

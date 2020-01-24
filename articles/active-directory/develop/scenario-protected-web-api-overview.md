---
title: API web protegida - visão geral
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web protegida (visão geral).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f96393adf0eaed8a28bc1a2ec0def6b0386bd7ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701982"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

Neste cenário, vamos mostrar-lhe como pode expor uma API web e como pode protegê-la para que apenas os utilizadores autenticados possam aceder à API. Você vai querer permitir que utilizadores autenticados com contas de trabalho e escola, ou contas pessoais da Microsoft para usar a sua API web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Especificações

Aqui estão algumas especificidades que precisa de saber para proteger apis web:

- O registo da sua aplicação deve expor pelo menos um âmbito. A versão simbólica aceite pela sua Web API depende do sinal em audiência.
- A configuração do código para a Web API deve validar o token que é usado ao chamar a Web API.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-protected-web-api-app-registration.md)

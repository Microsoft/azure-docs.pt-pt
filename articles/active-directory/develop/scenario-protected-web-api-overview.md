---
title: API Web protegida-visão geral
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida (visão geral).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64418138f9eff61cc94a57be4dc5e2625e5fdebb
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803683"
---
# <a name="scenario-protected-web-api"></a>Cenário: API Web protegida

Neste cenário, mostraremos como você pode expor uma API da Web e como você pode protegê-la para que somente usuários autenticados possam acessar a API. Você desejará habilitar usuários autenticados com contas corporativas e de estudante ou contas pessoais pessoais da Microsoft para usar sua API Web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Especificações

Aqui estão algumas especificações que você precisa saber para proteger as APIs da Web:

- O registro do aplicativo deve expor pelo menos um escopo. A versão do token aceita pela sua API da Web depende do público-alvo.
- A configuração do código para a API Web deve validar o token que é usado ao chamar a API da Web.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-protected-web-api-app-registration.md)

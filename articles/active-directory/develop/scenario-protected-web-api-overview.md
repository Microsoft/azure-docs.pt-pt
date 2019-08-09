---
title: API Web protegida-visão geral | Azure
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
ms.openlocfilehash: 02bd4b84cc7542714f6db45c12c4b5b13a7fb449
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852585"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

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

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
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c9cc8e6fcc796cfc7a09cf7bc1431646a6603af0
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442978"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

Neste cenário, aprende-se a expor uma API web. Também aprende a proteger a API web para que apenas os utilizadores autenticados possam aceder à sua rede.

Para utilizar a sua API web, precisa de ativar utilizadores autenticados com contas de trabalho e escola ou ativar contas pessoais da Microsoft.

## <a name="specifics"></a>Especificidades

Aqui estão informações específicas que precisa de saber para proteger as APIs da web:

- O registo da sua aplicação deve expor pelo menos um *âmbito* ou uma *função de aplicação.*
  - Os âmbitos são expostos por APIs web que são chamados em nome de um utilizador.
  - As funções de aplicação são expostas por APIs web chamadas por aplicações daemon (que liga para a sua API web em seu próprio nome).
- Se criar um novo registo de aplicativos API web, escolha a [versão de acesso aceite](reference-app-manifest.md#accesstokenacceptedversion-attribute) pela sua API web para `2` . Para as APIs web legacy, a versão token aceite pode ser `null` , mas este valor restringe o público de entrada apenas às organizações, e as contas pessoais da Microsoft (MSA) não serão suportadas.
- A configuração de código para a API web deve validar o token utilizado quando a API web é chamada.
- O código nas ações do controlador deve validar as funções ou âmbitos no token.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [registo de aplicações.](scenario-protected-web-api-app-registration.md)

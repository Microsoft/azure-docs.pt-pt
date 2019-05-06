---
title: Protegido Web API - descrição geral | Azure
description: Saiba como criar uma API (descrição geral) de web protegida.
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
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074894"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

Neste cenário, vamos mostrar-lhe como pode expor uma API web e como pode protegê-los para que apenas utilizadores autenticados pode aceder à API. Desejará permitir aos utilizadores autenticados com o trabalho e contas escolares ou pessoais contas pessoais da Microsoft utilizar a API web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Informações específicas

Aqui estão alguns pontos específicos que precisa saber para proteger as APIs web:

- O registo de aplicação deve expor pelo menos um âmbito. A versão de token aceite pela sua API web depende do público-alvo de início de sessão.
- A configuração do código para a API web tem de validar o token que é utilizado quando chamar a API web.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-protected-web-api-app-registration.md)

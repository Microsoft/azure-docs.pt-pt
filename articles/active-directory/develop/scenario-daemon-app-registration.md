---
title: Registar aplicações daemon que chamam APIs web - Plataforma de identidade da Microsoft Rio Azure
description: Saiba como construir uma app daemon que chama APIs web - registo de aplicações
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80885485"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>App Daemon que chama APIs web - registo de aplicações

Para uma aplicação daemon, eis o que precisa de saber quando registar a aplicação.

## <a name="supported-account-types"></a>Tipos de conta suportados

As aplicações da Daemon só fazem sentido nos inquilinos da AD Azure. Assim, quando cria a aplicação, tem de escolher uma das seguintes opções:

- **Contas apenas neste diretório organizacional.** Esta escolha é a mais comum porque as aplicações daemon são geralmente escritas por desenvolvedores de linha de negócios (LOB).
- **Contas em qualquer diretório organizacional.** Você fará esta escolha se você é um ISV fornecendo uma ferramenta de utilidade para os seus clientes. Vai precisar dos administradores dos seus clientes para aprová-lo.

## <a name="authentication---no-reply-uri-needed"></a>Autenticação - sem resposta necessária URI

No caso de a sua aplicação de cliente confidencial utilizar *apenas* o fluxo de credenciais de cliente, a resposta URI não precisa de ser registada. Não é necessário para a configuração ou construção da aplicação. As credenciais dos clientes não a usam.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões API - permissões de aplicações e consentimento administrativo

Um pedido daemon só pode solicitar permissões de pedido a APIs (não permissões delegadas). Na página de **permissões** da API para o registo da candidatura, depois de ter selecionado **Adicionar uma permissão** e escolher a família API, escolher **permissões de aplicação**e, em seguida, selecionar as suas permissões.

![Permissões de aplicativos e consentimento administrativo](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A API web a que pretende chamar precisa de definir permissões de *aplicação (funções de aplicação)* e não permissões delegadas. Para obter detalhes sobre como expor tal API, consulte [a Protected web API: Registo de aplicações - quando a sua API web é chamada por uma app daemon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Os pedidos da Daemon exigem que um administrador de inquilino predemia o pedido chamando a API web. Os administradores de inquilinos fornecem este consentimento na mesma página **de permissão da API,** selecionando **o consentimento administrativo do Grant para a nossa *organização.* **

Se você é um ISV construindo uma aplicação multitenant, você deve ler a secção [Implementação - caso de aplicações multitenantes de daemon](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Daemon app - configuração de código de aplicação](./scenario-daemon-app-configuration.md)

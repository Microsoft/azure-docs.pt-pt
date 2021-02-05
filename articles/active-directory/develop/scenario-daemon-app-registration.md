---
title: Registar aplicações daemon que chamam APIs web - Plataforma de identidade da Microsoft | Rio Azure
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
ms.openlocfilehash: 938a19276839d5e0d2bd3e0244510fc068cb029c
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582912"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>App Daemon que chama APIs web - registo de aplicações

Para uma aplicação daemon, eis o que precisa de saber quando registar a aplicação.

## <a name="supported-account-types"></a>Tipos de conta suportados

As aplicações da Daemon só fazem sentido nos inquilinos da AD Azure. Assim, quando criar a aplicação, escolha uma das seguintes opções:

- **Contas apenas neste diretório organizacional.** Esta escolha é a mais comum porque as aplicações daemon são geralmente escritas por desenvolvedores de linha de negócios (LOB).
- **Contas em qualquer diretório organizacional.** Você fará esta escolha se você é um ISV fornecendo uma ferramenta de utilidade para os seus clientes. Vai precisar dos administradores dos seus clientes para aprová-lo.

## <a name="authentication---no-reply-uri-needed"></a>Autenticação - sem resposta necessária URI

No caso de a sua aplicação de cliente confidencial utilizar *apenas* o fluxo de credenciais de cliente, a resposta URI não precisa de ser registada. Não é necessário para a configuração ou construção da aplicação. As credenciais dos clientes não a usam.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões API - permissões de aplicações e consentimento administrativo

Um pedido daemon só pode solicitar permissões de pedido a APIs (não permissões delegadas). Na página de **permissões** da API para o registo da candidatura, depois de ter selecionado **Adicionar uma permissão** e escolher a família API, escolher **permissões de aplicação** e, em seguida, selecionar as suas permissões.

![Permissões de aplicativos e consentimento administrativo](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A API web a que pretende chamar precisa de definir permissões de *aplicação (funções de aplicação)* e não permissões delegadas. Para obter detalhes sobre como expor tal API, consulte [a Protected web API: Registo de aplicações - quando a sua API web é chamada por uma app daemon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Os pedidos da Daemon exigem que um administrador de inquilino predemia o pedido chamando a API web. Os administradores de inquilinos fornecem este consentimento na mesma página **de permissão da API,** selecionando **o consentimento administrativo do Grant para a nossa *organização.***

Se você é um ISV construindo uma aplicação multitenant, você deve ler a secção [Implementação - caso de aplicações multitenantes de daemon](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [configuração do código de aplicação](./scenario-daemon-app-configuration.md).

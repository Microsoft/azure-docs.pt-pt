---
title: Registe aplicações daemon que liguem para a web APIs - plataforma de identidade da Microsoft Azure
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885485"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>App Daemon que chama APIs web - registo de aplicativos

Para uma aplicação daemon, aqui está o que precisa de saber quando regista a aplicação.

## <a name="supported-account-types"></a>Tipos de conta suportados

As aplicações da Daemon só fazem sentido nos inquilinos da AD Azure. Por isso, quando cria a aplicação, tem de escolher uma das seguintes opções:

- **Contas apenas neste diretório organizacional.** Esta escolha é a mais comum porque as aplicações daemon são geralmente escritas por desenvolvedores de linha de negócio (LOB).
- **Contas em qualquer diretório organizacional.** Você fará esta escolha se você é um ISV fornecendo uma ferramenta de utilidade para os seus clientes. Vai precisar dos administradores dos inquilinos dos seus clientes para aprová-lo.

## <a name="authentication---no-reply-uri-needed"></a>Autenticação - sem resposta que URI necessária

No caso de a sua aplicação de cliente confidencial utilizar *apenas* o fluxo de credenciais do cliente, a resposta URI não precisa de ser registada. Não é necessário para a configuração da aplicação ou construção. O fluxo de credenciais do cliente não o usa.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões DaPi - permissões de aplicativos e consentimento da administração

Um pedido de daemon só pode solicitar permissões de pedido de APIs (não permissões delegadas). Na página de **permissões da API** para o registo da candidatura, depois de ter selecionado **Adicionar uma permissão** e escolher a família API, escolher **permissões**de Pedido e, em seguida, selecionar as suas permissões.

![Permissões de aplicativos e consentimento da administração](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A API web que pretende ligar precisa de definir *permissões de aplicação (funções de aplicação)*, e não permissões delegadas. Para mais detalhes sobre como expor tal API, consulte [a Web API protegida: registo de aplicações - quando a sua Web API é chamada por uma aplicação daemon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

As aplicações da Daemon exigem que um administrador inquilino concorde com a aplicação que chama a Web API. Os administradores dos inquilinos fornecem este consentimento na mesma página de **permissão da API,** selecionando o **consentimento do administrador grant para a nossa *organização* **

Se você é um ISV construindo uma aplicação multiarrendatária, você deve ler a secção [Deployment - caso de aplicações de daemon multiinquilino](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [App Daemon - configuração de código de aplicação](./scenario-daemon-app-configuration.md)

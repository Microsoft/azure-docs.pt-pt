---
title: O daemon aplicação chamadas das APIs web (registo da aplicação) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de daemon que chama o web APIs - registo de aplicações
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076244"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplicação de daemon que chamadas de web APIs - registo de aplicações

Para uma aplicação de daemon, eis o que precisa saber ao registar a aplicação.

## <a name="supported-account-types"></a>Tipos de conta suportados

Uma vez que os aplicativos de daemon só fazem sentido no inquilino do Azure AD, ao criar a aplicação terá de escolher:

- qualquer um dos **contas neste diretório organizacional apenas**. Esta opção é o caso mais comum, como aplicativos de daemon geralmente são escritos por desenvolvedores do linha de negócio (LOB).
- ou **contas em qualquer diretório organizacional**. Faz essa opção se for um ISV fornecer uma ferramenta de utilitário para os seus clientes. Terá dos administradores de inquilinos do cliente aprová-la.

## <a name="authentication---no-reply-uri-needed"></a>Nenhum URI de resposta de autenticação - necessário

No caso em que utilize a sua aplicação de cliente confidencial **apenas** fluxo as credenciais de cliente, o URL de resposta não têm de ser registados. Não é necessário para a construção/configuração de aplicação. O fluxo de credenciais de cliente não usá-lo.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões de API - consentimento de administrador e permissões de aplicações

Um aplicativo de daemon só pode solicitar permissões de aplicação para APIs (não delegadas permissões). Na **permissão de API** página para o registo de aplicação, depois de selecionar **adicionar uma permissão** e escolher a família de API, escolha **permissões de aplicação**, e, em seguida, selecione as suas permissões

![Consentimento de administrador e permissões de aplicações](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Aplicações daemon exigem um administrador de inquilino previamente autorizar a aplicação chamar a API web. Este consentimento é fornecido na mesma **permissão de API** página, por um administrador de inquilino selecionando **conceder autorização de administrador para *nossa organização***

Se for um ISV, criação de uma aplicação multi-inquilino, iria querer verificar a [implementação - caso de aplicações de daemon de multi-inquilino](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) parágrafo.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicação de daemon - configuração de código de aplicação](./scenario-daemon-app-configuration.md)

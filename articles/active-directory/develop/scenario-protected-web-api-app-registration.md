---
title: API da web - registo de aplicação protegida | Azure
description: Saiba como criar uma API Web protegida e as informações necessárias registar a aplicação.
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
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074909"
---
# <a name="protected-web-api---app-registration"></a>API - registo de aplicações web protegida

Este artigo explica as especificações de registo de aplicação para uma API web protegida.

Consulte [início rápido: Registar uma aplicação com a plataforma de identidade do Microsoft](quickstart-register-app.md) para obter os passos comuns sobre como registar a aplicação.

## <a name="accepted-token-version"></a>Versão aceite de token

O ponto de extremidade de plataforma de identidade Microsoft pode emitir os dois tipos de tokens: v1.0 tokens e v2.0 tokens. Pode saber mais sobre estes tokens no [tokens de acesso](access-tokens.md). A versão de token aceite depende da **tipos de conta suportados** que escolheu quando criou a sua aplicação:

- Se o valor de **tipos de conta suportados** é **contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, o Skype, Xbox, Outlook.com)**, a versão de token aceite será a versão 2.0.
- Caso contrário, a versão de token aceite será a versão 2.0.

Depois de criar a aplicação, pode alterar a versão de token aceite seguindo estes passos:

1. No portal do Azure, selecione a sua aplicação e, em seguida, selecione o **manifesto** para a sua aplicação.
2. No manifesto, procure **"accessTokenAcceptedVersion"** e vê que seu valor é **2**. Esta propriedade permite que o Azure AD, sabe que a API web aceita tokens de v2.0. Se for **nulo**, a versão de token aceite estará v1.0.
3. Selecione **Guardar**.

> [!NOTE]
> Cabe-lhe para a API web para decidir qual versão de token (versão 1.0 ou v2.0) aceita. Quando os clientes solicitam um token para a API utilizando o ponto de final de v2.0 de plataforma de identidade do Microsoft web, elas receberão um token que indica qual versão é aceite pela web API.

## <a name="no-redirect-uri"></a>Nenhum URI de redirecionamento

As APIs da Web não precisam de registar um URI de redirecionamento, pois não está nenhum utilizador com sessão iniciada interativamente.

## <a name="expose-an-api"></a>Expor uma API

Outra configuração específica para web APIs é a API exposta e os âmbitos expostos.

### <a name="resource-uri-and-scopes"></a>URI do recurso e âmbitos

Os âmbitos são normalmente do formulário `resourceURI/scopeName`. Para o Microsoft Graph, os âmbitos têm atalhos, como `User.Read`, mas essa cadeia de caracteres é apenas um atalho para `https://graph.microsoft.com/user.read`.

Durante o registo de aplicação, terá de definir os seguintes parâmetros:

- Um recurso de URI - por predefinição, o portal de registo de aplicação recomenda que use `api://{clientId}`. Este URI do recurso é exclusivo, mas não é humano legível. Pode alterá-la, mas certifique-se de que é exclusivo.
- Um ou vários âmbitos

Os âmbitos são também apresentados no ecrã de consentimento que é apresentado aos utilizadores finais que utilizam a sua aplicação. Por conseguinte, terá de fornecer as cadeias de caracteres correspondentes que descrevem o âmbito:

- Como visto pelo utilizador final
- Como visto pelo administrador inquilino, que pode conceder o consentimento de administrador

### <a name="how-to-expose-the-api"></a>Como expor a API

1. Selecione o **expor uma API** secção registo de aplicação, e:
   1. Selecione **Adicionar âmbito**.
   1. Aceitar o URI de ID de aplicação propostas (api :// {clientId}), selecionando **guardar e continuar**.
   1. Introduza os seguintes parâmetros:
      - Para **nome do âmbito**, utilize `access_as_user`.
      - Para **quem pode consentir**, certifique-se a **administradores e utilizadores** opção está selecionada.
      - Na **nome de exibição de consentimento de administrador**, tipo `Access TodoListService as a user`.
      - Na **descrição do consentimento de administrador**, tipo `Accesses the TodoListService Web API as a user`.
      - Na **nome de exibição de consentimento do usuário**, tipo `Access TodoListService as a user`.
      - Na **descrição de consentimento do utilizador**, tipo `Accesses the TodoListService Web API as a user`.
      - Manter **estado** definida como **ativado**.
      - Selecione **Adicionar âmbito**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-protected-web-api-app-configuration.md)

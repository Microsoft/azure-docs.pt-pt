---
title: API da web - registo de aplicação protegida | Azure
description: Saiba como criar uma API web protegida e as informações necessárias registar a aplicação.
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
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536854"
---
# <a name="protected-web-api-app-registration"></a>API web protegida: Registo da aplicação

Este artigo explica as especificações de registo de aplicações para uma API web protegida.

Consulte [início rápido: Registar uma aplicação com a plataforma de identidade do Microsoft](quickstart-register-app.md) para obter os passos comuns para registar uma aplicação.

## <a name="accepted-token-version"></a>Versão aceite de token

O ponto de extremidade de plataforma de identidade Microsoft pode emitir os dois tipos de tokens: v1.0 tokens e v2.0 tokens. Para obter mais informações sobre estes tokens, consulte [tokens de acesso](access-tokens.md). A versão de token aceite depende da **tipos de conta suportados** que escolheu quando criou a sua aplicação:

- Se o valor de **tipos de conta suportados** é **contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, o Skype, Xbox, Outlook.com)** , a versão de token aceite será a versão 2.0.
- Caso contrário, a versão de token aceite será v1.0.

Depois de criar a aplicação, pode determinar ou alterar a versão de token aceite seguindo estes passos:

1. No portal do Azure, selecione a sua aplicação e, em seguida, selecione o **manifesto** para a sua aplicação.
2. No manifesto, procure **"accessTokenAcceptedVersion"** . Tenha em atenção que o seu valor é **2**. Esta propriedade especifica ao Azure Active Directory (Azure AD) que a API web aceita tokens de v2.0. Se o valor for **nulo**, a versão de token aceite é a v1.0.
3. Se alterou a versão de token, selecione **guardar**.

> [!NOTE]
> A API web Especifica qual versão de token (versão 1.0 ou v2.0) aceita. Quando os clientes solicitam um token para a API web a partir do ponto de final de v2.0 de plataforma de identidade do Microsoft, elas receberão um token que indica qual versão é aceite pela web API.

## <a name="no-redirect-uri"></a>Nenhum URI de redirecionamento

As APIs da Web não precisam de registar um URI de redirecionamento, porque nenhum utilizador tiver iniciado sessão interativamente.

## <a name="expose-an-api"></a>Expor uma API

Outra configuração específica para web APIs é a API exposta e os âmbitos expostos.

### <a name="resource-uri-and-scopes"></a>URI do recurso e âmbitos

Os âmbitos são normalmente na forma `resourceURI/scopeName`. Para o Microsoft Graph, os âmbitos têm atalhos como `User.Read`. Esta cadeia é um atalho para `https://graph.microsoft.com/user.read`.

Durante o registo de aplicação, terá de definir estes parâmetros:

- O URI do recurso. Por predefinição, o portal de registo de aplicação recomenda que use `api://{clientId}`. Este URI do recurso é exclusivo, mas não é humano legível. Pode alterá-la, mas certifique-se de que o novo valor é exclusivo.
- Um ou mais *âmbitos*. (Para aplicações de cliente, estas aparecerão como *permissões delegadas* para a API web.)
- Um ou mais *funções de aplicação*. (Para aplicações de cliente, estas aparecerão como *permissões de aplicação* para a API web.)

Os âmbitos são também apresentados no ecrã de consentimento que é apresentado aos utilizadores finais da sua aplicação. Por isso, terá de fornecer as cadeias de caracteres correspondentes que descrevem o âmbito:

- Como visto pelo usuário final.
- Como visto pelo administrador inquilino, que pode conceder o consentimento de administrador.

### <a name="exposing-delegated-permissions-scopes"></a>Expondo delegadas permissões (âmbitos)

1. Selecione o **expor uma API** secção do registo de aplicação.
1. Selecione **Adicionar âmbito**.
1. Se lhe for pedido, aceitar o URI de ID de aplicação propostas (`api://{clientId}`) ao selecionar **guardar e continuar**.
1. Introduza estes parâmetros:
      - Para **nome do âmbito**, utilize **access_as_user**.
      - Para **quem pode consentir**, certifique-se **administradores e utilizadores** está selecionada.
      - Na **nome de exibição de consentimento de administrador**, introduza **TodoListService de acesso como um utilizador**.
      - Na **descrição do consentimento de administrador**, introduza **acessa a API da Web como um utilizador TodoListService**.
      - Na **nome de exibição de consentimento do usuário**, introduza **TodoListService de acesso como um utilizador**.
      - Na **descrição de consentimento do utilizador**, introduza **acessa a API da Web como um utilizador TodoListService**.
      - Manter **estado** definida como **ativado**.
      - Selecione **Adicionar âmbito**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se a API web é chamada por um aplicativo do daemon

Nesta secção, irá aprender como registar a API web protegida para que ele pode ser chamado em segurança por aplicações daemon.

- terá de expor *permissões de aplicação*. Declarar apenas permissões de aplicação pois aplicações daemon não interagirem com os utilizadores, para que as permissões delegadas não faria sentido.
- Os administradores de inquilinos podem exigir o Azure Active Directory (Azure AD) para emitir tokens para a API web apenas para aplicativos registrados para acesso uma das permissões de aplicação da API web.

#### <a name="exposing-application-permissions-app-roles"></a>Expondo as permissões de aplicação (funções da aplicação)

Para expor as permissões de aplicação, terá de editar o manifesto.

1. No registo de aplicação para a sua aplicação, selecione **manifesto**.
1. Editar o manifesto, localizando o `appRoles` definição e adicionar uma ou mais funções de aplicação. A definição de função é fornecida no bloco JSON de exemplo seguinte. Deixe o `allowedMemberTypes` definido como `"Application"` apenas. Certifique-se de que o `id` é um GUID exclusivo e que `displayName` e `value` não contêm espaços.
1. Guarde o manifesto.

O exemplo a seguir mostra o conteúdo de `appRoles`. (O `id` pode ser qualquer GUID exclusivo.)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Garantir que o Azure AD emite tokens para a web API para os clientes só é permitido

Verifica a API web para a função de aplicação. (Essa é a maneira de desenvolvedor para expor as permissões de aplicação.) Mas também pode configurar o Azure AD para emitir um token para a API web apenas às aplicações que são aprovadas pelo administrador inquilino para aceder à sua API. Para adicionar este maior segurança:

1. Na aplicação **descrição geral** página para o seu registo de aplicação, selecione a ligação com o nome da sua aplicação sob **aplicação gerida no diretório local**. O título para este campo poderá ser truncado. Por exemplo, poderá, ver **aplicação gerida em...**

   > [!NOTE]
   >
   > Quando seleciona esta ligação, vamos para o **descrição geral de aplicações do Enterprise** página associada com o principal de serviço para a sua aplicação no inquilino onde criou. Pode navegar para a página de registo de aplicação com o botão voltar do seu navegador.

1. Selecione o **propriedades** página no **gerir** secção das páginas de aplicativo empresarial.
1. Se pretender que o Azure AD para permitir o acesso à web API de apenas determinados clientes, defina **atribuição utiliz. necessária?** ao **Sim**.

   > [!IMPORTANT]
   >
   > Se definir **atribuição utiliz. necessária?** ao **Sim**, do Azure AD irá verificar as atribuições de função de clientes quando eles solicitar um token de acesso para a API web. Se o cliente não está atribuído a quaisquer funções de aplicação, do Azure AD irá devolver o erro `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Se mantiver **atribuição utiliz. necessária?** definida como **não**, *do Azure AD não verifique as atribuições de função quando um cliente solicita um token de acesso para a API web*. Qualquer cliente de daemon (ou seja, qualquer cliente com o fluxo de credenciais de cliente) será capaz de obter um token de acesso para a API simplesmente especificando seu público-alvo. Qualquer aplicativo será capaz de aceder à API sem ter de pedir permissão para este. Mas a API web pode sempre, conforme explicado na seção anterior, certifique-se de que o aplicativo tem a função certa (o que está autorizada pelo administrador inquilino). A API realiza esta verificação ao validar que o token de acesso tem funções de uma afirmação e que o valor para essa declaração está correto. (No nosso caso, o valor é `access_as_application`.)

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-protected-web-api-app-configuration.md)

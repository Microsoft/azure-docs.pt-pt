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
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111117"
---
# <a name="protected-web-api---app-registration"></a>API - registo de aplicações web protegida

Este artigo explica as especificações de registo de aplicação para uma API web protegida.

Consulte [início rápido: Registar uma aplicação com a plataforma de identidade do Microsoft](quickstart-register-app.md) para obter os passos comuns sobre como registar a aplicação.

## <a name="accepted-token-version"></a>Versão aceite de token

O ponto de extremidade de plataforma de identidade Microsoft pode emitir os dois tipos de tokens: v1.0 tokens e v2.0 tokens. Pode saber mais sobre estes tokens no [tokens de acesso](access-tokens.md). A versão de token aceite depende da **tipos de conta suportados** que escolheu quando criou a sua aplicação:

- Se o valor de **tipos de conta suportados** é **contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, o Skype, Xbox, Outlook.com)** , a versão de token aceite será a versão 2.0.
- Caso contrário, a versão de token aceite será v1.0.

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

- O recurso URI - por predefinição, o portal de registo de aplicação recomenda que use `api://{clientId}`. Este URI do recurso é exclusivo, mas não é humano legível. Pode alterá-la, mas certifique-se de que é exclusivo.
- Um ou mais **âmbitos** (para aplicações de cliente, estes serão apresentados como **permissões delegadas** para a API Web)
- Um ou mais **funções de aplicação** (para aplicações de cliente, estes serão apresentados como **permissões de aplicação** para a API Web)

Os âmbitos são também apresentados no ecrã de consentimento que é apresentado aos utilizadores finais que utilizam a sua aplicação. Por conseguinte, terá de fornecer as cadeias de caracteres correspondentes que descrevem o âmbito:

- Como visto pelo utilizador final
- Como visto pelo administrador inquilino, que pode conceder o consentimento de administrador

### <a name="how-to-expose-delegated-permissions-scopes"></a>Como expor delegadas permissões (âmbitos)

1. Selecione o **expor uma API** secção registo de aplicação, e:
   1. Selecione **Adicionar âmbito**.
   1. Se solicitado, aceite o URI de ID de aplicação propostas (api :// {clientId}), selecionando **guardar e continuar**.
   1. Introduza os seguintes parâmetros:
      - Para **nome do âmbito**, utilize `access_as_user`.
      - Para **quem pode consentir**, certifique-se a **administradores e utilizadores** opção está selecionada.
      - Na **nome de exibição de consentimento de administrador**, tipo `Access TodoListService as a user`.
      - Na **descrição do consentimento de administrador**, tipo `Accesses the TodoListService Web API as a user`.
      - Na **nome de exibição de consentimento do usuário**, tipo `Access TodoListService as a user`.
      - Na **descrição de consentimento do utilizador**, tipo `Accesses the TodoListService Web API as a user`.
      - Manter **estado** definida como **ativado**.
      - Selecione **Adicionar âmbito**.

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>Caso em que a API Web é chamada pela aplicação de daemon

Este parágrafo, irá aprender a registar a sua API Web protegido para que ele pode ser chamado em segurança por aplicações de daemon:

- terá de expor **permissões de aplicação**. Só irá declarar permissões de aplicação à medida que os aplicativos de daemon não interagem com usuários e, portanto, permissões delegadas não faria sentido.
- Os administradores de inquilinos podem exigir do Azure AD para emitir tokens para a sua aplicação Web para apenas aplicativos registrados que querem aceder a uma das permissões de aplicações Web API.

#### <a name="how-to-expose-application-permissions-app-roles"></a>Como expor as permissões de aplicação (funções da aplicação)

Para expor as permissões de aplicação, terá de editar o manifesto.

1. No registo de aplicação para a sua aplicação, clique em **manifesto**.
1. Editar o manifesto, localizando o `appRoles` definição e adicionar uma ou várias funções de aplicação. A definição de função é fornecida no bloco JSON de exemplo abaixo.  Deixe o `allowedMemberTypes` para "Aplicação" apenas. Certifique-se de que o **id** é um guid exclusivo e **displayName** e **valor** não contêm quaisquer espaços.
1. Guarde o manifesto.

O conteúdo do `appRoles` deve ser o seguinte (o `id` pode ser qualquer GUID exclusivo)

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

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>Como garantir que o Azure AD emite tokens para a API Web apenas ao permitia aos clientes

Verifica a API Web para a função de aplicação (o que é a forma de desenvolvedor de se fazer isso). Mas ainda pode configurar o Azure Active Directory para emitir um token para a API Web apenas para aplicações que foram aprovadas pelo administrador inquilino para aceder à sua API. Para adicionar esta segurança adicional:

1. Na aplicação **descrição geral** página para o seu registo de aplicação, selecione a hiperligação com o nome do seu aplicativo na **aplicação gerida no diretório local**. O título para este campo pode ser truncado. Poderia, por exemplo, leia: `Managed application in ...`

   > [!NOTE]
   >
   > Quando seleciona esta ligação irá navegar para o **descrição geral de aplicações do Enterprise** página associada com o principal de serviço para a sua aplicação no inquilino onde criou. Pode navegar para a página de registo de aplicação com o botão voltar do seu navegador.

1. Selecione o **propriedades** página no **gerir** secção das páginas de aplicativo empresarial
1. Se pretender que o AAD para impor o acesso à sua API Web de apenas determinados clientes, defina **atribuição utiliz. necessária?** ao **Sim**.

   > [!IMPORTANT]
   >
   > Definindo **atribuição utiliz. necessária?** ao **Sim**, AAD verificará as atribuições de função dos clientes, quando eles solicitar um token de acesso para a API Web. Se o cliente não foi possível atribuir qualquer AppRoles, AAD retornaria apenas o seguinte erro: `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > Se mantiver **atribuição utiliz. necessária?** ao **não**, <span style='background-color:yellow; display:inline'>do Azure AD não verifique as atribuições de função quando um cliente solicita um token de acesso para a API Web</span>. Por conseguinte, qualquer cliente de daemon (ou seja, qualquer cliente com o fluxo de credenciais de cliente) ainda seria capaz de obter um token de acesso para a API simplesmente especificando seu público-alvo. Qualquer aplicativo, seria capaz de aceder à API sem ter de pedir permissão para este. Agora, isso não é, em seguida, end, como a API Web pode sempre, conforme explicado na próxima seção, certifique-se de que o aplicativo tem a função certa (que foi autorizada pelo administrador inquilino), validando em que o token de acesso tem um `roles` afirmação e o valor correto para t sua afirmação (no nosso caso `access_as_application`).

1. Selecione **guardar**

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-protected-web-api-app-configuration.md)

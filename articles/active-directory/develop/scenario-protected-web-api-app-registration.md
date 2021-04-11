---
title: Registo de aplicativos API protegidos na Web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web protegida e as informações necessárias para registar a aplicação.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: dcfedf2cceddb59d456d421c4846f3cd252a65b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651869"
---
# <a name="protected-web-api-app-registration"></a>API web protegida: Registo de aplicativos

Este artigo explica as especificidades do registo de aplicações para uma API web protegida.

Para que os passos comuns registe uma aplicação, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Versão simbólica aceite

A plataforma de identidade da Microsoft pode emitir tokens v1.0 e tokens v2.0. Para obter mais informações sobre estes tokens, consulte [os tokens de acesso.](access-tokens.md)

A versão simbólica que a API pode aceitar depende da seleção **dos tipos de conta suportados** quando criar o registo de aplicação da API na Web no portal Azure.

- Se o valor dos tipos de **conta suportado** for Contas em **qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**, a versão token aceite deve ser v2.0.
- Caso contrário, a versão simbólica aceite pode ser v1.0.

Depois de criar a aplicação, pode determinar ou alterar a versão simbólica aceita seguindo estes passos:

1. No portal Azure, selecione a sua aplicação e, em seguida, selecione **Manifesto**.
1. Encontre o acesso à **propriedadeTokenAcceptedVersion** no manifesto.
1. O valor especifica para Azure Ative Directory (Azure AD) que token versão que a API web aceita.
    - Se o valor for 2, a API web aceita tokens v2.0.
    - Se o valor for **nulo,** a API web aceita fichas v1.0.
1. Se alterar a versão token, **selecione Save**.

> [!NOTE]
> A API web especifica qual a versão simbólica que aceita. Quando um cliente solicita um token para a sua API web a partir da plataforma de identidade microsoft, o cliente recebe um token que indica qual a versão simbólica que a API web aceita.

## <a name="no-redirect-uri"></a>Sem redirecionar URI

As APIs web não precisam de registar um URI de redirecionamento porque nenhum utilizador está inscrito interativamente.

## <a name="exposed-api"></a>API exposta

Outras configurações específicas para APIs web são a API exposta e os âmbitos expostos ou funções de aplicação.

### <a name="application-id-uri-and-scopes"></a>ID URI de aplicação e âmbitos

Os âmbitos geralmente têm o `resourceURI/scopeName` formulário. Para o Microsoft Graph, os âmbitos têm atalhos. Por exemplo, `User.Read` é um atalho para `https://graph.microsoft.com/user.read` .

Durante o registo da aplicação, defina estes parâmetros:

- O recurso URI
- Um ou mais âmbitos
- Um ou mais papéis de aplicativo

Por predefinição, o portal de registo de aplicações recomenda que utilize o recurso URI `api://{clientId}` . Este URI é único, mas não legível pelo homem. Se alterar o URI, certifique-se de que o novo valor é único. O portal de registo de aplicações garantirá a utilização de um [domínio de editor configurado.](howto-configure-publisher-domain.md)

Para as aplicações do cliente, os âmbitos aparecem como *permissões delegadas* e as funções de aplicação aparecem como permissões de *aplicação* para a sua API web.

Os âmbitos também aparecem na janela de consentimento que é apresentada aos utilizadores da sua aplicação. Por conseguinte, forneça as cordas correspondentes que descrevem o âmbito:

- Como visto por um utilizador.
- Como visto por um administrador inquilino, que pode conceder o consentimento administrativo.

As funções da aplicação não podem ser consentidas por um utilizador (uma vez que são utilizadas por uma aplicação que liga para a API web em nome de si mesma). Um administrador de inquilino terá de consentir com as aplicações do cliente da sua API web expondo funções de aplicações. Consulte [o consentimento do Administrador](v2-admin-consent.md) para mais detalhes

### <a name="exposing-delegated-permissions-scopes"></a>Expor permissões delegadas (âmbitos)

1. Selecione **Expor uma API** no registo de inscrição.
1. Selecione **Adicionar âmbito**.
1. Se solicitado, aceite a aplicação proposta ID URI ( `api://{clientId}` ) selecionando **Save and Continue**.
1. Especificar estes valores:
    - Selecione **o nome Scope** e introduza **access_as_user**.
    - Selecione **Quem pode consentir** e certifique-se de que **os administradores e utilizadores estão selecionados.**
    - Selecione **o nome do visor do consentimento** do administrador e **introduza o Access TodoListService como utilizador**.
    - Selecione **descrição do consentimento do administrador** e **introduza Acessos à API web TodoListService como utilizador**.
    - Selecione **o nome do visualização do consentimento do utilizador** e **introduza o Access TodoListService como utilizador**.
    - Selecione **a descrição do consentimento do Utilizador** e **introduza Acessos à API web TodoListService como utilizador**.
    - Mantenha o valor **do Estado** definido para **Ativado**.
 1. Selecione **Adicionar âmbito**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se a sua API web for chamada por uma app Daemon

Nesta secção, você aprende a registar a sua API web protegida para que as aplicações daemon possam chamá-lo de forma segura.

- Declara e expõe *apenas permissões de aplicações* porque as aplicações da Daemon não interagem com os utilizadores. Permissões delegadas não fariam sentido.
- Os administradores de inquilinos podem exigir que a AZure AD emita fichas web de API apenas a aplicações que se registaram para aceder a uma das permissões de candidatura da API.

#### <a name="exposing-application-permissions-app-roles"></a>Expondo permissões de aplicações (funções de aplicação)

Para expor permissões de aplicação, edite o manifesto.

1. No registo de inscrição para a sua candidatura, selecione **Manifesto.**
1. Para editar o manifesto, encontre a `appRoles` definição e adicione funções de aplicação. As definições de função são fornecidas no bloco JSON da amostra seguinte.
1. Deixe `allowedMemberTypes` definido `"Application"` para apenas.
1. Certifique-se de que `id` é um GUID único.
1. Certifique-se `displayName` e `value` não contenha espaços.
1. Salve o manifesto.

A amostra que se segue mostra o conteúdo `appRoles` de, onde o valor de `id` pode ser qualquer GUID único.

```json
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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Garantindo que a Azure AD emite fichas para a sua API web apenas permitiu clientes

A API web verifica o papel da aplicação. Esta função é a forma de um desenvolvedor de software expor permissões de aplicações. Também pode configurar a Azure AD para emitir fichas API apenas para aplicações que o administrador inquilino aprova para acesso a API.

Para adicionar esta segurança aumentada:

1. Aceda à página **geral** da aplicação para o registo da sua aplicação.
1. Sob **aplicação Gerida no diretório local,** selecione o link com o nome da sua aplicação. O rótulo para esta seleção pode ser truncado. Por exemplo, pode ver **a aplicação Managed em ...**

   > [!NOTE]
   >
   > Quando selecionar este link, aceda à página **'Visão Geral da Aplicação da Empresa'.** Esta página está associada ao principal de serviço para a sua candidatura no arrendatário onde a criou. Pode ir à página de registo da aplicação utilizando o botão de trás do seu navegador.

1. Selecione a página **Propriedades** na secção **Gerir** as páginas de aplicação da Enterprise.
1. Se deseja que o AZure AD permita o acesso à sua API web a partir de apenas determinados clientes, desemocupe **a atribuição do Utilizador necessária?**

   > [!IMPORTANT]
   >
   > Se definir **a atribuição do Utilizador necessária?** para **Sim,** a AZure AD verifica as atribuições de funções de um cliente quando solicita um token de acesso da API web. Se o cliente não estiver atribuído a quaisquer funções de aplicação, a Azure AD devolverá a mensagem de erro "invalid_client: AADSTS501051: A aplicação \<application name\> não é atribuída a uma função para o \<web API\> ".
   >
   > Se mantiver a atribuição do  **Utilizador necessária?** Qualquer cliente daemon, ou seja, qualquer cliente que use o fluxo de credenciais de cliente, pode obter um token de acesso para a API apenas especificando o seu público. Qualquer aplicação pode aceder à API sem ter de solicitar permissões para a mesmo.
   >
   > Mas, como explicado na secção anterior, a sua API web pode sempre verificar se a aplicação tem o papel certo, que é autorizado pelo administrador do arrendatário. A API realiza esta verificação validando que o token de acesso tem uma reivindicação de funções e que o valor desta reclamação está correto. Na amostra JSON anterior, o valor é `access_as_application` .

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [configuração do código de aplicação](scenario-protected-web-api-app-configuration.md).

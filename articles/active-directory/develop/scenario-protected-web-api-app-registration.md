---
title: Registo de aplicações api web protegidas  Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web protegida e as informações necessárias para registar a app.
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
ms.openlocfilehash: 2d9e5d051f101288b8528b47fa88b4783a040950
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775151"
---
# <a name="protected-web-api-app-registration"></a>API da web protegida: Registo de aplicativos

Este artigo explica as especificidades do registo de aplicações para uma API web protegida.

Para que os passos comuns registem uma aplicação, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Versão simbólica aceite

O ponto final da plataforma de identidade da Microsoft pode emitir tokens v1.0 e tokens v2.0. Para obter mais informações sobre estas fichas, consulte fichas de [acesso.](access-tokens.md)

A versão simbólica aceite depende do valor dos tipos de **conta Suportada** que escolher quando cria a sua aplicação.

- Se o valor dos tipos de **conta suportada** for contas **em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** , a versão token aceite é v2.0.
- Caso contrário, a versão simbólica aceite é v1.0.

Depois de criar a aplicação, pode determinar ou alterar a versão simbólica aceite seguindo estes passos:

1. No portal Azure, selecione a sua aplicação e, em seguida, selecione **Manifesto**.
1. Encontre o acesso à **propriedadeTokenAcceptedVersion** no manifesto. O valor padrão da propriedade é 2.
1. O valor especifica para o Azure Ative Directory (Azure AD) que token versão a Web API aceita.
    - Se o valor for 2, a Web API aceita fichas v2.0.
    - Se o valor for **nulo,** a Web API aceita fichas v1.0.
1. Se alterar a versão simbólica, selecione **Guardar**.

> [!NOTE]
> A Web API especifica qual versão simbólica que aceita. Quando um cliente solicita um símbolo para a sua Web API a partir da plataforma de identidade da Microsoft (v2.0), o cliente recebe um token que indica qual versão simbólica que a Web API aceita.

## <a name="no-redirect-uri"></a>Sem redirecionar URI

As APIs web não precisam de registar um URI redirecionado porque nenhum utilizador é inscrito interactivamente.

## <a name="exposed-api"></a>API exposto

Outras definições específicas para apis web são a API exposta e os âmbitos expostos.

### <a name="application-id-uri-and-scopes"></a>ID DE aplicação URI e âmbitos

Os âmbitos geralmente têm o formulário `resourceURI/scopeName`. Para o Microsoft Graph, os âmbitos têm atalhos. Por exemplo, `User.Read` é um atalho para `https://graph.microsoft.com/user.read`.

Durante o registo da aplicação, é necessário definir estes parâmetros:

- O recurso URI
- Um ou mais âmbitos
- Um ou mais papéis de aplicativo

Por predefinição, o portal de registo de aplicações recomenda que utilize o recurso URI `api://{clientId}`. Este URI é único, mas não humano legível. Se alterar o URI, certifique-se de que o novo valor é único.

Para aplicações de clientes, os âmbitos aparecem como *permissões delegadas* e as funções de aplicações aparecem como *permissões* de aplicação para a sua Web API.

Os âmbitos também aparecem na janela de consentimento que é apresentada aos utilizadores da sua aplicação. Por isso, é necessário fornecer as cordas correspondentes que descrevem o âmbito:

- Como visto por um utilizador.
- Como visto por um administrador inquilino, que pode conceder o consentimento do administrador.

### <a name="exposing-delegated-permissions-scopes"></a>Expondo permissões delegadas (âmbitos)

1. Selecione **Expor uma API** no registo de candidatura.
1. Selecione **Adicionar âmbito**.
1. Se solicitado, aceite o ID DE aplicação proposto (`api://{clientId}`) selecionando **Guardar e Continuar**.
1. Especifique estes valores:
    - Selecione **o nome Scope** e introduza **access_as_user**.
    - Selecione **Quem pode consentir** e certificar-se de que **os Administradores e utilizadores** são selecionados.
    - Selecione o nome do **ecrã de consentimento do Administrador** e introduza o Access **TodoListService como utilizador**.
    - Selecione **descrição** de consentimento do Administrador e **introduza acessos à API Web TodoListService como utilizador**.
    - Selecione **o nome** do ecrã de consentimento do utilizador e introduza o Access **TodoListService como utilizador**.
    - Selecione **a descrição do consentimento** do utilizador e **introduza a API Web TodoListService como utilizador**.
    - Mantenha o valor **do Estado** definido para **Ativado**.
 1. Selecione **Adicionar escopo**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se a sua Web API for chamada por uma aplicação daemon

Nesta secção, aprende-se a registar a sua API protegida para que as aplicações daemon possam chamá-la de forma segura.

- Declara e expõe apenas *permissões* de aplicações porque as aplicações daemon não interagem com os utilizadores. Permissões delegadas não fariam sentido.
- Os administradores dos inquilinos podem exigir que a AD Azure emita fichas web da API apenas a aplicações que se tenham registado para aceder a uma das permissões de candidatura da API.

#### <a name="exposing-application-permissions-app-roles"></a>Expondo permissões de aplicações (funções de aplicação)

Para expor permissões de pedido, precisa editar o manifesto.

1. No registo de candidatura para a sua candidatura, selecione **Manifesto**.
1. Para editar o manifesto, encontre a definição `appRoles` e adicione funções de aplicação. As definições de funções são fornecidas no seguinte bloco JSON da amostra.
1. Deixe `allowedMemberTypes` apenas para `"Application"`.
1. Certifique-se de que `id` é um GUID único.
1. Certifique-se de que `displayName` e `value` não contenham espaços.
1. Salve o manifesto.

A amostra que se segue mostra o conteúdo de `appRoles`, onde o valor da `id` pode ser um GUIA único.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Garantir que a Azure AD emite fichas para a sua Web API apenas permitiu que os clientes permitissem que os clientes permitissem

A Web API verifica o papel da aplicação. Esta função é a forma de um desenvolvedor de software expor permissões de aplicação. Também pode configurar a AD Azure para emitir tokens API apenas a apps que o administrador do inquilino aprova para acesso à API.

Para adicionar esta segurança acrescida:

1. Vá à página de **visão geral** da aplicação para o registo da sua aplicação.
1. Sob **aplicação gerida no diretório local,** selecione o link com o nome da sua aplicação. O rótulo para esta seleção pode ser truncado. Por exemplo, pode ver a **aplicação gerida em ...**

   > [!NOTE]
   >
   > Quando selecionar este link, vá à página de visão geral da **aplicação da empresa.** Esta página está associada ao diretor de serviço para a sua candidatura no inquilino onde a criou. Pode ir à página de registo da aplicação utilizando o botão traseiro do seu navegador.

1. Selecione a página **Propriedades** na secção **Gerir** as páginas de aplicação da Enterprise.
1. Se pretender que o Azure AD permita o acesso à sua Web API a partir de apenas certos clientes, detete a atribuição do **Utilizador necessária para** **Sim**.

   > [!IMPORTANT]
   >
   > Se definir a atribuição de **utilizador necessária?** Se o cliente não for atribuído a nenhuma função de aplicação, a Azure AD devolverá a mensagem de erro "invalid_client: AADSTS501051: Aplicação \<nome de candidatura\> não é atribuído a um papel para a \<web API\>".
   >
   > Se mantiver a atribuição de **utilizador necessária?** Qualquer cliente daemon, ou seja, qualquer cliente que use o fluxo de credenciais do cliente, pode obter um sinal de acesso para a API apenas especificando o seu público. Qualquer aplicação pode aceder à API sem ter de solicitar permissões para a si.
   >
   > Mas, como explicado na secção anterior, a sua API web pode sempre verificar que a aplicação tem o papel certo, que é autorizado pelo administrador do arrendatário. A API realiza esta verificação validando que o token de acesso tem uma reivindicação de funções e que o valor para esta reclamação está correto. Na amostra JSON anterior, o valor é `access_as_application`.

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração do código de aplicação](scenario-protected-web-api-app-configuration.md)

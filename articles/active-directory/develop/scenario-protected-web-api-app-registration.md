---
title: API Web protegida-registro de aplicativo
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida e as informações necessárias para registrar o aplicativo.
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
ms.openlocfilehash: 0c905dfd86fd80b9e55aa7bd5a9b9b03f277570c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802537"
---
# <a name="protected-web-api-app-registration"></a>API Web protegida: registro de aplicativo

Este artigo explica as especificidades de registro de aplicativo para uma API Web protegida.

Consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md) para obter as etapas comuns para registrar um aplicativo.

## <a name="accepted-token-version"></a>Versão do token aceito

O ponto de extremidade da plataforma de identidade da Microsoft pode emitir dois tipos de tokens: tokens v 1.0 e tokens v 2.0. Para obter mais informações sobre esses tokens, consulte [tokens de acesso](access-tokens.md). A versão de token aceita depende dos **tipos de conta com suporte** que você escolheu quando criou seu aplicativo:

- Se o valor de **tipos de conta com suporte** for **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** , a versão de token aceita será v 2.0.
- Caso contrário, a versão do token aceito será v 1.0.

Depois de criar o aplicativo, você pode determinar ou alterar a versão do token aceito seguindo estas etapas:

1. No portal do Azure, selecione seu aplicativo e, em seguida, selecione o **manifesto** para seu aplicativo.
2. No manifesto, pesquise por **"accessTokenAcceptedVersion"** . Observe que seu valor é **2**. Esta propriedade especifica para Azure Active Directory (Azure AD) que a API da Web aceita tokens v 2.0. Se o valor for **NULL**, a versão do token aceito será v 1.0.
3. Se você alterou a versão do token, selecione **salvar**.

> [!NOTE]
> A API da Web especifica qual versão do token (v 1.0 ou v 2.0) aceita. Quando os clientes solicitarem um token para sua API Web do ponto de extremidade da plataforma Microsoft Identity (v 2.0), eles obterão um token que indica qual versão é aceita pela API da Web.

## <a name="no-redirect-uri"></a>Nenhum URI de redirecionamento

APIs da Web não precisam registrar um URI de redirecionamento porque nenhum usuário está conectado interativamente.

## <a name="expose-an-api"></a>Expor uma API

Outra configuração específica para APIs Web é a API exposta e os escopos expostos.

### <a name="resource-uri-and-scopes"></a>URI de recurso e escopos

Os escopos geralmente estão no formato `resourceURI/scopeName`. Por Microsoft Graph, os escopos têm atalhos como `User.Read`. Essa cadeia de caracteres é um atalho para `https://graph.microsoft.com/user.read`.

Durante o registro do aplicativo, você precisará definir esses parâmetros:

- O URI do recurso. Por padrão, o portal de registro de aplicativos recomenda que você use `api://{clientId}`. Esse URI de recurso é exclusivo, mas não é legível por humanos. Você pode alterá-lo, mas certifique-se de que o novo valor seja exclusivo.
- Um ou mais *escopos*. (Para aplicativos cliente, eles aparecerão como *permissões delegadas* para sua API Web.)
- Uma ou mais *funções de aplicativo*. (Para aplicativos cliente, eles aparecerão como *permissões de aplicativo* para sua API Web.)

Os escopos também são exibidos na tela de consentimento que é apresentada aos usuários finais do seu aplicativo. Portanto, você precisará fornecer as cadeias de caracteres correspondentes que descrevem o escopo:

- Como visto pelo usuário final.
- Conforme visto pelo administrador de locatários, que pode conceder consentimento de administrador.

### <a name="exposing-delegated-permissions-scopes"></a>Expondo permissões delegadas (escopos)

1. Selecione a seção **expor uma API** no registro do aplicativo.
1. Selecione **Adicionar âmbito**.
1. Se solicitado, aceite o URI da ID do aplicativo proposto (`api://{clientId}`) selecionando **salvar e continuar**.
1. Insira estes parâmetros:
      - Para **nome do escopo**, use **access_as_user**.
      - Para **quem pode consentir**, verifique se **Administradores e usuários** está selecionado.
      - Em **nome de exibição do consentimento do administrador**, digite **TodoListService de acesso como um usuário**.
      - Em **Descrição de consentimento do administrador**, insira **acessa a API Web TodoListService como um usuário**.
      - Em **nome de exibição do consentimento do usuário**, digite **TodoListService de acesso como um usuário**.
      - Em **Descrição de consentimento do usuário**, insira **acessa a API Web TodoListService como um usuário**.
      - Mantenha o **estado** definido como **habilitado**.
      - Selecione **Adicionar escopo**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se sua API Web for chamada por um aplicativo daemon

Nesta seção, você aprenderá a registrar sua API Web protegida para que ela possa ser chamada com segurança por aplicativos de daemon.

- Você precisará expor *permissões de aplicativo*. Você irá declarar somente permissões de aplicativo porque os aplicativos daemon não interagem com usuários, portanto, permissões delegadas não farão sentido.
- Os administradores de locatários podem exigir Azure Active Directory (Azure AD) para emitir tokens para sua API Web somente para aplicativos que se registraram para acessar uma das permissões de aplicativo da API Web.

#### <a name="exposing-application-permissions-app-roles"></a>Expondo permissões de aplicativo (funções de aplicativo)

Para expor permissões de aplicativo, você precisará editar o manifesto.

1. No registro do aplicativo para seu aplicativo, selecione **manifesto**.
1. Edite o manifesto localizando a configuração de `appRoles` e adicionando uma ou mais funções de aplicativo. A definição de função é fornecida no bloco JSON de exemplo a seguir. Deixe a `allowedMemberTypes` definida como `"Application"` apenas. Verifique se o `id` é um GUID exclusivo e se `displayName` e `value` não contêm espaços.
1. Salve o manifesto.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Garantindo que o Azure AD emita tokens para sua API Web somente para clientes permitidos

A API da Web verifica a função do aplicativo. (Essa é a maneira do desenvolvedor de expor permissões de aplicativo.) Mas você também pode configurar o Azure AD para emitir um token para sua API Web somente para aplicativos que são aprovados pelo administrador de locatários para acessar sua API. Para adicionar esse aumento de segurança:

1. Na página **visão geral** do aplicativo para o registro do aplicativo, selecione o link com o nome do seu aplicativo em **aplicativo gerenciado no diretório local**. O título deste campo pode ser truncado. Você pode, por exemplo, ver **aplicativo gerenciado em.** ..

   > [!NOTE]
   >
   > Ao selecionar esse link, você vai para a página **visão geral do aplicativo empresarial** associada à entidade de serviço para seu aplicativo no locatário em que você o criou. Você pode navegar de volta para a página de registro do aplicativo usando o botão voltar do seu navegador.

1. Selecione a página **Propriedades** na seção **gerenciar** das páginas do aplicativo empresarial.
1. Se você quiser que o Azure AD permita o acesso à sua API Web somente de determinados clientes, defina **atribuição de usuário necessária?** para **Sim**.

   > [!IMPORTANT]
   >
   > Se você definir a **atribuição de usuário necessária?** para **Sim**, o Azure ad verificará as atribuições de função de aplicativo de clientes quando eles solicitarem um token de acesso para a API Web. Se o cliente não estiver atribuído a nenhuma função de aplicativo, o Azure AD retornará o erro `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Se você mantiver a **atribuição de usuário necessária?** definida como **não**, *o Azure ad não verificará as atribuições de função de aplicativo quando um cliente solicitar um token de acesso para sua API Web*. Qualquer cliente daemon (ou seja, qualquer cliente que use o fluxo de credenciais do cliente) poderá obter um token de acesso para a API apenas especificando seu público-alvo. Qualquer aplicativo poderá acessar a API sem precisar solicitar permissões para ela. Mas sua API Web sempre pode, conforme explicado na seção anterior, verifique se o aplicativo tem a função certa (que é autorizada pelo administrador de locatário). A API executa essa verificação Validando se o token de acesso tem uma declaração de funções e se o valor dessa declaração está correto. (Em nosso caso, o valor é `access_as_application`.)

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-protected-web-api-app-configuration.md)

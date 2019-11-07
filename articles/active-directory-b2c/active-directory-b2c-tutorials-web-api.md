---
title: Tutorial-conceder acesso a uma API Web do ASP.NET-Azure Active Directory B2C
description: Tutorial sobre como usar Active Directory B2C para proteger uma API Web ASP.NET e chamá-la de um aplicativo Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0cd2eff7e3fafc1a6f3418a1f86629cfb1584e89
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641757"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: conceder acesso a uma API Web do ASP.NET usando Azure Active Directory B2C

Este tutorial mostra como chamar um recurso de API Web protegido em Azure Active Directory B2C (Azure AD B2C) de um aplicativo Web ASP.NET.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [tutorial: habilitar a autenticação em um aplicativo Web usando o Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Os recursos da API Web precisam ser registrados em seu locatário antes que possam aceitar e responder a solicitações de recursos protegidos por aplicativos cliente que apresentam um token de acesso.

Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **incluir aplicativo Web/API Web**, selecione **Sim**.
7. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:44332`.
8. Para **URI da ID do aplicativo**, insira o identificador usado para sua API Web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *webapi1*.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:44332`.
1. Selecione **Registar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

* * *

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões de aplicativo para a API. No tutorial de pré-requisito, você criou um aplicativo Web em Azure AD B2C chamado *webapp1*. Você usa esse aplicativo para chamar a API da Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Seu aplicativo está registrado para chamar a API Web protegida. Um usuário é autenticado com Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure a API Web para usar seu locatário Azure AD B2C. Neste tutorial, vai configurar uma API Web de exemplo. A API Web de exemplo está incluída no projeto que você baixou no tutorial de pré-requisito.

Existem dois projetos na solução de exemplo:

* **TaskWebApp** -criar e editar uma lista de tarefas. O exemplo usa o fluxo de usuário de inscrição **ou de entrada** para inscrever-se ou conectar usuários.
* **TaskService** -dá suporte à funcionalidade criar, ler, atualizar e excluir lista de tarefas. A API é protegida por Azure AD B2C e chamada pelo TaskWebApp.

### <a name="configure-the-web-application"></a>Configurar o aplicativo Web

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp** , abra **Web. config**.
1. Para executar a API localmente, utilize a definição de localhost **api:TaskServiceUrl**. Altere o Web.config da seguinte forma:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Configure o URI da API. Esse é o URI que o aplicativo Web usa para fazer a solicitação de API. Além disso, configure as permissões pedidas.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. No projeto **TaskService** , abra **Web. config**.
1. Configure a API para utilizar o seu inquilino.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Defina a ID do cliente para a ID do aplicativo do seu aplicativo de API Web registrado, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Atualize a configuração de fluxo de usuário com o nome do seu fluxo de usuário de inscrição e entrada, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configure a configuração de escopos para corresponder às que você criou no Portal.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Tem de executar os dois projetos, **TaskWebApp** e **TaskService**.

1. No Explorador de Soluções, clique com o botão direito do rato na solução e selecione **Definir Projetos de Arranque...** .
1. Selecione **vários projetos de inicialização**.
1. Altere a **Ação** em ambos os projetos para **Iniciar**.
1. Clique em **OK** para salvar a configuração.
1. Prima **F5** para executar as duas aplicações. Cada aplicativo é aberto em sua própria janela do navegador.
    * `https://localhost:44316/` é o aplicativo Web.
    * `https://localhost:44332/` é a API Web.

1. No aplicativo Web, selecione **inscrever-se/entrar** para entrar no aplicativo Web. Use a conta que você criou anteriormente.
1. Depois de entrar, selecione **lista de tarefas pendentes** e crie um item de lista de tarefas pendentes.

Quando você cria um item de lista de tarefas pendentes, o aplicativo Web faz uma solicitação para a API da Web para gerar o item de lista de tarefas pendentes. Seu aplicativo Web protegido está chamando a API da Web protegida por Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)

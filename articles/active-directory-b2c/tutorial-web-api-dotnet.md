---
title: 'Tutorial: Conceder acesso a uma API web ASP.NET'
titleSuffix: Azure AD B2C
description: Tutorial sobre como usar o Ative Directory B2C para proteger uma API web ASP.NET e chamá-lo de uma aplicação web ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ada4226500437e7733605a29988ee7cacae40761
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953088"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API web ASP.NET usando o Azure Ative Directory B2C

Este tutorial mostra-lhe como chamar um recurso de API web protegido em Azure Ative Directory B2C (Azure AD B2C) a partir de uma aplicação web ASP.NET.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões à Web API
> * Configure a amostra para utilizar a aplicação

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Preencha os passos e requisitos em [Tutorial: Ative a autenticação numa aplicação web utilizando o Azure Ative Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Os recursos da Web API precisam de ser registados no seu inquilino antes de poderem aceitar e responder a pedidos de recursos protegidos por aplicações de clientes que apresentem um token de acesso.

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *webapi1*.
1. Em **Redirect URI**, selecione **Web**, e, em seguida, introduza um ponto final onde a Azure AD B2C deve devolver quaisquer fichas que a sua candidatura solicite. Neste tutorial, a amostra corre localmente e `https://localhost:44332` ouve.
1. Selecione **Registar**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **Aplicações (Legado)** e, em seguida, **selecione Adicionar**.
5. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir aplicativo web/web API,** selecione **Sim**.
7. Para **URL de resposta,** insira um ponto final onde a Azure AD B2C deve devolver quaisquer fichas que o seu pedido de candidatura. Neste tutorial, a amostra corre localmente e `https://localhost:44332` ouve.
8. Para **app ID URI,** insira o identificador utilizado para a sua API web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, grave o ID da aplicação que utilizará quando configurar a aplicação web.

* * *

## <a name="configure-scopes"></a>Configure âmbitos

Os âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de uma aplicação, precisa conceder as permissões de sua candidatura à API. No tutorial pré-requisito, criou uma aplicação web em Azure AD B2C chamada *webapp1*. Você usa esta aplicação para ligar para a API web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

A sua aplicação está registada para ligar para a API web protegida. Um utilizador autentica-se com Azure AD B2C para utilizar a aplicação. O pedido obtém uma autorização da Azure AD B2C para aceder à API web protegida.

## <a name="configure-the-sample"></a>Configure a amostra

Agora que a API web está registada e tem âmbitos definidos, configura a API web para usar o seu inquilino Azure AD B2C. Neste tutorial, vai configurar uma API Web de exemplo. A amostra web API está incluída no projeto que descarregou no tutorial pré-requisito.

Existem dois projetos na solução de exemplo:

* **TaskWebApp** - Criar e editar uma lista de tarefas. A amostra utiliza o fluxo de utilizador **de inscrição ou de inscrição** para se inscrever ou iniciar sê-lo nos utilizadores.
* **TaskService** - Suporta a funcionalidade de criar, ler, atualizar e eliminar a funcionalidade da lista de tarefas. A API está protegida pelo Azure AD B2C e é chamada pelo TaskWebApp.

### <a name="configure-the-web-application"></a>Configure a aplicação web

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp,** abra **Web.config**.
1. Para executar a API localmente, utilize a definição de localhost **api:TaskServiceUrl**. Altere o Web.config da seguinte forma:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Configure o URI da API. Este é o URI que a aplicação web utiliza para fazer o pedido da API. Além disso, configure as permissões pedidas.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. No projeto **TaskService,** abra **Web.config**.
1. Configure a API para utilizar o seu inquilino.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Descreva o ID do cliente no ID de aplicação da sua aplicação de API web registada, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Atualize a definição de fluxo do utilizador com o nome da sua inscrição e fluxo de utilizador de entrada, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configure a definição de âmbitos para corresponder às que criou no portal.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Tem de executar os dois projetos, **TaskWebApp** e **TaskService**.

1. No Explorador de Soluções, clique com o botão direito do rato na solução e selecione **Definir Projetos de Arranque...**.
1. Selecione **vários projetos de startups.**
1. Altere a **Ação** em ambos os projetos para **Iniciar**.
1. Clique **em OK** para guardar a configuração.
1. Prima **F5** para executar as duas aplicações. Cada aplicação abre na sua própria janela de navegador.
    * `https://localhost:44316/` é a aplicação web.
    * `https://localhost:44332/` é a API Web.

1. Na aplicação web, selecione **iniciar s inscrição /iniciar sôms** para iniciar sins in sê-lo na aplicação web. Utilize a conta que criou anteriormente.
1. Depois de iniciar sessão, selecione **a lista de A fazer** e crie um item de lista de coisas a fazer.

Quando cria um item de lista de a fazer, a aplicação web faz um pedido à API web para gerar o item da lista de a fazer. A sua aplicação web protegida está a chamar a API web protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões à Web API
> * Configure a amostra para utilizar a aplicação

> [!div class="nextstepaction"]
> [Tutorial: Adicione fornecedores de identidade às suas aplicações no Azure Ative Directory B2C](tutorial-add-identity-providers.md)
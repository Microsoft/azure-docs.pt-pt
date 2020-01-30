---
title: 'Tutorial: Conceder acesso a uma API web ASP.NET'
titleSuffix: Azure AD B2C
description: Tutorial sobre como usar o Ative Directory B2C para proteger uma API web ASP.NET e chamá-lo de uma aplicação web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: fe6f8469354922dcf38a987b9b4371547678eb36
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850098"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API web ASP.NET utilizando o Diretório Ativo Azure B2C

Este tutorial mostra-lhe como chamar um recurso API web protegido no Azure Ative Directory B2C (Azure AD B2C) a partir de uma aplicação web ASP.NET.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar os âmbitos para uma API web
> * Conceder permissões à Web API
> * Configurar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos e pré-requisitos no Tutorial: Ativar a [autenticação numa aplicação web utilizando o Diretório Ativo Azure B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Os recursos da Web API precisam de ser registados no seu inquilino antes de poderem aceitar e responder a pedidos de recursos protegidos por aplicações de clientes que apresentem um sinal de acesso.

Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
5. Insira um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir web app/ web API,** selecione **Sim**.
7. Para **o URL de resposta,** insira um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Neste tutorial, a amostra corre localmente e ouve `https://localhost:44332`.
8. Para **o ID da aplicação URI,** introduza o identificador utilizado para a sua Web API. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, grave o ID da aplicação que utilizará quando configurar a aplicação web.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *webapi1*.
1. Em **Redirecione o URI**, selecione **Web**e, em seguida, introduza um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Neste tutorial, a amostra corre localmente e ouve `https://localhost:44332`.
1. Selecione **Registar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

* * *

## <a name="configure-scopes"></a>Configurar os âmbitos

Os âmbitos fornecem uma forma de governar o acesso aos recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de uma aplicação, você precisa conceder as suas permissões de pedido para a API. No tutorial pré-requisito, criou uma aplicação web em Azure AD B2C chamada *webapp1*. Usa esta aplicação para chamar a Web API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

A sua aplicação está registada para chamar a API da web protegida. Um utilizador autentica com Azure AD B2C para utilizar a aplicação. O pedido obtém uma autorização do Azure AD B2C para aceder à API web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a Web API está registada e tem âmbitos definidos, configura a Web API para utilizar o seu inquilino Azure AD B2C. Neste tutorial, vai configurar uma API Web de exemplo. A API web da amostra está incluída no projeto que descarregou no tutorial pré-requisito.

Existem dois projetos na solução de exemplo:

* **TaskWebApp** -criar e editar uma lista de tarefas. A amostra utiliza o fluxo de utilizador de **inscrição ou de entrada** para se inscrever ou inscrever nos utilizadores.
* **TaskService** -dá suporte à funcionalidade criar, ler, atualizar e excluir lista de tarefas. A API é protegida por Azure AD B2C e chamada pelo TaskWebApp.

### <a name="configure-the-web-application"></a>Configurar a aplicação web

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp,** abra **web.config**.
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

1. No projeto **TaskService,** abra **Web.config.**
1. Configure a API para utilizar o seu inquilino.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Detete o ID do cliente para o ID de aplicação da sua aplicação API web registada, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Atualize a definição de fluxo do utilizador com o nome do fluxo de utilizador de inscrição e de entrada, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configure a definição de âmbitos para combinar com as que criou no portal.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Tem de executar os dois projetos, **TaskWebApp** e **TaskService**.

1. No Explorador de Soluções, clique com o botão direito do rato na solução e selecione **Definir Projetos de Arranque...** .
1. Selecione **Vários projetos**de arranque.
1. Altere a **Ação** em ambos os projetos para **Iniciar**.
1. Clique **em OK** para salvar a configuração.
1. Prima **F5** para executar as duas aplicações. Cada aplicação abre na sua própria janela de navegador.
    * `https://localhost:44316/` é a aplicação web.
    * `https://localhost:44332/` é a API Web.

1. Na aplicação web, selecione **iniciar sessão/iniciar sessão** para iniciar sessão na aplicação web. Utilize a conta que criou anteriormente.
1. Depois de iniciar sessão, selecione **lista de afazeres** e crie um item de lista de afazeres.

Quando cria um item de lista de afazeres, a aplicação web faz um pedido à API web para gerar o item da lista de afazeres. A sua aplicação web protegida está a chamar a Web API protegida por Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar os âmbitos para uma API web
> * Conceder permissões à Web API
> * Configurar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicione fornecedores de identidade às suas aplicações no Diretório Ativo Azure B2C](tutorial-add-identity-providers.md)

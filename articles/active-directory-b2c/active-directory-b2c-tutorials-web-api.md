---
title: Tutorial-conceder acesso a uma API Web do ASP.NET-Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar Active Directory B2C para proteger uma API Web ASP.NET e chamá-la de um aplicativo Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 339b118e48a01469312a40e6b0652a4ffb90291a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347125"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web do ASP.NET usando Azure Active Directory B2C

Este tutorial mostra como chamar um recurso de API Web protegido no Azure Active Directory (Azure AD) B2C de um aplicativo Web ASP.NET.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos [no tutorial: Habilite a autenticação em um aplicativo Web](active-directory-b2c-tutorials-web-app.md)usando Azure Active Directory B2C.

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Os recursos da API Web precisam ser registrados em seu locatário antes que possam aceitar e responder a solicitações de recursos protegidos por aplicativos cliente que apresentam um token de acesso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **incluir aplicativo Web/API Web** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:44332`.
8. Para **URI da ID do aplicativo**, insira o identificador usado para sua API Web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapi1*.
2. Selecione **escopos publicados**.
3. Para **escopo**, insira `Hello.Read`e, para descrição, digite `Read access to hello`.
4. Para **escopo**, insira `Hello.Write`e, para descrição, digite `Write access to hello`.
5. Clique em **Guardar**.

Os escopos publicados podem ser usados para conceder a uma permissão de aplicativo cliente para a API da Web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões de aplicativo para a API. No tutorial de pré-requisito, você criou um aplicativo Web em Azure AD B2C chamado *webapp1*. Você usa esse aplicativo para chamar a API da Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapp1*.
2. Selecione **acesso à API**e, em seguida, selecione **Adicionar**.
3. Na lista suspensa **selecionar API** , selecione *webapi1*.
4. Na lista suspensa **selecionar** escopos, selecione os escopos **Hello. Read** e **Hello. Write** que você definiu anteriormente.
5. Clique em **OK**.

Seu aplicativo está registrado para chamar a API Web protegida. Um usuário é autenticado com Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure a API Web para usar seu locatário Azure AD B2C. Neste tutorial, vai configurar uma API Web de exemplo. A API Web de exemplo está incluída no projeto que você baixou no tutorial de pré-requisito.

Existem dois projetos na solução de exemplo:

Os dois projetos a seguir estão na solução de exemplo:

- **TaskWebApp** -criar e editar uma lista de tarefas. O exemplo usa o fluxo de usuário de inscrição **ou de entrada** para inscrever-se ou conectar usuários.
- **TaskService** -dá suporte à funcionalidade criar, ler, atualizar e excluir lista de tarefas. A API é protegida por Azure AD B2C e chamada pelo TaskWebApp.

### <a name="configure-the-web-application"></a>Configurar o aplicativo Web

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
2. Abra **Web.config** no projeto **TaskWebApp**.
3. Para executar a API localmente, utilize a definição de localhost **api:TaskServiceUrl**. Altere o Web.config da seguinte forma: 

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configure o URI da API. Esse é o URI que o aplicativo Web usa para fazer a solicitação de API. Além disso, configure as permissões pedidas.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra **Web.config** no projeto **TaskService**.
2. Configure a API para utilizar o seu inquilino.

    ```csharp
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Defina o ID de cliente como o ID da Aplicação registada para a API.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Atualize a configuração de fluxo de usuário com o nome do fluxo de usuário de inscrição e de entrada.

    ```csharp
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Configure a definição de âmbitos para que corresponda ao que criou no portal.

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Tem de executar os dois projetos, **TaskWebApp** e **TaskService**. 

1. No Explorador de Soluções, clique com o botão direito do rato na solução e selecione **Definir Projetos de Arranque...** . 
2. Selecione **vários projetos de inicialização**.
3. Altere a **Ação** em ambos os projetos para **Iniciar**.
4. Clique em **OK** para salvar a configuração.
5. Prima **F5** para executar as duas aplicações. Cada aplicativo é aberto em sua própria guia do `https://localhost:44316/` navegador. é o aplicativo Web.
    `https://localhost:44332/` é a API Web.

6. No aplicativo Web, clique em **inscrever-se/entrar** para entrar no aplicativo Web. Use a conta que você criou anteriormente. 
7. Depois de entrar, clique em **lista de tarefas** e crie um item de lista de tarefas pendentes.

Quando você cria um item de lista de tarefas pendentes, o aplicativo Web faz uma solicitação para a API da Web para gerar o item de lista de tarefas pendentes. Você está protegido o aplicativo Web está chamando a API Web protegida em seu locatário Azure AD B2C.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)

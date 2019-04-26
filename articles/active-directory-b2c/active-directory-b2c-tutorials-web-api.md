---
title: Tutorial - conceder acesso a uma API web ASP.NET - Azure Active Directory B2C | Documentos da Microsoft
description: Tutorial sobre como utilizar o Active Directory B2C para proteger uma API web ASP.NET e chamá-lo a partir de uma aplicação web ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cc4db0f2fe8f5db41f6e8332a398029bd105f3af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315600"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API através do Azure Active Directory B2C web ASP.NET

Este tutorial mostra-lhe como chamar um recurso de API web protegido no Azure Active Directory (Azure AD) B2C a partir de uma aplicação web ASP.NET.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões para a API web
> * Configurar o exemplo a utilizar a aplicação

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos e a pré-requisitos no [Tutorial: Ativar autenticação numa aplicação web através do Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Recursos da API Web têm de ser registados no seu inquilino antes de poderem aceitar e responder a pedidos de recursos protegidos por aplicações cliente que apresentam um token de acesso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir a aplicação web / web API** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, introduza um ponto de extremidade em que o Azure AD B2C deverá devolver quaisquer tokens que a aplicação solicite. Neste tutorial, o exemplo é executada localmente e escuta na `https://localhost:44332`.
8. Para **URI de ID de aplicação**, introduza o identificador utilizado para a API web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar a aplicação web.

## <a name="configure-scopes"></a>Configurar âmbitos

Âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapi1*.
2. Selecione **âmbitos publicados**.
3. Para **âmbito**, introduza `Hello.Read`e para a descrição, insira `Read access to hello`.
4. Para **âmbito**, introduza `Hello.Write`e para a descrição, insira `Write access to hello`.
5. Clique em **Guardar**.

Os âmbitos publicados podem ser utilizados para conceder um cliente permissão da aplicação para a API web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida a partir de uma aplicação, tem de conceder as permissões de aplicação para a API. O tutorial de pré-requisitos, criou uma aplicação web no Azure AD B2C com o nome *webapp1*. Utilizar esta aplicação para chamar a API web.

1. Selecione **aplicativos**e, em seguida, selecione *webapp1*.
2. Selecione **acesso à API**e, em seguida, selecione **Add**.
3. Na **selecionar API** menu pendente, selecione *webapi1*.
4. Na **selecionar âmbitos** menu pendente, selecione a **Hello.Read** e **Hello.Write** âmbitos que definiu anteriormente.
5. Clique em **OK**.

A aplicação fica registada para chamar a API web protegida. Um utilizador é autenticado com o Azure AD B2C para utilizar a aplicação. A aplicação obtém uma concessão de autorização do Azure AD B2C para aceder à API web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API web está registada e já estão definidos âmbitos, configurar a API web para utilizar o seu inquilino do Azure AD B2C. Neste tutorial, vai configurar uma API Web de exemplo. A API da web de exemplo está incluída no projeto que transferiu no tutorial de pré-requisito.

Existem dois projetos na solução de exemplo:

São os seguintes dois projetos na solução de exemplo:

- **TaskWebApp** - criar e editar uma lista de tarefas. Este exemplo utiliza a **inscrição ou início de sessão** fluxo de utilizador para inscrever-se ou iniciar sessão dos utilizadores.
- **TaskService** - oferece suporte a criar, ler, atualizar e eliminar a funcionalidade de lista de tarefas. A API é protegida pelo Azure AD B2C e chamada pela TaskWebApp.

### <a name="configure-the-web-application"></a>Configurar a aplicação web

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
2. Abra **Web.config** no projeto **TaskWebApp**.
3. Para executar a API localmente, utilize a definição de localhost **api:TaskServiceUrl**. Altere o Web.config da seguinte forma: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configure o URI da API. Este é o URI a aplicação web utiliza para fazer o pedido de API. Além disso, configure as permissões pedidas.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra **Web.config** no projeto **TaskService**.
2. Configure a API para utilizar o seu inquilino.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Defina o ID de cliente como o ID da Aplicação registada para a API.

    ```C#
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Fluxo de atualização, o utilizador fluxo definição com o nome do sinal de cópia de segurança e de início de sessão do utilizador.

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Configure a definição de âmbitos para que corresponda ao que criou no portal.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Tem de executar os dois projetos, **TaskWebApp** e **TaskService**. 

1. No Explorador de Soluções, clique com o botão direito do rato na solução e selecione **Definir Projetos de Arranque...**. 
2. Selecione **vários projetos de arranque**.
3. Altere a **Ação** em ambos os projetos para **Iniciar**.
4. Clique em **OK** para guardar a configuração.
5. Prima **F5** para executar as duas aplicações. Cada aplicação abre no seu próprio separador do browser. `https://localhost:44316/` é o aplicativo web.
    `https://localhost:44332/` é a API Web.

6. No aplicativo da web, clique em **inscrição / início de sessão** para iniciar sessão para a aplicação web. Utilize a conta que criou anteriormente. 
7. Depois de iniciar sessão, clique em **lista de tarefas** e criar um item de lista de tarefas.

Quando cria um item de lista de tarefas, o aplicativo web faz um pedido para a API web para gerar esse item. Protegida aplicação web é chamar a API de web protegida no seu inquilino do Azure AD B2C.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões para a API web
> * Configurar o exemplo a utilizar a aplicação

> [!div class="nextstepaction"]
> [Tutorial: Adicionar fornecedores de identidade às suas aplicações no Azure Active Directory B2C](tutorial-add-identity-providers.md)

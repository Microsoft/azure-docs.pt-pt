---
title: 'Tutorial: Ativar a autenticação numa aplicação web'
titleSuffix: Azure AD B2C
description: Tutorial sobre como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para uma aplicação Web ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183333"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Ativar a autenticação numa aplicação web utilizando o Diretório Ativo Azure B2C

Este tutorial mostra-lhe como utilizar o Azure Ative Directory B2C (Azure AD B2C) para iniciar sessão e inscrever os utilizadores numa aplicação web ASP.NET. O Azure AD B2C permite que as suas aplicações se autentiquem a contas sociais, contas empresariais e contas de Diretório Ativo Azure utilizando protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação em Azure AD B2C
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se utilizando o fluxo do utilizador

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Crie fluxos de utilizador](tutorial-create-user-flows.md) para permitir as experiências do utilizador na sua aplicação.
* Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a **carga de trabalho de ASP.NET e desenvolvimento web.**

## <a name="update-the-application-registration"></a>Atualizar o registo de candidatura

No tutorial que completou como parte dos pré-requisitos, registou uma aplicação web no Azure AD B2C. Para permitir a comunicação com a amostra neste tutorial, é necessário adicionar um URI redirecionado e criar um segredo de cliente (chave) para a aplicação registada.

### <a name="add-a-redirect-uri-reply-url"></a>Adicione um URI redireccional (URL de resposta)

Pode utilizar a experiência atual de **Aplicações** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização)** para atualizar a aplicação. [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações**e, em seguida, selecione a aplicação *webapp1.*
1. Under **Answer URL,** add `https://localhost:44316`.
1. Selecione **Guardar**.
1. Na página de propriedades, grave o ID da aplicação para utilização num passo posterior quando configurar a aplicação web.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione registos de **aplicações (Pré-visualização)**- selecione o separador **de aplicações Owned** e, em seguida, selecione a aplicação *webapp1.*
1. **Selecione Autenticação**e, em seguida, selecione **Experimente a nova experiência** (se mostrada).
1. Na **Web,** selecione a `https://localhost:44316`ligação Adicionar **URI,** introduza e, em seguida, selecione **Guardar**.
1. Selecione **Descrição geral**.
1. Grave o ID da **Aplicação (cliente)** para utilização num passo posterior quando configurar a aplicação web.

* * *

### <a name="create-a-client-secret"></a>Criar um segredo de cliente

Em seguida, crie um segredo de cliente para a aplicação web registada. A amostra do código de aplicação web utiliza-o para provar a sua identidade ao solicitar fichas.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Configure a amostra

Neste tutorial, configura uma amostra que pode descarregar a partir do GitHub. A amostra usa ASP.NET para fornecer uma lista simples de fazer. A amostra utiliza componentes de [middleware Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o exemplo a partir do GitHub. Certifique-se de que extrai o ficheiro de exemplo numa pasta em que o comprimento de carateres total do caminho seja inferior a 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Os dois projetos seguintes encontram-se na solução de amostragem:

* **TaskWebApp** - Criar e editar uma lista de tarefas. A amostra utiliza o fluxo de utilizador de **inscrição ou de entrada** para se inscrever e inscrever nos utilizadores.
* **TaskService** - Suporta a criação, leitura, atualização e eliminação da funcionalidade da lista de tarefas. A API está protegida por Azure AD B2C e chamada por TaskWebApp.

Altera a amostra para utilizar a aplicação registada no seu inquilino, que inclui o ID da aplicação e a chave que registou anteriormente. Também configura os fluxos de utilizador que criou. A amostra define os valores de configuração como configurações no ficheiro *Web.config.*

Atualize as definições no ficheiro Web.config para funcionar com o fluxo do utilizador:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp,** abra o ficheiro **Web.config.**
    1. Atualize o `ida:Tenant` `ida:AadInstance` valor e com o nome do inquilino Azure AD B2C que criou. Por exemplo, `fabrikamb2c` `contoso`substitua-o por .
    1. Substitua o `ida:ClientId` valor do ID da aplicação que gravou.
    1. Substitua o valor de `ida:ClientSecret` pela chave que registou. Se o segredo do cliente contiver quaisquer entidades`<`XML`>`predefinidas, por`&`exemplo menos`"`do que ( ( ( ( ( ( ou citação dupla), você deve escapar a esses caracteres codificando o segredo do cliente antes de adicioná-lo ao seu Web.config.
    1. Substitua o `ida:SignUpSignInPolicyId` `b2c_1_signupsignin1`valor de .
    1. Substitua o `ida:EditProfilePolicyId` `b2c_1_profileediting1`valor de .
    1. Substitua o `ida:ResetPasswordPolicyId` `b2c_1_passwordreset1`valor de .

## <a name="run-the-sample"></a>Executar o exemplo

1. No Solution Explorer, clique no projeto **TaskWebApp** e, em seguida, clique em **Definir como StartUp Project**.
1. Prima **F5**. É iniciado o browser predefinido para o endereço do site local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. **Selecione Iniciar sessão / Iniciar** sessão para se inscrever como utilizador da aplicação. O **fluxo de utilizador b2c_1_signupsignin1** é utilizado.
1. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, **selecione Inscrever-se agora**. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a palavra-passe do utilizador e os atributos solicitados definidos no fluxo do utilizador.
1. Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de inscrição/inscrição](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Selecione **Criar** para criar uma conta local no inquilino Azure AD B2C.

O utilizador da aplicação pode agora utilizar o seu endereço de e-mail para iniciar sessão e utilizar a aplicação web.

No entanto, a funcionalidade **Lista de A-Do** não funcionará até completar o próximo tutorial da série, [Tutorial: Use Azure AD B2C para proteger uma ASP.NET Web API](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação em Azure AD B2C
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se utilizando o fluxo do utilizador

Agora passe para o próximo tutorial para ativar a funcionalidade **De A-Do List** da aplicação web. Nele, regista uma aplicação Web API no seu próprio inquilino Azure AD B2C e, em seguida, modifica a amostra de código para usar o seu inquilino para autenticação API.

> [!div class="nextstepaction"]
> [Tutorial: Utilize o Diretório Ativo Azure B2C para proteger uma ASP.NET web API >](tutorial-web-api-dotnet.md)

---
title: 'Tutorial: Ativar a autenticação numa aplicação web'
titleSuffix: Azure AD B2C
description: Tutorial sobre como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para uma aplicação Web ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c3c63b6116e02e8a742b69e90c11e182d72ab2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953037"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Ativar a autenticação numa aplicação web utilizando o Azure Ative Directory B2C

Este tutorial mostra-lhe como usar o Azure Ative Directory B2C (Azure AD B2C) para iniciar seduções e inscrever utilizadores numa aplicação web ASP.NET. O Azure AD B2C permite que as suas aplicações autentem para as contas sociais, contas empresariais e contas do Azure Ative Directory utilizando protocolos de série aberta.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação em Azure AD B2C
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se usando o fluxo do utilizador

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Este tutorial usa uma aplicação web de amostra ASP.NET. Para outras aplicações de amostra (incluindo ASP.NET Core, Node.js, Python, entre outros), consulte [amostras de código do Azure Ative Directory B2C](code-samples.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Crie fluxos](tutorial-create-user-flows.md) de utilizador para permitir experiências do utilizador na sua aplicação.
* Instale [o Visual Studio 2019](https://www.visualstudio.com/downloads/) com a ASP.NET e a carga de trabalho **de desenvolvimento web.**

## <a name="update-the-application-registration"></a>Atualizar o registo de pedidos

No tutorial que completou como parte dos pré-requisitos, registou uma aplicação web em Azure AD B2C. Para ativar a comunicação com a amostra neste tutorial, é necessário adicionar um URI de redirecionamento e criar um segredo de cliente (chave) para a aplicação registada.

### <a name="add-a-redirect-uri-reply-url"></a>Adicione um URI de redirecionamento (URL de resposta)

Para atualizar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de Aplicações**, selecione o **separador aplicações Próprias** e, em seguida, selecione a aplicação *webapp1.*
1. Na **Web**, selecione a ligação **Add URI,** introduza `https://localhost:44316` e, em seguida, selecione **Save**.
1. Selecione **Descrição geral**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior quando configurar a aplicação web.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações (Legado)** e, em seguida, selecione a aplicação *webapp1.*
1. Em **URL de resposta**, adicione `https://localhost:44316` .
1. Selecione **Guardar**.
1. Na página de propriedades, grave o ID da aplicação para utilização num passo posterior quando configurar a aplicação web.

* * *

### <a name="create-a-client-secret"></a>Criar um segredo de cliente

Em seguida, crie um segredo de cliente para a aplicação web registada. A amostra de código de aplicação web usa-o para provar a sua identidade quando solicita tokens.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Configure a amostra

Neste tutorial, você configura uma amostra que você pode baixar a partir de GitHub. A amostra utiliza ASP.NET para fornecer uma lista simples de tarefas. A amostra utiliza [componentes de middleware Microsoft OWIN](/aspnet/aspnet/overview/owin-and-katana/). [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o exemplo a partir do GitHub. Certifique-se de que extrai o ficheiro de exemplo numa pasta em que o comprimento de carateres total do caminho seja inferior a 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Os dois projetos seguintes constam da solução de amostra:

* **TaskWebApp** - Criar e editar uma lista de tarefas. A amostra utiliza o fluxo de utilizador **de inscrição ou de inscrição** para se inscrever e iniciar s inscrição nos utilizadores.
* **TaskService** - Suporta a funcionalidade de criar, ler, atualizar e eliminar a funcionalidade da lista de tarefas. A API está protegida pelo Azure AD B2C e é chamada pelo TaskWebApp.

Altere a amostra para utilizar a aplicação registada no seu inquilino, que inclui o ID da aplicação e a chave que registou anteriormente. Também configura os fluxos de utilizador que criou. A amostra define os valores de configuração como definições no *ficheiroWeb.config.*

Atualize as definições no ficheiro Web.config para funcionar com o fluxo do utilizador:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp,** abra o ficheiro **Web.config.**
    1. Atualize o valor `ida:Tenant` e com o nome do inquilino `ida:AadInstance` Azure AD B2C que criou. Por exemplo, `fabrikamb2c` substitua-o por `contoso` .
    1. Substitua o valor do ID do `ida:TenantId` diretório, que pode encontrar nos imóveis para o seu inquilino Azure B2C (no portal Azure sob o ID do **Diretório Azure Ative**  >  **Directory**  >  Properties).
    1. Substitua o valor do `ida:ClientId` ID da aplicação que registou.
    1. Substitua o valor de `ida:ClientSecret` pela chave que registou. Se o segredo do cliente contiver quaisquer entidades XML predefinidas, por exemplo menos do que `<` ( , superiores a ( `>` ) ampersand ( `&` ) ou citação dupla `"` (), você deve escapar desses caracteres codificando o segredo do cliente antes de adicioná-lo ao seu Web.config.
    1. Substitua o valor de `ida:SignUpSignInPolicyId` `b2c_1_signupsignin1` .
    1. Substitua o valor de `ida:EditProfilePolicyId` `b2c_1_profileediting1` .
    1. Substitua o valor de `ida:ResetPasswordPolicyId` `b2c_1_passwordreset1` .

## <a name="run-the-sample"></a>Executar o exemplo

1. No Solution Explorer, clique com o botão direito no projeto **TaskWebApp** e, em seguida, clique em **Definir como StartUp Project**.
1. Prima **F5**. É iniciado o browser predefinido para o endereço do site local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Selecione **Iniciar s seção / Iniciar s** inscrição como utilizador da aplicação. É utilizado o fluxo de utilizador **b2c_1_signupsignin1.**
1. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, **selecione Inscreva-se agora**. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a palavra-passe do utilizador e os atributos solicitados definidos no fluxo do utilizador.
1. Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de inscrição/inscrição](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Selecione **Criar** para criar uma conta local no inquilino Azure AD B2C.

O utilizador da aplicação pode agora utilizar o seu endereço de e-mail para iniciar sôm e utilizar a aplicação web.

No entanto, a funcionalidade **Lista de A-Fazer** não funcionará até completar o próximo tutorial da série, [Tutorial: Use Azure AD B2C para proteger uma API web ASP.NET](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação em Azure AD B2C
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se usando o fluxo do utilizador

Passe agora para o próximo tutorial para ativar a funcionalidade **Lista de A-Fazer** da aplicação web. Nele, regista-se uma aplicação web da API no seu próprio inquilino Azure AD B2C e, em seguida, modifica a amostra de código para usar o seu inquilino para a autenticação da API.

> [!div class="nextstepaction"]
> [Tutorial: Use o Azure Ative Directory B2C para proteger uma ASP.NET web API >](tutorial-web-api-dotnet.md)
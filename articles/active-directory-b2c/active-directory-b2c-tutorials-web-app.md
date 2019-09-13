---
title: Tutorial – habilitar a autenticação em um aplicativo Web-Azure Active Directory B2C
description: Tutorial sobre como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para uma aplicação Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 2066a7848efaf067dddde3d5db1decfc88d94436
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914205"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar a autenticação em um aplicativo Web usando Azure Active Directory B2C

Este tutorial mostra como usar Azure Active Directory (Azure AD) B2C para entrar e inscrever usuários em um aplicativo Web ASP.NET. O Azure AD B2C permite que seus aplicativos se autentiquem em contas sociais, contas corporativas e Azure Active Directory contas usando protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Crie fluxos de usuário](tutorial-create-user-flows.md) para habilitar experiências de usuário em seu aplicativo.
* Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento de ASP.net e Web** .

## <a name="update-the-application"></a>Atualizar a aplicação

No tutorial que você concluiu como parte dos pré-requisitos, você adicionou um aplicativo Web no Azure AD B2C. Para habilitar a comunicação com o exemplo neste tutorial, você precisa adicionar um URI de redirecionamento ao aplicativo em Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione o aplicativo *webapp1* .
5. Em **URL de resposta**, `https://localhost:44316`adicione.
6. Selecione **Guardar**.
7. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.
8. Selecione **chaves**, selecione **gerar chave**e selecione **salvar**. Registre a chave que você usará ao configurar o aplicativo Web.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você configura um exemplo que pode ser baixado do GitHub. O exemplo usa ASP.NET para fornecer uma lista de tarefas pendentes simples. O exemplo usa os [componentes de middleware do Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o exemplo a partir do GitHub. Certifique-se de que extrai o ficheiro de exemplo numa pasta em que o comprimento de carateres total do caminho seja inferior a 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Os dois projetos a seguir estão na solução de exemplo:

- **TaskWebApp** -criar e editar uma lista de tarefas. O exemplo usa o fluxo de usuário de inscrição **ou de entrada** para inscrever-se e conectar usuários.
- **TaskService** -dá suporte à funcionalidade criar, ler, atualizar e excluir lista de tarefas. A API é protegida por Azure AD B2C e chamada pelo TaskWebApp.

Você altera o exemplo para usar o aplicativo que está registrado em seu locatário, que inclui a ID do aplicativo e a chave que você registrou anteriormente. Você também configura os fluxos de usuário que criou. O exemplo define os valores de configuração como configurações no arquivo *Web. config* .

Atualize as configurações no arquivo Web. config para trabalhar com seu fluxo de usuário:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp** , abra o arquivo **Web. config** .
    1. Substitua o valor de `ida:Tenant` e `ida:AadInstance` pelo nome do locatário que você criou.
    1. Substitua o valor de `ida:ClientId` pela ID do aplicativo que você registrou.
    1. Substitua o valor de `ida:ClientSecret` pela chave que registou. Você deve codificar o segredo do cliente em XML antes de adicioná-lo ao Web. config.
    1. Substitua o valor de `ida:SignUpSignInPolicyId` por `b2c_1_signupsignin1`.
    1. Substitua o valor de `ida:EditProfilePolicyId` por `b2c_1_profileediting1`.
    1. Substitua o valor de `ida:ResetPasswordPolicyId` por `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Executar o exemplo

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TaskWebApp** e clique em **definir como projeto de inicialização**.
2. Pressione **F5**. É iniciado o browser predefinido para o endereço do site local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Clique em **inscrever-se/entrar** para se inscrever como um usuário do aplicativo. O fluxo de usuário **b2c_1_signupsignin1** é usado.
2. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Como você ainda não tem uma conta, selecione **inscrever-se agora**. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo do usuário.
3. Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de entrada/inscrição](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

4. Clique em **Criar** para criar uma conta local no inquilino do Azure AD B2C.

Agora, o usuário pode usar seu endereço de email para entrar e usar o aplicativo Web.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

> [!div class="nextstepaction"]
> [Tutorial: Usar Azure Active Directory B2C para proteger uma API Web ASP.NET](active-directory-b2c-tutorials-web-api.md)

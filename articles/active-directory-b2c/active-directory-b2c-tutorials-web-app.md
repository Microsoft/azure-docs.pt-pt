---
title: Tutorial - ativar a autenticação num aplicativo web - Azure Active Directory B2C | Documentos da Microsoft
description: Tutorial sobre como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para uma aplicação Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e9e497c8f10108b8e05314f35546345f7f812524
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507789"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Ativar a autenticação numa aplicação web através do Azure Active Directory B2C

Este tutorial mostra-lhe como utilizar o Azure Active Directory (Azure AD) B2C para iniciar sessão e inscrição de utilizadores numa aplicação web ASP.NET. O Azure AD B2C permite que seus aplicativos autenticar contas de redes sociais, contas empresariais e contas do Azure Active Directory através de protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação no Azure AD B2C
> * Configurar o exemplo a utilizar a aplicação
> * Inscreva-se com o fluxo de utilizador

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Criar fluxos de utilizador](tutorial-create-user-flows.md) para permitir experiências de utilizador na sua aplicação. 
- Instale [Visual Studio 2019](https://www.visualstudio.com/downloads/) com o **desenvolvimento na web e ASP.NET** carga de trabalho.

## <a name="update-the-application"></a>Atualizar a aplicação

Tutorial que concluídas como parte dos pré-requisitos, adicionou uma aplicação web no Azure AD B2C. Para ativar a comunicação com o exemplo neste tutorial, terá de adicionar um URI de redirecionamento para a aplicação no Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione a *webapp1* aplicação.
5. Sob **URL de resposta**, adicione `https://localhost:44316`.
6. Selecione **Guardar**.
7. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar a aplicação web.
8. Selecione **chaves**, selecione **gerar chave**e selecione **guardar**. Registe a chave que irá utilizar quando configurar a aplicação web.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, configurou um exemplo que pode transferir a partir do GitHub. O exemplo usa o ASP.NET para fornecer uma lista de tarefas simples. O exemplo usa [componentes de middleware do Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o exemplo a partir do GitHub. Certifique-se de que extrai o ficheiro de exemplo numa pasta em que o comprimento de carateres total do caminho seja inferior a 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

São os seguintes dois projetos na solução de exemplo:

- **TaskWebApp** - criar e editar uma lista de tarefas. Este exemplo utiliza a **inscrição ou início de sessão** fluxo de utilizador para inscrever-se ou iniciar sessão dos utilizadores.
- **TaskService** - oferece suporte a criar, ler, atualizar e eliminar a funcionalidade de lista de tarefas. A API é protegida pelo Azure AD B2C e chamada pela TaskWebApp.

Alterar o exemplo a utilizar a aplicação que está registrada no seu inquilino, que inclui o ID da aplicação e a chave que registou anteriormente. Também é configurar os fluxos de utilizador que criou. O exemplo define os valores de configuração como definições no ficheiro Web. config. Para alterar as definições:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
2. Na **TaskWebApp** projeto, abra o **Web. config** ficheiro. Substitua o valor de `ida:Tenant` pelo nome do inquilino que criou. Substitua o valor de `ida:ClientId` pelo ID da aplicação que registou. Substitua o valor de `ida:ClientSecret` pela chave que registou.
3. No ficheiro **Web.config**, substitua o valor de `ida:SignUpSignInPolicyId` por `b2c_1_signupsignin1`. Substitua o valor de `ida:EditProfilePolicyId` por `b2c_1_profileediting1`. Substitua o valor de `ida:ResetPasswordPolicyId` por `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Executar o exemplo

1. No Solution Explorer, clique com botão direito a **TaskWebApp** projeto e, em seguida, clique em **Set as StartUp Project**.
2. Prima **F5**. É iniciado o browser predefinido para o endereço do site local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Clique em **inscrever-se / iniciar sessão** para se inscrever como um utilizador da aplicação. O **b2c_1_signupsignin1** é utilizado o fluxo de utilizador.
2. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, selecione **Inscreva-se agora**. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a senha do usuário e os atributos solicitados definidos no fluxo de utilizador.
3. Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no inquilino do Azure AD B2C.

Agora o utilizador pode utilizar o respetivo endereço de e-mail para iniciar sessão e utilizar a aplicação web.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação no Azure AD B2C
> * Configurar o exemplo a utilizar a aplicação
> * Inscreva-se com o fluxo de utilizador

> [!div class="nextstepaction"]
> [Tutorial: Utilizar o Azure Active Directory B2C para proteger uma API web ASP.NET](active-directory-b2c-tutorials-web-api.md)

---
title: Tutorial-autenticar usuários em um aplicativo cliente nativo-Azure Active Directory B2C
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer logon de usuário para um aplicativo de área de trabalho .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6d1c2eff9bc9ffe8a0fb2166d39c16360c0a79cc
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538928"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Tutorial: autenticar usuários em um cliente de desktop nativo usando Azure Active Directory B2C

Este tutorial mostra como usar Azure Active Directory B2C (Azure AD B2C) para entrar e inscrever usuários em um aplicativo de área de trabalho Windows Presentation Foundation (WPF). O Azure AD B2C permite que seus aplicativos se autentiquem em contas sociais, contas corporativas e Azure Active Directory contas usando protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar o aplicativo cliente nativo
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Crie fluxos de usuário](tutorial-create-user-flows.md) para habilitar experiências de usuário em seu aplicativo.
- Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com cargas de **trabalho de desenvolvimento de desktop .net** e **ASP.net e desenvolvimento** para a Web.

## <a name="add-the-native-client-application"></a>Adicionar o aplicativo cliente nativo

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você configura um exemplo que pode ser baixado do GitHub. O aplicativo de área de trabalho WPF de exemplo demonstra a inscrição, a entrada e pode chamar uma API Web protegida no Azure AD B2C. [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [navegar no repositório](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Para atualizar o aplicativo para trabalhar com seu locatário Azure AD B2C e invocar seus fluxos de usuário em vez daqueles no locatário de demonstração padrão:

1. Abra a solução **Active-Directory-B2C-WPF** (`active-directory-b2c-wpf.sln`) no Visual Studio.
2. No projeto **Active-Directory-B2C-WPF** , abra o arquivo *app.XAML.cs* e localize as definições de variável a seguir. Substitua `{your-tenant-name}` pelo nome do locatário Azure AD B2C e `{application-ID}` pela ID do aplicativo que você registrou anteriormente.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Atualize as variáveis de nome de política com os nomes dos fluxos de usuário que você criou como parte dos pré-requisitos. Por exemplo:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Pressione **F5** para compilar e executar o exemplo.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Selecione **entrar** para se inscrever como um usuário. Isso usa o fluxo de usuário do **B2C_1_signupsignin1** .
2. Azure AD B2C apresenta uma página de entrada com um link **inscrever-se agora** . Como você ainda não tem uma conta, selecione o link **inscrever-se agora** .
3. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo do usuário.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de entrada/inscrição](media/active-directory-b2c-tutorials-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Selecione **criar** para criar uma conta local no locatário do Azure ad B2C.

O usuário agora pode usar seu endereço de email para entrar e usar o aplicativo da área de trabalho. Após uma inscrição ou entrada bem-sucedida, os detalhes do token são exibidos no painel inferior do aplicativo do WPF.

![Detalhes do token mostrados no painel inferior do aplicativo de área de trabalho do WPF](media/active-directory-b2c-tutorials-desktop-app/desktop-app-01-post-signin.png)

Se você selecionar o botão **chamar API** , uma **mensagem de erro** será exibida. Você encontra o erro porque, em seu estado atual, o aplicativo está tentando acessar uma API protegida pelo locatário de demonstração, `fabrikamb2c.onmicrosoft.com`. Como seu token de acesso é válido somente para seu locatário Azure AD B2C, a chamada à API é, portanto, não autorizada.

Continue no próximo tutorial para registrar uma API Web protegida em seu próprio locatário e habilitar a funcionalidade de **API de chamada** .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar o aplicativo cliente nativo
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

Em seguida, para habilitar a funcionalidade do botão **chamar API** , conceda ao aplicativo da área de trabalho do WPF acesso a uma API Web registrada em seu próprio locatário Azure ad B2C:

> [!div class="nextstepaction"]
> [Tutorial: conceder acesso a uma API Web node. js de um aplicativo de área de trabalho >](active-directory-b2c-tutorials-desktop-app-webapi.md)

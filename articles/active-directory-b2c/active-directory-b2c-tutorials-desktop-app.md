---
title: Tutorial - ativar a autenticação num aplicativo cliente nativo - Azure Active Directory B2C | Documentos da Microsoft
description: Tutorial sobre como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para um aplicativo de desktop do .NET.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1842859723173412df2053a242ebe9ca4cf7f32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386966"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Tutorial: Ativar a autenticação num aplicativo de cliente nativo através do Azure Active Directory B2C

Este tutorial mostra-lhe como utilizar o Azure Active Directory (Azure AD) B2C para inscrever e iniciar a sessão dos utilizadores numa aplicação de ambiente de trabalho do Windows Presentation Foundation (WPF). O Azure AD B2C permite que seus aplicativos autenticar contas de redes sociais, contas empresariais e contas do Azure Active Directory através de protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar a aplicação de cliente nativo
> * Configurar o exemplo a utilizar a aplicação
> * Inscreva-se com o fluxo de utilizador

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Criar fluxos de utilizador](tutorial-create-user-flows.md) para permitir experiências de utilizador na sua aplicação. 
- Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho de **desenvolvimento no ambiente de trabalho em .NET** e **desenvolvimento na Web e em ASP.NET**.

## <a name="add-the-native-client-application"></a>Adicionar a aplicação de cliente nativo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para a aplicação. Por exemplo, *nativeapp1*.
6. Para **incluir a aplicação web / web API**, selecione **não**.
7. Para **incluir cliente nativo**, selecione **Sim**.
8. Para **URI de redirecionamento**, introduza um URI de redirecionamento válido com um esquema personalizado. Existem duas considerações importantes ao escolher um URI de redirecionamento:

    - **Exclusivo** -o esquema do URI de redirecionamento deve ser exclusivo para cada aplicação. No exemplo `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` é o esquema. Este padrão deve ser seguido. Se duas aplicações partilharem o mesmo esquema, o utilizador recebe uma opção para escolher uma aplicação. Se o utilizador fizer uma seleção incorreta, o início de sessão irá falhar.
    - **Completa** -o URI de redirecionamento tem de ter um esquema e um caminho. O caminho tem de conter, pelo menos, uma barra depois do domínio. Por exemplo, `//contoso/` funciona e `//contoso` falhar. Certifique-se de que o URI de redirecionamento não inclui caracteres especiais, como carateres de sublinhado.

9. Clique em **Criar**.
10. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar o exemplo.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, configurou um exemplo que pode transferir a partir do GitHub. A aplicação ambiente de trabalho do WPF de exemplo demonstra a inscrição, início de sessão e chama uma API de web protegida no Azure AD B2C. [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [navegar no repositório](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Para alterar as definições de aplicação, substitua a `<your-tenant-name>` com o seu nome de inquilino e substitua`<application-ID`> com o ID da aplicação que registou.

1. Abra a solução `active-directory-b2c-wpf` no Visual Studio.
2. No projeto `active-directory-b2c-wpf`, abra o ficheiro **App.xaml.cs** e faça as seguintes atualizações:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Atualização do **PolicySignUpSignIn** variável com o nome do fluxo de utilizador que criou.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Prima **F5** para compilar e executar o exemplo.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Clique em **sessão** para se inscrever como um utilizador. Esta opção utiliza a **B2C_1_signupsignin1** fluxo de utilizador.
2. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, clique na ligação **Inscrever-se agora**. 
3. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a senha do usuário e os atributos solicitados definidos no fluxo de utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no inquilino do Azure AD B2C.

Agora, o utilizador pode utilizar o respetivo endereço de e-mail para iniciar sessão e utilizar a aplicação de ambiente de trabalho.

> [!NOTE]
> Se clicar no botão **Chamar API**, receberá o erro "Não autorizado". Recebe este erro porque está a tentar aceder a um recurso a partir do inquilino de demonstração. Como o seu token de acesso só é válido para o seu inquilino do Azure AD, a chamada à API não é autorizada. Avance para o próximo tutorial para criar uma API Web protegida para o seu inquilino.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar a aplicação de cliente nativo
> * Configurar o exemplo a utilizar a aplicação
> * Inscreva-se com o fluxo de utilizador

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a uma API da web de node. js a partir de uma aplicação de ambiente de trabalho com o Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)

---
title: Tutorial – habilitar a autenticação em um aplicativo cliente nativo-Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer logon de usuário para um aplicativo de área de trabalho .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9a3c11c7303d467a1a993c49d983131639683239
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064885"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar a autenticação em um aplicativo cliente nativo usando Azure Active Directory B2C

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

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *nativeapp1*.
6. Para **incluir aplicativo Web/API Web**, selecione **não**.
7. Para **incluir cliente nativo**, selecione **Sim**.
8. Para **URI**de redirecionamento, insira um URI de redirecionamento válido com um esquema personalizado. Há duas considerações importantes ao escolher um URI de redirecionamento:

    - **Unique** – o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário fizer uma escolha incorreta, a entrada falhará.
    - **Complete** -o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//contoso/` funciona e `//contoso` falha. Certifique-se de que o URI de redirecionamento não inclua caracteres especiais, como sublinhados.

9. Clique em **Criar**.
10. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o exemplo.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você configura um exemplo que pode ser baixado do GitHub. O aplicativo de área de trabalho do WPF de exemplo demonstra a inscrição, a entrada e a chamada de uma API Web protegida no Azure AD B2C. [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [navegar no repositório](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Para alterar as configurações do aplicativo, substitua `<your-tenant-name>` pelo nome do locatário e substitua`<application-ID`> pela ID do aplicativo que você registrou.

1. Abra a solução `active-directory-b2c-wpf` no Visual Studio.
2. No projeto `active-directory-b2c-wpf`, abra o ficheiro **App.xaml.cs** e faça as seguintes atualizações:

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Atualize a variável **PolicySignUpSignIn** com o nome do fluxo de usuário que você criou.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Pressione **F5** para compilar e executar o exemplo.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Clique em **entrar** para inscrever-se como um usuário. Isso usa o fluxo de usuário **B2C_1_signupsignin1** .
2. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, clique na ligação **Inscrever-se agora**.
3. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo do usuário.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de entrada/inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Clique em **Criar** para criar uma conta local no inquilino do Azure AD B2C.

Agora, o utilizador pode utilizar o respetivo endereço de e-mail para iniciar sessão e utilizar a aplicação de ambiente de trabalho.

> [!NOTE]
> Se clicar no botão **Chamar API**, receberá o erro "Não autorizado". Recebe este erro porque está a tentar aceder a um recurso a partir do inquilino de demonstração. Como o seu token de acesso só é válido para o seu inquilino do Azure AD, a chamada à API não é autorizada. Avance para o próximo tutorial para criar uma API Web protegida para o seu inquilino.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar o aplicativo cliente nativo
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a uma API Web node. js de um aplicativo de desktop usando Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)

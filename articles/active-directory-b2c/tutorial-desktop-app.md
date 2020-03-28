---
title: 'Tutorial: Autenticar utilizadores numa aplicação de cliente nativo'
titleSuffix: Azure AD B2C
description: Tutorial sobre como usar o Diretório Ativo Azure B2C para fornecer login ao utilizador para uma aplicação de ambiente de trabalho .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186204"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Tutorial: Autenticar utilizadores num cliente nativo de desktop usando o Diretório Ativo Azure B2C

Este tutorial mostra-lhe como utilizar o Azure Ative Directory B2C (Azure AD B2C) para iniciar sessão e inscrever os utilizadores numa aplicação de ambiente de trabalho da Windows Presentation Foundation (WPF). O Azure AD B2C permite que as suas aplicações se autentiquem a contas sociais, contas empresariais e contas de Diretório Ativo Azure utilizando protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicione a aplicação de cliente nativo
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se utilizando o fluxo do utilizador

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Crie fluxos de utilizador](tutorial-create-user-flows.md) para permitir as experiências do utilizador na sua aplicação.
- Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com o desenvolvimento do ambiente de **trabalho .NET** e ASP.NET e trabalhos de **trabalho de desenvolvimento web.**

## <a name="add-the-native-client-application"></a>Adicione a aplicação de cliente nativo

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Grave o ID de **Aplicação (cliente)** para utilização num passo posterior.

## <a name="configure-the-sample"></a>Configure a amostra

Neste tutorial, configura uma amostra que pode descarregar a partir do GitHub. A aplicação de ambiente de trabalho WPF da amostra demonstra o início, o início de sessão e pode chamar uma API web protegida em Azure AD B2C. [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [navegar no repositório](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Para atualizar a aplicação para trabalhar com o seu inquilino Azure AD B2C e invocar os seus fluxos de utilizador em vez dos do inquilino de demonstração padrão:

1. Abra a solução **active-directy-b2c-wpf** ()`active-directory-b2c-wpf.sln`no Estúdio Visual.
2. No projeto **active-directy-b2c-wpf,** abra o ficheiro *App.xaml.cs* e encontre as seguintes definições variáveis. Substitua `{your-tenant-name}` pelo nome do seu inquilino `{application-ID}` Azure AD B2C e pelo ID de inscrição que gravou anteriormente.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Atualize as variáveis de nome de política com os nomes dos fluxos de utilizador que criou como parte dos pré-requisitos. Por exemplo:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Pressione **F5** para construir e executar a amostra.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Selecione **Iniciar sessão** para se inscrever como utilizador. Isto utiliza o **fluxo de utilizador B2C_1_signupsignin1.**
2. Azure AD B2C apresenta um sinal na página com um **link De Sinal agora.** Uma vez que ainda não tem uma conta, selecione o link **'Iniciar' agora.**
3. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a palavra-passe do utilizador e os atributos solicitados definidos no fluxo do utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de inscrição/inscrição](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Selecione **Criar** para criar uma conta local no inquilino Azure AD B2C.

O utilizador pode agora utilizar o seu endereço de e-mail para iniciar sessão e utilizar a aplicação de ambiente de trabalho. Após uma inscrição ou inscrição bem sucedida, os detalhes do símbolo são apresentados no painel inferior da aplicação WPF.

![Detalhes simbólicos mostrados no painel inferior da aplicação do ambiente de trabalho wPF](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Se selecionar o botão **Call API,** é apresentada uma **mensagem de erro.** Encontra-se o erro porque, no seu estado atual, a aplicação está a `fabrikamb2c.onmicrosoft.com`tentar aceder a uma API protegida pelo inquilino da demonstração, . Uma vez que o seu token de acesso é válido apenas para o seu inquilino Azure AD B2C, a chamada da API não é, portanto, autorizada.

Continue até ao próximo tutorial para registar uma API web protegida no seu próprio inquilino e ativar a funcionalidade **Call API.**

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicione a aplicação de cliente nativo
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se utilizando o fluxo do utilizador

Em seguida, para ativar a funcionalidade do botão **Call API,** conceda à aplicação de desktop WPF acesso a uma API web registada no seu próprio inquilino Azure AD AD B2C:

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a uma API web Node.js a partir de uma aplicação de desktop >](tutorial-desktop-app-webapi.md)

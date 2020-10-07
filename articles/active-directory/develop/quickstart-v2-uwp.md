---
title: 'Quickstart: Inicie sísmia nos utilizadores e ligue para o Microsoft Graph numa aplicação Universal Windows Platform / Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, saiba como uma aplicação Universal Windows Platform (UWP) pode obter um token de acesso e chamar uma API protegida pela plataforma de identidade Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 4efccf86bb546f62cafcc652d04fdc4a9062f3c1
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812578"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Início Rápido: Chamar a Microsoft Graph API a partir de uma aplicação da Plataforma Universal do Windows (UWP)

Este quickstart contém uma amostra de código que demonstra como uma aplicação Universal Windows Platform (UWP) pode assinar em utilizadores com contas pessoais ou contas de trabalho e escola, obter um token de acesso e ligar para a Microsoft Graph API. (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> [!div renderon="docs" class="sxs-lookup"]
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Aceda ao novo [portal Azure - Painel de inscrições](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) da App.
> 1. Introduza um nome para a sua aplicação e clique em **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, siga os passos a seguir:
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue para a plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://aka.ms/MobileAppReg)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por `UWP-App-calling-MsGraph` exemplo.
>      - Na secção **Tipos de conta suportados**, selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Xbox, Outlook.com)**.
>      - Selecione **Registar** para criar a aplicação.
> 1. Na lista de páginas da aplicação, selecione **Autenticação**.
> 1. Na secção **de redirecionamento de**  |  **URIs sugeridas para clientes públicos (mobile, desktop)** verifique **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> 1. Selecione **Guardar**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para que a amostra de código para este arranque rápido funcione, é necessário adicionar um URI de redirecionamento como **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-uwp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Transfira o seu projeto do Visual Studio

> [!div renderon="docs"]
> [Descarregue o projeto Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Executar o projeto usando o Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para funcionar.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o projeto do Visual Studio
>
> 1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Abra o projeto no Visual Studio. Pode ser solicitado a instalar um UWP SDK. Nesse caso, aceite.
> 1. Edite **MainPage.Xaml.cs** e substitua os valores do `ClientId` campo:
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
> Em que:
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
>
> > [!TIP]
> > Para encontrar o valor do ID da *aplicação,* **aceda** à secção Geral do portal

#### <a name="step-4-run-your-application"></a>Passo 4: Executar a sua aplicação

Se quiser experimentar o arranque rápido da sua máquina Windows:

1. Na barra de ferramentas Visual Studio, escolha a plataforma certa (provavelmente **x64** ou **x86**, não ARM). Observará que o dispositivo-alvo muda de *Dispositivo* para *Máquina Local*
1. Selecione Debug **Começar sem depurar**

## <a name="more-information"></a>Mais informações

Esta secção apresenta mais informações sobre o início rápido.

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL[(Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client)é a biblioteca utilizada para iniciar serções nos utilizadores e solicitar fichas de segurança. Os tokens de segurança são usados para aceder a uma API protegida pela plataforma Microsoft Identity para desenvolvedores. Pode instalar a MSAL ao executar o comando seguinte na *Consola do Gestor de Pacotes* do Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, o MSAL é inicializado utilizando o seguinte código:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

> |Em que: | Descrição |
> |---------|---------|
> | `ClientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |

### <a name="requesting-tokens"></a>Pedir tokens

A MSAL tem dois métodos para adquirir fichas numa aplicação UWP: `AcquireTokenInteractive` e `AcquireTokenSilent` .

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Algumas situações exigem forçar os utilizadores a interagir com o ponto final da plataforma de identidade da Microsoft através de uma janela popup para validar as suas credenciais ou dar consentimento. Alguns exemplos incluem:

- Os utilizadores da primeira vez insinuam-se na aplicação
- Quando os utilizadores possam ter de reintroduzir as respetivas credenciais por a palavra-passe ter expirado
- Quando a sua aplicação está a solicitar acesso a um recurso, que o utilizador precisa de consentir
- Quando é necessária autenticação de dois fatores

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Em que:| Descrição |
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser solicitados, tais como `{ "user.read" }` para o Microsoft Graph ou para `{ "api://<Application ID>/access_as_user" }` APIs web personalizados. |

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

Utilize o `AcquireTokenSilent` método para obter fichas para aceder a recursos protegidos após o `AcquireTokenInteractive` método inicial. Não pretende exigir que o utilizador valide as suas credenciais sempre que precise de aceder a um recurso. A maior parte do tempo quer aquisições simbólicas e renovação sem qualquer interação com o utilizador

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Em que: | Descrição |
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser solicitados, tais como `{ "user.read" }` para o Microsoft Graph ou para `{ "api://<Application ID>/access_as_user" }` APIs web personalizados |
> | `firstAccount` | Especifica a primeira conta de utilizador na cache (o MSAL suporta vários utilizadores numa única aplicação) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial do ambiente de trabalho do Windows para obter um guia passo a passo completo sobre a criação de aplicações e novas funcionalidades, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [UWP - Chamar tutorial da Graph API](tutorial-v2-windows-uwp.md)

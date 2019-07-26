---
title: Guia de início rápido do Windows UWP da plataforma de identidade da Microsoft | Azure
description: Saiba como um aplicativo Plataforma Universal do Windows (XAML) pode obter um token de acesso e chamar uma API protegida pelo ponto de extremidade da plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b89bd3992bd6249e0acd576fee7e1556968ad65
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334178"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Início rápido: Chame a Microsoft Graph API a partir de uma aplicação da Plataforma Universal do Windows (UWP)

Este guia de início rápido contém um exemplo de código que demonstra como um aplicativo Plataforma Universal do Windows (UWP) pode conectar usuários com contas pessoais ou contas corporativas e de estudante, obter um token de acesso e chamar a API Microsoft Graph.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> [!div renderon="docs" class="sxs-lookup"]
> Tem duas opções para iniciar a aplicação de início rápido:
> * Express [Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Manual [Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código
>
> 1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) .
> 1. Introduza um nome para a sua aplicação e clique em **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, siga os passos a seguir:
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://aka.ms/MobileAppReg) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `UWP-App-calling-MsGraph`.
>      - Na secção **Tipos de conta suportados**, selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Xbox, Outlook.com)** .
>      - Selecione **Registar** para criar a aplicação.
> 1. Na lista de páginas da aplicação, selecione **Autenticação**.
> 1. Expanda a seção **Desktop + dispositivos** .  (Se os **dispositivos desktop +** não estiverem visíveis, primeiro clique na faixa superior para exibir a experiência de autenticação de visualização)
> 1. Na seção **URI** de redirecionamento, selecione **Adicionar URI**.  Digite **urn: IETF: WG: OAuth: 2.0: OOB**.
> 1. Selecione **Guardar**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para o código de exemplo deste início rápido funcionar, terá de adicionar um URI de redirecionamento como **urn: ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-uwp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Baixe seu projeto do Visual Studio

 - [Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar seu projeto do Visual Studio

1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
1. Abra o projeto no Visual Studio. Você pode ser solicitado a instalar um SDK do UWP. Nesse caso, aceite.
1. Edite **MainPage.XAML.cs** e substitua os valores do `ClientId` campo:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.    

> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
>
> > [!TIP]
> > Para localizar o valor da *ID do aplicativo*, vá para a seção **visão geral** no portal

#### <a name="step-4-run-your-application"></a>Passo 4: Executar a aplicação

Se você quiser experimentar o início rápido em seu computador Windows:

1. Na barra de ferramentas do Visual Studio, escolha a plataforma correta (provavelmente **x64** ou **x86**, não ARM).
   > Observe que o dispositivo de destino muda de *dispositivo* para *computador local*
1. Selecionar depuração | **Iniciar sem depuração**

## <a name="more-information"></a>Mais informações

Esta secção apresenta mais informações sobre o início rápido.

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca usada para conectar usuários e solicitar tokens de segurança. Os tokens de segurança são usados para acessar uma API protegida pela plataforma de identidade da Microsoft para desenvolvedores. Pode instalar a MSAL ao executar o comando seguinte na *Consola do Gestor de Pacotes* do Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, MSAL é inicializado usando o seguinte código:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = new PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Em que: ||
> |---------|---------|
> | `ClientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |

### <a name="requesting-tokens"></a>Pedir tokens

O MSAL tem dois métodos para adquirir tokens em um aplicativo UWP `AcquireTokenInteractive` : `AcquireTokenSilent`e.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Algumas situações exigem forçar os usuários a interagir com o ponto de extremidade da plataforma Microsoft Identity por meio de uma janela pop-up para validar suas credenciais ou dar consentimento. Alguns exemplos incluem:

- A primeira vez que os usuários entram no aplicativo
- Quando os utilizadores possam ter de reintroduzir as respetivas credenciais por a palavra-passe ter expirado
- Quando seu aplicativo está solicitando acesso a um recurso, o usuário precisa consentir
- Quando é precisa a autenticação de dois fatores

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Em que:||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser pedidos (como o `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as API Web personalizadas). |

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

Use o `AcquireTokenSilent` método para obter tokens para acessar recursos protegidos após o `AcquireTokenInteractive` método inicial. Você não deseja exigir que o usuário valide suas credenciais toda vez que precisar acessar um recurso. Na maior parte do tempo que você deseja que as aquisições e a renovação de tokens sem qualquer interação do usuário

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser pedidos (como o `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as API Web personalizadas) |
> | `firstAccount` | Especifica a primeira conta de usuário no cache (o MSAL dá suporte a vários usuários em um único aplicativo) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Experimente o tutorial do ambiente de trabalho do Windows para obter um guia passo a passo completo sobre a criação de aplicações e novas funcionalidades, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [UWP - Chamar tutorial da Graph API](tutorial-v2-windows-uwp.md)

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
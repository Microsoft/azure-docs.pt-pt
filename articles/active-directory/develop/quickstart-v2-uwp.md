---
title: Guia de introdução do Microsoft identity plataforma UWP do Windows | Azure
description: Saiba como um aplicativo da plataforma de Windows Universal (XAML) pode obter um token de acesso e chamar uma API protegida pelo ponto final de plataforma de identidade Microsoft.
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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c61da6a55b1f4502deee056b29fdbc22ef33514
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490623"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Início rápido: Chame a Microsoft Graph API a partir de uma aplicação da Plataforma Universal do Windows (UWP)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como uma aplicação da Plataforma Universal do Windows (UWP) pode iniciar sessão dos utilizadores com contas pessoais ou contas profissionais e escolares, obter um token de acesso e chamar a Microsoft Graph API.

![Mostra como funciona a aplicação de exemplo gerada por este início rápido](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> [!div renderon="docs" class="sxs-lookup"]
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá para a nova [portal do Azure – registos de aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) painel.
> 1. Introduza um nome para a sua aplicação e clique em **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, siga os passos a seguir:
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
> 1. Selecione **novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `UWP-App-calling-MsGraph`.
>      - Na secção **Tipos de conta suportados**, selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Xbox, Outlook.com)**.
>      - Selecione **Registar** para criar a aplicação.
> 1. Na lista de páginas da aplicação, selecione **Autenticação**.
> 1. Na secção **URLs de Redirecionamento**, localize a secção **URIs de Redirecionamento Sugeridos para clientes públicos (móvel, ambiente de trabalho)** e selecione **"urn:ietf:wg:oauth:2.0:oob**.
> 1. Selecione **Guardar**.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para o código de exemplo deste início rápido funcionar, terá de adicionar um URI de redirecionamento como **urn: ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Efetuar esta alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-uwp/green-check.png) seu aplicativo está configurado com esses atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Transfira o seu projeto do Visual Studio

 - [Transfira o projeto do Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o seu projeto do Visual Studio

1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
1. Abra o projeto no Visual Studio. Poderá ser-lhe pedido para instalar um SDK UWP. Nesse caso, aceite.
1. Editar **MainPage.Xaml.cs** e substitua os valores do `ClientId` campo:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```

> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here` -é o Id da aplicação que registou.
>
> > [!TIP]
> > Para localizar os valores da *ID da aplicação*, aceda ao **descrição geral** página

#### <a name="step-4-run-your-application"></a>Passo 4: Executar a aplicação

Se quiser experimentar o guia de introdução no seu computador Windows:

1. Na barra de ferramentas da Visual Studio, escolha a plataforma à direita (provavelmente **x64** ou **x86**, não ARM).
   > Observe que o dispositivo de destino é alterado de *dispositivo* para *computador Local*
1. Selecione Debug | **Iniciar sem depuração**

## <a name="more-information"></a>Mais informações

Esta secção apresenta mais informações sobre o início rápido.

### <a name="msalnet"></a>MSAL.NET

A MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca utilizada para iniciar sessão dos utilizadores e solicitar tokens de segurança. Os tokens de segurança são utilizados para aceder a uma API protegida pela plataforma do Microsoft Identity para desenvolvedores. Pode instalar a MSAL ao executar o comando seguinte na *Consola do Gestor de Pacotes* do Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, inicialize a MSAL com o código seguinte:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = new PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Em que: ||
> |---------|---------|
> | `ClientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |

### <a name="requesting-tokens"></a>Pedir tokens

A MSAL tem dois métodos usados para aquisição de tokens interativamente: `AcquireTokenInteractive` e `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Algumas situações exijam a forçar utilizadores interagirem com o ponto de extremidade da plataforma de identidade Microsoft por meio de uma janela de pop-up para optar por validar as credenciais ou para dar consentimento. Alguns exemplos incluem:

- Os utilizadores de iniciantes iniciam sessão na aplicação
- Quando os utilizadores possam ter de reintroduzir as respetivas credenciais por a palavra-passe ter expirado
- Quando está a solicitar acesso a um recurso, o que o utilizador tem de dar consentimento a sua aplicação
- Quando é precisa a autenticação de dois fatores

```csharp
authResult = await App.PublicClientApp.AcquireToken(scopes, this)
                       .ExecuteAsync();
```

> |Em que:||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser pedidos (como o `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as API Web personalizadas). |
> | `this`| Representa a janela do WPF que será utilizada para o Centro de caixa de diálogo início de sessão

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

Não vai querer exigir que o utilizador valide as suas credenciais sempre que tiver de aceder a um recurso. A maioria das vezes, vai querer que as aquisições e renovação de tokens sejam feitas sem qualquer interação do utilizador. Pode utilizar o método `AcquireTokenSilent` para obter tokens para aceder a recursos protegidos após o método `AcquireTokenAsync` inicial:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser pedidos (como o `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as API Web personalizadas) |
> | `firstAccount` | Especifica a primeira conta de utilizador na cache (MSAL oferece suporte a vários usuários numa única aplicação) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Experimente o tutorial do ambiente de trabalho do Windows para obter um guia passo a passo completo sobre a criação de aplicações e novas funcionalidades, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [UWP - tutorial de chamada Graph API](tutorial-v2-windows-uwp.md)

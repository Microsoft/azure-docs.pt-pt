---
title: 'Quickstart: Inicie síprofone nos utilizadores e ligue para o Microsoft Graph numa aplicação de desktop da Plataforma Universal Windows ! Rio Azure'
description: Neste quickstart, saiba como uma aplicação windows desktop .NET (XAML) pode obter um token de acesso e chamar uma API protegida por um ponto final da plataforma de identidade da Microsoft
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0ffc3b40ecff1a7935eeef7fabfcb332fcd9dc71
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017294"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Início rápido: Adquirir um token e chamar a Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows

Neste arranque rápido, descarrega e executa uma amostra de código que demonstra como uma aplicação do Windows desktop .NET (WPF) pode iniciar sôm nos utilizadores e obter um token de acesso para ligar para a API do Gráfico microsoft. 

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho [de desenvolvimento da Plataforma Universal Windows](/windows/uwp/get-started/get-set-up) instalada
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Aceda ao novo [portal Azure - Inscrições de aplicações.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `Win-App-calling-MsGraph` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Na secção **Tipos de conta suportados**, selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Xbox, Outlook.com)**.
> 1. Selecione **Registar** para criar a aplicação.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. **Selecione Adicionar uma plataforma** de  >  **aplicações móveis e desktop**.
> 1. Na secção **Redirecionar URIs,** selecione `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
> Para que a amostra de código para este arranque rápido funcione, é necessário adicionar um URL de resposta como **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-windows-desktop/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Transfira o seu projeto do Visual Studio

> [!div renderon="docs"]
> [Descarregue o projeto Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Executar o projeto usando o Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para funcionar.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o projeto do Visual Studio
> 1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Abra o projeto no Visual Studio.
> 1. Edite **App.Xaml.cs** e substitua os valores dos campos `ClientId` e `Tenant` com o código seguinte:
>
>    ```csharp
>    private static string ClientId = "Enter_the_Application_Id_here";
>    private static string Tenant = "Enter_the_Tenant_Info_Here";
>    ```
>
> Onde:
> - `Enter_the_Application_Id_here` - é o **ID da Aplicação (cliente)** que registou.
> - `Enter_the_Tenant_Info_Here` - está definido para uma das seguintes opções:
>   - Se a sua aplicação suportar **Contas neste diretório organizacional**, substitua este valor pelo **Id do Inquilino** ou pelo **Nome do inquilino** (por exemplo, contoso.microsoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **Contas em quaisquer contas da Microsoft de diretório organizacional e pessoais**, substitua este valor por `common`
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

### <a name="msalnet"></a>MSAL.NET
MSAL ([Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client)é a biblioteca utilizada para assinar nos utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Pode instalar a MSAL ao executar o comando seguinte na **Consola do Gestor de Pacotes** do Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, inicialize a MSAL com o código seguinte:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Onde: | Descrição |
> |---------|---------|
> | `ClientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |

### <a name="requesting-tokens"></a>Pedir tokens

A MSAL tem dois métodos para comprar tokens: `AcquireTokenInteractive` e `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Algumas situações exigem forçar os utilizadores a interagir com o ponto final da plataforma de identidade da Microsoft através de uma janela popup para validar as suas credenciais ou dar consentimento. Alguns exemplos incluem:

- A primeira vez que os utilizadores iniciam sessão na aplicação
- Quando os utilizadores possam ter de reintroduzir as respetivas credenciais por a palavra-passe ter expirado
- Quando a aplicação está a pedir acesso a um recurso para o qual o utilizador tem de dar consentimento
- Quando é necessária autenticação de dois fatores

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Onde:| Descrição |
> |---------|---------|
> | `_scopes` | Contém os âmbitos que estão a ser solicitados, tais como `{ "user.read" }` para o Microsoft Graph ou para `{ "api://<Application ID>/access_as_user" }` APIs web personalizados. |

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

Não vai querer exigir que o utilizador valide as suas credenciais sempre que tiver de aceder a um recurso. A maioria das vezes, vai querer que as aquisições e renovação de tokens sejam feitas sem qualquer interação do utilizador. Pode utilizar o método `AcquireTokenSilent` para obter tokens para aceder a recursos protegidos após o método `AcquireTokenInteractive` inicial:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Onde: | Descrição |
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser solicitados, tais como `{ "user.read" }` para o Microsoft Graph ou para `{ "api://<Application ID>/access_as_user" }` APIs web personalizados. |
> | `firstAccount` | Especifica o primeiro utilizador na cache (a MSAL suporta vários utilizadores numa única aplicação). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximos passos

Experimente o tutorial do ambiente de trabalho do Windows para obter um guia passo a passo completo sobre a criação de aplicações e novas funcionalidades, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [Chamar tutorial da Graph API](./tutorial-v2-windows-desktop.md)

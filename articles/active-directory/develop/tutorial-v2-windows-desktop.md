---
title: 'Tutorial: Criar uma app da Fundação de Apresentação do Windows (WPF) que utiliza a plataforma de identidade da Microsoft para autenticação Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação WPF que utiliza a plataforma de identidade da Microsoft para iniciar sísmis nos utilizadores e obter um token de acesso para ligar para a Microsoft Graph API em seu nome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0caac72125b28a9c6b17eb68413a7242df5cd06c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011650"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Tutorial: Ligue para a API do Gráfico microsoft a partir de uma aplicação para desktop do Windows

Neste tutorial, você constrói uma aplicação nativa do Windows Desktop .NET (XAML) que assina nos utilizadores e obtém um token de acesso para ligar para a API do Microsoft Graph. 

Quando tiver concluído o guia, a sua aplicação poderá ligar para uma API protegida que utilize contas pessoais (incluindo outlook.com, live.com e outros). A aplicação também utilizará contas de trabalho e escola de qualquer empresa ou organização que utilize o Azure Ative Directory.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto *da Fundação de Apresentação do Windows (WPF)* no Estúdio Visual
> * Instale a Biblioteca de Autenticação do Microsoft (MSAL) para .NET
> * Registe a inscrição no portal Azure
> * Adicione código para suportar a inscrição do utilizador e a s inscrição
> * Adicione código para ligar para a Microsoft Graph API
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de amostras gerada por este guia

![Mostra como funciona a app de amostras gerada por este tutorial](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

A aplicação de amostra que cria com este guia permite uma aplicação do Windows Desktop que questiona a API do Microsoft Graph ou uma API web que aceita tokens a partir de um ponto final da plataforma de identidade da Microsoft. Para este cenário, adicione um token aos pedidos HTTP através do cabeçalho de Autorização. A Microsoft Authentication Library (MSAL) lida com a aquisição e renovação de fichas.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Manipulação da aquisição de token para aceder a APIs web protegidas

Após a autenticação do utilizador, a aplicação da amostra recebe um token que pode utilizar para consultar a Microsoft Graph API ou uma API web que é protegida pela plataforma de identidade da Microsoft para desenvolvedores.

APIs como o Microsoft Graph requerem um símbolo para permitir o acesso a recursos específicos. Por exemplo, é necessário um token para ler o perfil de um utilizador, aceder ao calendário de um utilizador ou enviar e-mail. A sua aplicação pode solicitar um token de acesso utilizando o MSAL para aceder a estes recursos especificando os âmbitos de API. Este token de acesso é então adicionado ao cabeçalho de autorização HTTP para cada chamada feita contra o recurso protegido.

A MSAL gere fichas de acesso de caching e refrescantes para si, para que a sua aplicação não precise.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft (MSAL.NET)|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Nesta secção cria-se um novo projeto para demonstrar como integrar uma aplicação Do Windows Desktop .NET (XAML) com *o Sign-In com* a Microsoft para que a aplicação possa consultar APIs web que requerem um token.

A aplicação que cria com este guia exibe um botão que é usado para chamar um gráfico, uma área para mostrar os resultados no ecrã e um botão de inscrição.

> [!NOTE]
> Prefere descarregar o projeto visual studio desta amostra? [Faça o download de um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)e salte para o passo de [Configuração](#register-your-application) para configurar a amostra de código antes de executá-la.
>

Para criar a sua aplicação, faça o seguinte:

1. No Estúdio Visual, selecione **File**  >  **New**  >  **Project**.
2. Em **Modelos**, selecione **Visual C#**.
3. Selecione **A App WPF (.NET Framework)**, dependendo da versão da versão do Visual Studio que está a utilizar.

## <a name="add-msal-to-your-project"></a>Adicione MSAL ao seu projeto

1. No Estúdio Visual, selecione **Tools**  >  **NuGet Package Manager** Package Manager >  **Consola**.
2. Na janela da consola do gestor de pacotes, cole o seguinte comando Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Este comando instala a Microsoft Authentication Library. MSAL lida com a aquisição, caching e tokens refrescantes do utilizador que são usados para aceder às APIs que são protegidas pelo Azure Ative Directory v2.0
    >

## <a name="register-your-application"></a>Registar a aplicação

Pode registar a sua candidatura de duas formas.

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Pode registar rapidamente a sua candidatura fazendo o seguinte:
1. Aceda ao [portal Azure - Registo de Candidaturas.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)
1. Introduza um nome para a sua aplicação e xelecione **Registar**.
1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, faça o seguinte:
1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Introduza um **Nome** para a sua aplicação, por `Win-App-calling-MsGraph` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Na secção **de tipos de conta suportada,** selecione Contas em qualquer **diretório organizacional (qualquer diretório AD AZure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**.
1. Selecione **Registar**.
1. Em **Gestão**, **selecione Autenticação**  >  **Adicione uma plataforma**.
1. Selecione **aplicações móveis e desktop**.
1. Na secção **Redirecionar URIs,** selecione **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Selecione **Configurar**.
1. Vá ao Visual Studio, abra o ficheiro *App.xaml.cs* e, em seguida, substitua `Enter_the_Application_Id_here` no código snippet abaixo pelo ID da aplicação que acabou de registar e copiar.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Adicione o código para inicializar o MSAL

Neste passo, cria-se uma classe para lidar com a interação com a MSAL, como o manuseamento de fichas.

1. Abra o ficheiro *App.xaml.cs* e, em seguida, adicione a referência para MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Atualize a classe de aplicativos para o seguinte:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Criar a UI da aplicação

Esta secção mostra como uma aplicação pode consultar um servidor de back-end protegido, como o Microsoft Graph.

Um ficheiro *MainWindow.xaml* deve ser automaticamente criado como parte do seu modelo de projeto. Abra este ficheiro e, em seguida, substitua o nó da sua aplicação *\<Grid>* pelo seguinte código:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Use o MSAL para obter um símbolo para a Microsoft Graph API

Nesta secção, você usa o MSAL para obter um símbolo para a Microsoft Graph API.

1. No ficheiro *MainWindow.xaml.cs,* adicione a referência para MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Substitua o `MainWindow` código de classe pelo seguinte:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>Mais informações

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Chamar o `AcquireTokenInteractive` método resulta numa janela que leva os utilizadores a iniciar sôs- insusição. As aplicações geralmente exigem que os utilizadores assinem interativamente a primeira vez que precisam de aceder a um recurso protegido. Também podem ter de iniciar seduções quando uma operação silenciosa para adquirir um símbolo falha (por exemplo, quando a palavra-passe de um utilizador é expirada).

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `AcquireTokenSilent` método lida com aquisições e renovações simbólicas sem qualquer interação do utilizador. Depois `AcquireTokenInteractive` de ser executado pela primeira vez, é o método habitual de `AcquireTokenSilent` utilização para obter fichas que acedam a recursos protegidos para chamadas subsequentes, porque as chamadas para solicitar ou renovar fichas são feitas silenciosamente.

Eventualmente, o `AcquireTokenSilent` método falhará. As razões para falhar podem ser que o utilizador tenha assinado ou alterado a sua palavra-passe noutro dispositivo. Quando a MSAL deteta que a questão pode ser resolvida exigindo uma ação interativa, dispara uma `MsalUiRequiredException` exceção. A sua aplicação pode lidar com esta exceção de duas formas:

* Pode fazer uma ligação `AcquireTokenInteractive` imediatamente. Esta chamada resulta em levar o utilizador a iniciar sinsumento. Este padrão é normalmente utilizado em aplicações online onde não existe conteúdo offline disponível para o utilizador. A amostra gerada por esta configuração guiada segue este padrão, que pode ver em ação na primeira vez que executa a amostra.

* Como nenhum utilizador utilizou a aplicação, `PublicClientApp.Users.FirstOrDefault()` contém um valor nulo, e `MsalUiRequiredException` uma exceção é lançada.

* O código da amostra trata então da exceção ligando , o `AcquireTokenInteractive` que leva o utilizador a fazer o seu sôm.

* Em vez disso, pode apresentar uma indicação visual aos utilizadores de que é necessário um sinal interativo, para que possam selecionar o momento certo para iniciar sedu. Ou a aplicação pode voltar a tentar `AcquireTokenSilent` mais tarde. Este padrão é frequentemente utilizado quando os utilizadores podem utilizar outra funcionalidade da aplicação sem interrupções -- por exemplo, quando o conteúdo offline está disponível na aplicação. Neste caso, os utilizadores podem decidir quando pretendem iniciar seducação para aceder ao recurso protegido ou atualizar as informações desatualizadas. Em alternativa, a aplicação pode decidir voltar a tentar `AcquireTokenSilent` quando a rede for restaurada depois de ter estado temporariamente indisponível.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Ligue para a Microsoft Graph API usando o token que acabou de obter

Adicione o seguinte novo método ao seu `MainWindow.xaml.cs` . O método é utilizado para fazer um `GET` pedido contra a API do gráfico utilizando um cabeçalho autorizado:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre fazer uma chamada REST contra uma API protegida

Nesta aplicação de amostra, utiliza o `GetHttpContentWithToken` método para fazer um pedido HTTP contra um recurso protegido que requer um `GET` token e, em seguida, devolver o conteúdo ao autor da chamada. Este método adiciona o token adquirido no cabeçalho de autorização HTTP. Para esta amostra, o recurso é o ponto final do Microsoft Graph API *me,* que exibe as informações de perfil do utilizador.

## <a name="add-a-method-to-sign-out-a-user"></a>Adicione um método para assinar um utilizador

Para assinar um utilizador, adicione o seguinte método ao seu `MainWindow.xaml.cs` ficheiro:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>Mais informações sobre a assinatura do utilizador

O `SignOutButton_Click` método remove os utilizadores da cache do utilizador MSAL, que efetivamente diz à MSAL para esquecer o utilizador atual para que um pedido futuro de aquisição de um token só seja bem sucedido se for feito para ser interativo.

Embora a aplicação nesta amostra suporte a utilizadores individuais, o MSAL suporta cenários onde várias contas podem ser assinadas ao mesmo tempo. Um exemplo é uma aplicação de e-mail onde um utilizador tem várias contas.

## <a name="display-basic-token-information"></a>Mostrar informações básicas de token

Para apresentar informações básicas sobre o token, adicione o seguinte método ao seu ficheiro *MainWindow.xaml.cs:*

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>Mais informações

Além do token de acesso que é usado para chamar a API do Gráfico microsoft, depois de o utilizador entrar, o MSAL também obtém um token de ID. Este símbolo contém um pequeno subconjunto de informação que é pertinente para os utilizadores. O `DisplayBasicTokenInfo` método exibe a informação básica que está contida no símbolo. Por exemplo, apresenta o nome de visualização e iD do utilizador, bem como a data de validade do token e a cadeia que representa o próprio símbolo de acesso. Pode selecionar o botão API do *Gráfico de Chamada Microsoft várias* vezes e ver se o mesmo token foi reutilizado para pedidos posteriores. Também pode ver a data de validade ser prorrogada quando a MSAL decidir que é hora de renovar o token.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a construção de aplicativos de desktop que chamem APIs web protegidos na nossa série de cenários multi-partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo de desktop que chama APIs web](scenario-desktop-overview.md)

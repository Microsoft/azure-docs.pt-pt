---
title: Introdução à plataforma de identidade do Microsoft Windows Desktop | Microsoft Docs
description: Como um aplicativo .NET da área de trabalho do Windows (XAML) pode obter um token de acesso e chamar uma API protegida pela plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1864b4867b8e1367a2397c5ed2ed9a77001dadb4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701200"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chamar a API de Microsoft Graph de um aplicativo de área de trabalho do Windows

Este guia demonstra como um aplicativo nativo do .NET (XAML) do Windows Desktop usa um token de acesso para chamar a API do Microsoft Graph. O aplicativo também pode acessar outras APIs que exigem tokens de acesso de uma plataforma de identidade da Microsoft para o ponto de extremidade do Developers v 2.0. Essa plataforma era denominada Azure AD anteriormente.

Quando você tiver concluído o guia, seu aplicativo poderá chamar uma API protegida que usa contas pessoais (incluindo outlook.com, live.com e outros). O aplicativo também usará contas corporativas e de estudante de qualquer empresa ou organização que usa Azure Active Directory.  

> [!NOTE]
> O guia requer o Visual Studio 2015 atualização 3, o Visual Studio 2017 ou o Visual Studio 2019. Não tem nenhuma dessas versões? [Baixe o Visual Studio 2019 gratuitamente.](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

O aplicativo de exemplo que você cria com este guia permite que um aplicativo da área de trabalho do Windows consulte a API do Microsoft Graph ou uma API Web que aceite tokens de um ponto de extremidade da plataforma de identidade da Microsoft. Para esse cenário, você adiciona um token às solicitações HTTP por meio do cabeçalho Authorization. A MSAL (biblioteca de autenticação da Microsoft) manipula a aquisição e a renovação de tokens.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Lidando com a aquisição de token para acessar APIs Web protegidas

Depois que o usuário é autenticado, o aplicativo de exemplo recebe um token que você pode usar para consultar Microsoft Graph API ou uma API Web protegida pela plataforma de identidade da Microsoft para desenvolvedores.

APIs como Microsoft Graph exigem um token para permitir o acesso a recursos específicos. Por exemplo, um token é necessário para ler o perfil de um usuário, acessar o calendário de um usuário ou enviar email. Seu aplicativo pode solicitar um token de acesso usando MSAL para acessar esses recursos especificando escopos de API. Esse token de acesso é então adicionado ao cabeçalho de autorização HTTP para cada chamada feita no recurso protegido.

O MSAL gerencia o cache e a atualização de tokens de acesso para você, para que seu aplicativo não precise.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|MSAL.NET (biblioteca de autenticação da Microsoft)|

## <a name="set-up-your-project"></a>Configurar seu projeto

Nesta seção, você cria um novo projeto para demonstrar como integrar um aplicativo .NET da área de trabalho do Windows (XAML) com a *entrada com a Microsoft* para que o aplicativo possa consultar APIs Web que exigem um token.

O aplicativo que você cria com este guia exibe um botão que é usado para chamar um grafo, uma área para mostrar os resultados na tela e um botão de saída.

> [!NOTE]
> Prefere baixar o projeto do Estúdio Visual desta amostra? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)e pule para a [etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.
>

Para criar seu aplicativo, faça o seguinte:

1. No Visual Studio, selecione **arquivo** > **novo** **projeto**de > .
2. Em **modelos**, selecione **Visual C#** .
3. Selecione **aplicativo WPF (.NET Framework)** , dependendo da versão da versão do Visual Studio que você está usando.

## <a name="add-msal-to-your-project"></a>Adicione MSAL ao seu projeto

1. No Visual Studio, selecione **Ferramentas** > **Gestor de Pacotes NuGet**> **Consola do Gestor de Pacotes**.
2. Na janela do console do Gerenciador de pacotes, Cole o seguinte comando de Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Esse comando instala a biblioteca de autenticação da Microsoft. O MSAL lida com a aquisição, o armazenamento em cache e a atualização de tokens de usuário usados para acessar as APIs protegidas pelo Azure Active Directory v 2.0
    >

## <a name="register-your-application"></a>Registar a sua aplicação

Você pode registrar seu aplicativo de uma das duas maneiras.

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Você pode registrar rapidamente seu aplicativo fazendo o seguinte:
1. Vá para o [registro de aplicativo portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Introduza um nome para a sua aplicação e xelecione **Registar**.
1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, faça o seguinte:
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `Win-App-calling-MsGraph`.
   - Na secção **Tipos de conta suportados**, selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Xbox, Outlook.com)** .
   - Selecione **Registar** para criar a aplicação.
1. Na lista de páginas da aplicação, selecione **Autenticação**.
   1. Na seção **URIs de redirecionamento** , na lista URIs de redirecionamento:
   1. Na coluna **tipo** , selecione **cliente público/nativo (Mobile & Desktop)** .
   1. Na coluna **URI de redirecionamento** , insira `https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Selecione **Registar**.
1. Vá ao Visual Studio, abra o ficheiro *App.xaml.cs* e, em seguida, substitua `Enter_the_Application_Id_here` no código abaixo com o ID da aplicação que acabou de registar e copiar.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Adicione o código para inicializar o MSAL

Nesta etapa, você cria uma classe para lidar com a interação com o MSAL, como a manipulação de tokens.

1. Abra o arquivo *app.XAML.cs* e, em seguida, adicione a referência para MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Atualize a classe de aplicativo para o seguinte:

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

## <a name="create-the-application-ui"></a>Criar a interface do usuário do aplicativo

Esta seção mostra como um aplicativo pode consultar um servidor back-end protegido, como Microsoft Graph. 

Um arquivo *MainWindow. XAML* deve ser criado automaticamente como parte do seu modelo de projeto. Abra esse arquivo e substitua a *grade de\<* do seu aplicativo > nó pelo seguinte código:

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

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Use MSAL para obter um token para a API de Microsoft Graph

Nesta seção, você usará MSAL para obter um token para a API de Microsoft Graph.

1. No arquivo *MainWindow.XAML.cs* , adicione a referência para MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Substitua o código da classe `MainWindow` pelo seguinte:

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
    ```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Chamar o método `AcquireTokenInteractive` resulta em uma janela que solicita que os usuários entrem. Os aplicativos geralmente exigem que os usuários entrem interativamente na primeira vez que precisam acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar (por exemplo, quando a senha de um usuário estiver expirada).

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O método `AcquireTokenSilent` manipula aquisições e renovações de token sem nenhuma interação do usuário. Depois que `AcquireTokenInteractive` é executado pela primeira vez, `AcquireTokenSilent` é o método usual a ser usado para obter tokens que acessam recursos protegidos para chamadas subsequentes, pois as chamadas para solicitar ou renovar tokens são feitas silenciosamente.

Eventualmente, o método `AcquireTokenSilent` falhará. Os motivos para a falha podem ser que o usuário tenha desconectado ou alterado sua senha em outro dispositivo. Quando o MSAL detecta que o problema pode ser resolvido exigindo uma ação interativa, ele dispara uma exceção de `MsalUiRequiredException`. Seu aplicativo pode manipular essa exceção de duas maneiras:

* Ele pode fazer uma chamada contra `AcquireTokenInteractive` imediatamente. Essa chamada resulta na solicitação do usuário para entrar. Esse padrão geralmente é usado em aplicativos online em que não há nenhum conteúdo offline disponível para o usuário. O exemplo gerado por essa instalação interativa segue esse padrão, que você pode ver em ação na primeira vez que executar o exemplo. 

* Como nenhum usuário usou o aplicativo, `PublicClientApp.Users.FirstOrDefault()` contém um valor nulo e uma exceção de `MsalUiRequiredException` é gerada. 

* O código no exemplo manipula a exceção chamando `AcquireTokenInteractive`, o que resulta na solicitação do usuário para entrar.

* Em vez disso, ele pode apresentar uma indicação visual para os usuários de que uma entrada interativa é necessária, para que eles possam selecionar o horário certo para entrar. Ou o aplicativo pode tentar novamente `AcquireTokenSilent` mais tarde. Esse padrão é usado frequentemente quando os usuários podem usar outras funcionalidades do aplicativo sem interrupções, por exemplo, quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam entrar para acessar o recurso protegido ou atualizar as informações desatualizadas. Como alternativa, o aplicativo pode decidir tentar novamente `AcquireTokenSilent` quando a rede for restaurada depois de ter sido temporariamente indisponível.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar a API de Microsoft Graph usando o token que você acabou de obter

Adicione o novo método a seguir ao seu `MainWindow.xaml.cs`. O método é usado para fazer uma solicitação de `GET` em relação a API do Graph usando um cabeçalho de autorização:

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

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste aplicativo de exemplo, você usa o método `GetHttpContentWithToken` para fazer uma solicitação HTTP `GET` em um recurso protegido que requer um token e, em seguida, retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no cabeçalho de autorização HTTP. Para este exemplo, o recurso é o Microsoft Graph ponto de extremidade do API *me* , que exibe as informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Adicionar um método para desconectar um usuário

Para sair de um usuário, adicione o seguinte método ao seu arquivo de `MainWindow.xaml.cs`:

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

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Mais informações sobre a saída do usuário

O método `SignOutButton_Click` remove os utilizadores da cache de utilizador MSAL, que efetivamente diz à MSAL para esquecer o utilizador atual, de modo a que um pedido futuro de aquisição de um símbolo só tenha sucesso se for feito para ser interativo.

Embora o aplicativo neste exemplo dê suporte a usuários únicos, o MSAL dá suporte a cenários em que várias contas podem ser conectadas ao mesmo tempo. Um exemplo é um aplicativo de email em que um usuário tem várias contas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Exibir informações básicas de token

Para exibir informações básicas sobre o token, adicione o seguinte método ao seu arquivo *MainWindow.XAML.cs* :

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

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

Além do token de acesso usado para chamar a API de Microsoft Graph, depois que o usuário entra, o MSAL também obtém um token de ID. Esse token contém um pequeno subconjunto de informações pertinentes aos usuários. O método `DisplayBasicTokenInfo` exibe as informações básicas contidas no token. Por exemplo, ele exibe o nome de exibição e a ID do usuário, bem como a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Você pode selecionar o botão *chamar Microsoft Graph API* várias vezes e ver que o mesmo token foi reutilizado para solicitações subsequentes. Você também pode ver a data de expiração que está sendo estendida quando MSAL decide que é hora de renovar o token.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

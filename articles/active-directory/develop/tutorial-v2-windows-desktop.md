---
title: Começar com a plataforma de identidade microsoft Windows desktop [ desktop] Microsoft Docs
description: Como uma aplicação Windows Desktop .NET (XAML) pode obter um sinal de acesso e chamar um API protegido pela plataforma de identidade da Microsoft.
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
ms.openlocfilehash: 422251da25ae0ef911eb723bb0342a84fe99559c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129919"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Ligue para a Microsoft Graph API a partir de uma aplicação do Windows Desktop

Este guia demonstra como uma aplicação nativa do Windows Desktop .NET (XAML) utiliza um sinal de acesso para ligar para o Microsoft Graph API. A aplicação também pode aceder a outras APIs que requerem fichas de acesso de uma plataforma de identidade da Microsoft para desenvolvedores v2.0 endpoint. Esta plataforma foi anteriormente chamada Azure AD.

Quando tiver concluído o guia, a sua aplicação poderá chamar uma API protegida que utiliza contas pessoais (incluindo outlook.com, live.com e outras). A aplicação também utilizará contas de trabalho e escola de qualquer empresa ou organização que utilize o Diretório Ativo Azure.  

> [!NOTE]
> O guia requer visual Studio 2015 Update 3, Visual Studio 2017 ou Visual Studio 2019. Não tem nenhuma destas versões? [Baixe o Visual Studio 2019 gratuitamente.](https://www.visualstudio.com/downloads/)

>[!NOTE]
> Se for novidade na plataforma de identidade da Microsoft, recomendamos que comece com o Adquirir um símbolo e ligue para a [Microsoft Graph API a partir de uma aplicação de desktop windows](quickstart-v2-windows-desktop.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de amostragerada por este guia

![Mostra como funciona a aplicação de amostragerada por este tutorial](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

A aplicação de amostra que cria com este guia permite uma aplicação do Windows Desktop que questiona o Microsoft Graph API ou um Web API que aceita tokens de um ponto final da plataforma de identidade da Microsoft. Para este cenário, adicione um token aos pedidos http através do cabeçalho de Autorização. A Microsoft Authentication Library (MSAL) trata da aquisição e renovação simbólicas.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Manipulação da aquisição de fichas para aceder a APIs web protegidos

Depois de o utilizador ser autenticado, a aplicação de amostra recebe um símbolo que pode utilizar para consultar a Microsoft Graph API ou uma API Web que é protegida pela plataforma de identidade da Microsoft para desenvolvedores.

APIs como o Microsoft Graph requerem um sinal para permitir o acesso a recursos específicos. Por exemplo, é necessário um símbolo para ler o perfil de um utilizador, aceder ao calendário de um utilizador ou enviar e-mail. A sua aplicação pode solicitar um sinal de acesso utilizando o MSAL para aceder a estes recursos, especificando os âmbitos da API. Este token de acesso é adicionado ao cabeçalho de autorização http para cada chamada feita contra o recurso protegido.

A MSAL gere fichas de acesso refrescantes para si, para que a sua aplicação não precise.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft (MSAL.NET)|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Nesta secção cria-se um novo projeto para demonstrar como integrar uma aplicação Windows Desktop .NET (XAML) com o *Sign-In com* a Microsoft para que a aplicação possa consultar APIs web que requeiram um símbolo.

A aplicação que cria com este guia exibe um botão que é usado para chamar um gráfico, uma área para mostrar os resultados no ecrã e um botão de sinalização.

> [!NOTE]
> Prefere baixar o projeto do Estúdio Visual desta amostra? [Descarregue um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)e salte para a [etapa de Configuração](#register-your-application) para configurar a amostra de código antes de executá-la.
>

Para criar a sua aplicação, faça o seguinte:

1. No Estúdio Visual, selecione **File** > **New** > **Project**.
2. Em **Modelos,** selecione **C#Visual**.
3. Selecione **WPF App (.NET Framework)** , dependendo da versão do Estúdio Visual que está a usar.

## <a name="add-msal-to-your-project"></a>Adicione MSAL ao seu projeto

1. No Visual Studio, selecione **Ferramentas** > **Gestor de Pacotes NuGet**> **Consola do Gestor de Pacotes**.
2. Na janela de consola de gestor de pacotes, cola o seguinte comando Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Este comando instala a Biblioteca de Autenticação da Microsoft. MSAL pega na aquisição, cache e refrescantes tokens do utilizador que são usados para aceder às APIs que são protegidas pelo Azure Ative Directory v2.0
    >

## <a name="register-your-application"></a>Registar a sua aplicação

Pode registar a sua candidatura de duas formas.

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Pode registar rapidamente a sua candidatura fazendo o seguinte:
1. Vá ao [portal Azure - Registo de Candidaturas.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)
1. Introduza um nome para a sua aplicação e xelecione **Registar**.
1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, faça o seguinte:
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecione **Novo registo**.
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `Win-App-calling-MsGraph`.
   - Na secção **Tipos de conta suportados**, selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Xbox, Outlook.com)** .
   - Selecione **Registar** para criar a aplicação.
1. Na lista de páginas da aplicação, selecione **Autenticação**.
   1. Na secção **REdirecionamento de URIs,** na lista de URIs redirecionados:
   1. Na coluna **TYPE** selecione **Cliente público/nativo (mobile & desktop)** .
   1. Na coluna **REDIRECT URI,** introduza `https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Selecione **Registar**.
1. Vá ao Visual Studio, abra o ficheiro *App.xaml.cs* e, em seguida, substitua `Enter_the_Application_Id_here` no código abaixo com o ID da aplicação que acabou de registar e copiar.

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

2. Atualize a classe de aplicações para o seguinte:

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

## <a name="create-the-application-ui"></a>Criar a aplicação UI

Esta secção mostra como uma aplicação pode consultar um servidor back-end protegido, como o Microsoft Graph. 

Um ficheiro *MainWindow.xaml* deve ser criado automaticamente como parte do seu modelo de projeto. Abra este ficheiro e substitua a *\<Grid&gt* da sua aplicação; o nó pelo seguinte código:

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

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Use mSAL para obter um símbolo para a Microsoft Graph API

Nesta secção, utiliza-se o MSAL para obter um símbolo para a Microsoft Graph API.

1. No ficheiro *MainWindow.xaml.cs,* adicione a referência para mSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Substitua o código de `MainWindow` de classe pelo seguinte:

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

Chamar o método `AcquireTokenInteractive` resulta numa janela que leva os utilizadores a iniciarem o seu sessão. As aplicações geralmente exigem que os utilizadores assinem interativamente na primeira vez que precisam de aceder a um recurso protegido. Também podem ter de iniciar sessão quando uma operação silenciosa para adquirir um símbolo falha (por exemplo, quando a palavra-passe de um utilizador é expirada).

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O método `AcquireTokenSilent` lida com aquisições e renovações simbólicas sem qualquer interação do utilizador. Após `AcquireTokenInteractive` é executado pela primeira vez, `AcquireTokenSilent` é o método habitual de usar para obter fichas que acedem a recursos protegidos para chamadas subsequentes, porque as chamadas para solicitar ou renovar fichas são feitas em silêncio.

Eventualmente, o método `AcquireTokenSilent` falhará. As razões para a falha podem ser que o utilizador tenha assinado ou alterado a sua palavra-passe noutro dispositivo. Quando a MSAL deteta que a questão pode ser resolvida exigindo uma ação interativa, dispara uma exceção `MsalUiRequiredException`. A sua aplicação pode lidar com esta exceção de duas formas:

* Pode fazer uma chamada contra `AcquireTokenInteractive` imediatamente. Esta chamada resulta em levar o utilizador a iniciar sessão. Este padrão é normalmente utilizado em aplicações online onde não existe conteúdo offline disponível para o utilizador. A amostra gerada por esta configuração guiada segue este padrão, que pode ver em ação na primeira vez que executa a amostra. 

* Como nenhum utilizador utilizou a aplicação, `PublicClientApp.Users.FirstOrDefault()` contém um valor nulo, e uma `MsalUiRequiredException` exceção é lançada. 

* O código da amostra trata então da exceção, ligando para `AcquireTokenInteractive`, o que leva o utilizador a iniciar o seu insessão.

* Em vez disso, pode apresentar uma indicação visual aos utilizadores de que é necessário um sinal de inscrição interativo, para que possam selecionar o momento certo para iniciar o seu sessão. Ou a aplicação pode voltar a tentar `AcquireTokenSilent` mais tarde. Este padrão é frequentemente utilizado quando os utilizadores podem utilizar outrafuncionalidade de aplicação sem interrupções -- por exemplo, quando o conteúdo offline está disponível na aplicação. Neste caso, os utilizadores podem decidir quando pretendem iniciar sessão para aceder ao recurso protegido ou atualizar as informações desatualizadas. Em alternativa, a aplicação pode decidir voltar a tentar `AcquireTokenSilent` quando a rede for restaurada depois de ter estado temporariamente indisponível.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Ligue para a Microsoft Graph API usando o símbolo que acabou de obter

Adicione o seguinte novo método ao seu `MainWindow.xaml.cs`. O método é utilizado para fazer um pedido de `GET` contra a API do gráfico utilizando um cabeçalho autorizado:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre fazer uma chamada rest contra uma API protegida

Nesta aplicação de amostra, utiliza o método `GetHttpContentWithToken` para fazer um pedido de `GET` HTTP contra um recurso protegido que requer um símbolo e, em seguida, devolver o conteúdo ao chamador. Este método adiciona o token adquirido no cabeçalho de autorização http. Para esta amostra, o recurso é o ponto *final* do Microsoft Graph API me, que exibe as informações de perfil do utilizador.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Adicione um método para assinar um utilizador

Para assinar um utilizador, adicione o seguinte método ao seu ficheiro `MainWindow.xaml.cs`:

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
### <a name="more-information-about-user-sign-out"></a>Mais informações sobre a inscrição do utilizador

O método `SignOutButton_Click` remove os utilizadores da cache de utilizador MSAL, que efetivamente diz à MSAL para esquecer o utilizador atual, de modo a que um pedido futuro de aquisição de um símbolo só tenha sucesso se for feito para ser interativo.

Embora a aplicação nesta amostra suporte utilizadores únicos, a MSAL suporta cenários em que várias contas podem ser assinadas ao mesmo tempo. Um exemplo é uma aplicação de e-mail onde um utilizador tem várias contas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Mostrar informações básicas de fichas

Para apresentar informações básicas sobre o símbolo, adicione o seguinte método ao seu ficheiro *MainWindow.xaml.cs:*

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

Além do sinal de acesso que é usado para chamar a API do Microsoft Graph, depois de o utilizador entrar, o MSAL também obtém um token de identificação. Este símbolo contém um pequeno subconjunto de informação que é pertinente para os utilizadores. O método `DisplayBasicTokenInfo` mostra a informação básica contida no símbolo. Por exemplo, exibe o nome e o ID do utilizador, bem como a data de validade do símbolo e a cadeia que representa o próprio símbolo de acesso. Pode selecionar o botão *Call Microsoft Graph API* várias vezes e ver se o mesmo token foi reutilizado para pedidos posteriores. Também pode ver que a data de validade será prorrogada quando a MSAL decidir que é hora de renovar o símbolo.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

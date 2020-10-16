---
title: 'Tutorial: Criar uma aplicação Universal Windows Platform (UWP) que utiliza a plataforma de identidade da Microsoft para autenticação Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação UWP que utiliza a plataforma de identidade da Microsoft para assinar nos utilizadores e obter um token de acesso para ligar para a Microsoft Graph API em seu nome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: cbfb5c598a2a56b0b14a3a90cf29ce23366b9b6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627674"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Tutorial: Ligue para a Microsoft Graph API a partir de uma aplicação Universal Windows Platform (UWP)

Este guia explica como uma aplicação nativa da Universal Windows Platform (UWP) pode solicitar um token de acesso. A aplicação chama então a API do Gráfico microsoft. O guia aplica-se também a outras APIs que requerem acesso a tokens a partir do ponto final da plataforma de identidade da Microsoft.

No final deste guia, a sua aplicação chama uma API protegida utilizando contas pessoais. Exemplos são outlook.com, live.com, e outros. A sua aplicação também chama contas de trabalho e escola de qualquer empresa ou organização que tenha diretório Azure Ative (Azure AD).

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto *Universal Windows Platform (UWP)* em Estúdio Visual
> * Registe a inscrição no portal Azure
> * Adicione código para suportar a inscrição do utilizador e a s inscrição
> * Adicione código para ligar para a Microsoft Graph API
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho [de desenvolvimento da Plataforma Universal Windows](/windows/uwp/get-started/get-set-up) instalada

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como funciona a app de amostras gerada por este tutorial](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Este guia cria uma aplicação UWP de amostra que consulta a API do Gráfico microsoft. Para este cenário, é adicionado um token aos pedidos HTTP utilizando o cabeçalho de Autorização. A Microsoft Authentication Library lida com aquisições e renovações simbólicas.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza o seguinte pacote NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft|
|[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)|Biblioteca do cliente do Microsoft Graph|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta secção fornece instruções passo a passo para integrar uma aplicação Do Windows Desktop .NET (XAML) com o início de sposição com a Microsoft. Em seguida, a aplicação pode consultar APIs web que requerem um token, como a Microsoft Graph API.

Este guia cria uma aplicação que exibe um botão que consulta a API do Microsoft Graph e um botão para assinar. Também exibe caixas de texto que contêm os resultados das chamadas.

> [!NOTE]
> Deseja descarregar o projeto visual studio desta amostra em vez de o criar? [Faça o download de um projeto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)e salte para o passo de registo da [aplicação](#register-your-application "passo de registo de pedidos") para configurar a amostra de código antes de ser executada.

### <a name="create-your-application"></a>Crie a sua aplicação

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**
1. In **Create a new project**, escolha Blank App **(Universal Windows)** para C# e selecione **Next**.
1. Em **Configurar o seu novo projeto,** nomeie a app e selecione **Create.**
1. Se solicitado, no **Novo Projeto universal da plataforma windows,** selecione qualquer versão para **versões Target** e **Minimum** e selecione **OK**.

   ![Versões mínimas e alvo](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Adicione a Biblioteca de Autenticação do Microsoft ao seu projeto

1. No Estúdio Visual, selecione **Tools**  >  **NuGet Package Manager**Package Manager  >  **Consola**.
1. Copiar e colar os seguintes comandos na janela **da consola Package Manager:**

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > O primeiro comando instala a [Microsoft Authentication Library (MSAL.NET)](https://aka.ms/msal-net). MSAL.NET adquire, caches e atualiza fichas de utilizador que acedem a APIs que estão protegidas pela plataforma de identidade da Microsoft. O segundo comando instala [a Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) para autenticar pedidos no Microsoft Graph e fazer chamadas para o serviço.

### <a name="create-your-applications-ui"></a>Crie uI da sua aplicação

O Visual Studio cria *o MainPage.xaml* como parte do seu modelo de projeto. Abra este ficheiro e, em seguida, substitua o nó **grid** da sua aplicação pelo seguinte código:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Utilize a Microsoft Authentication Library para obter um token para a API do Gráfico microsoft

Esta secção mostra como usar a Microsoft Authentication Library para obter um token para a API do Gráfico microsoft. Faça alterações no ficheiro *MainPage.xaml.cs.*

1. Em *MainPage.xaml.cs*, adicione as seguintes referências:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Substitua a sua `MainPage` classe pelo seguinte código:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente<a name="more-information"></a>

O `AcquireTokenInteractive` método resulta numa janela que leva os utilizadores a iniciar sôs. As aplicações geralmente exigem que os utilizadores assinem interativamente a primeira vez para aceder a um recurso protegido. Também podem ter de assinar quando uma operação silenciosa para adquirir um símbolo falhar. Um exemplo é quando a palavra-passe de um utilizador expirou.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `AcquireTokenSilent` método lida com aquisições e renovações simbólicas sem qualquer interação do utilizador. Depois de `AcquireTokenInteractive` correr pela primeira vez e solicitar ao utilizador credenciais, utilize o método para solicitar `AcquireTokenSilent` fichas para chamadas posteriores. Este método adquire fichas silenciosamente. A Microsoft Authentication Library trata da cache e da renovação do símbolo.

Eventualmente, o `AcquireTokenSilent` método falha. As razões para a falha incluem um utilizador que assinou ou alterou a sua palavra-passe noutro dispositivo. Quando a Microsoft Authentication Library deteta que o problema requer uma ação interativa, abre uma `MsalUiRequiredException` exceção. A sua aplicação pode lidar com esta exceção de duas formas:

* A sua candidatura liga `AcquireTokenInteractive` imediatamente. Esta chamada resulta em levar o utilizador a iniciar sinsumento. Normalmente, utilize esta abordagem para aplicações online onde não há conteúdo offline disponível para o utilizador. A amostra gerada por esta configuração guiada segue o padrão. Vê-lo em ação da primeira vez que faz a amostra.

   Como nenhum utilizador utilizou a aplicação, `accounts.FirstOrDefault()` contém um valor nulo e lança uma `MsalUiRequiredException` exceção.

   O código da amostra trata então da exceção ligando `AcquireTokenInteractive` . Esta chamada resulta em levar o utilizador a iniciar sinsumento.

* A sua aplicação apresenta uma indicação visual aos utilizadores de que precisam de iniciar sôm. Então podem selecionar o momento certo para se inscreverem. O pedido pode voltar a tentar `AcquireTokenSilent` mais tarde. Utilize esta abordagem quando os utilizadores podem utilizar outras funcionalidades da aplicação sem interrupções. Um exemplo é quando o conteúdo offline está disponível na aplicação. Neste caso, os utilizadores podem decidir quando querem entrar. A aplicação pode voltar a tentar `AcquireTokenSilent` depois de a rede estar temporariamente indisponível.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>Instantaneamente o Cliente de Serviço de Gráficos da Microsoft obtendo o token a partir do método SignInUserAndGetTokenUsingMSAL

Adicione o seguinte novo método à *MainPage.xaml.cs:*

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre a realização de uma chamada REST contra uma API protegida

Nesta aplicação de amostra, o `GetGraphServiceClient` método instantaneamente `GraphServiceClient` utilizando um token de acesso. Depois, `GraphServiceClient` é usado para obter a informação de perfil do utilizador do ponto final do **me.**

### <a name="add-a-method-to-sign-out-the-user"></a>Adicione um método para assinar o utilizador

Para assinar o utilizador, adicione o seguinte método à *MainPage.xaml.cs:*

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

> [!NOTE]
> MSAL.NET usa métodos assíncronos para adquirir fichas ou manipular contas. Tens de apoiar as ações de UI na linha de UI. Esta é a razão da `Dispatcher.RunAsync` chamada e as precauções para `ConfigureAwait(false)` ligar.

#### <a name="more-information-about-signing-out"></a>Mais informações sobre a assinatura<a name="more-information-on-sign-out"></a>

O `SignOutButton_Click` método remove o utilizador da cache do utilizador da Microsoft Authentication Library. Este método diz eficazmente à Microsoft Authentication Library para esquecer o utilizador atual. Um pedido futuro para adquirir um símbolo só tem sucesso se for interativo.

A aplicação nesta amostra suporta um único utilizador. A Microsoft Authentication Library suporta cenários em que o utilizador pode iniciar sposição em mais de uma conta. Um exemplo é uma aplicação de e-mail onde um utilizador tem várias contas.

### <a name="display-basic-token-information"></a>Mostrar informações básicas de token

Adicione o seguinte método para *MainPage.xaml.cs* para apresentar informações básicas sobre o token:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>Mais informações<a name="more-information-1"></a>

Os tokens de ID adquiridos através do **OpenID Connect** também contêm um pequeno subconjunto de informação pertinente ao utilizador. `DisplayBasicTokenInfo` exibe informações básicas contidas no token. Esta informação inclui o nome de exibição do utilizador e o ID. Também inclui a data de validade do token e a cadeia que representa o próprio símbolo de acesso. Se selecionar várias vezes o botão **API do Gráfico de Chamada Microsoft,** verá que o mesmo token foi reutilizado para pedidos posteriores. Também pode ver a data de validade estendida quando a Microsoft Authentication Library decidir que é hora de renovar o token.

### <a name="display-message"></a>Mensagem de exibição

Adicione o seguinte novo método à *MainPage.xaml.cs:*

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Registar a aplicação

Agora tem de registar a sua candidatura:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione as inscrições da **App Azure Ative**  >  **Directory**.
1. Selecione **Novo registo**. Introduza um nome significativo da aplicação que será apresentado aos utilizadores da aplicação, por exemplo, *UWP-App-call-MSGraph*.
1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**. Em seguida, **selecione Registar-se** para continuar.
1. Na página geral, encontre o valor **de ID da Aplicação (cliente)** e copie-o. Volte ao Visual Studio, abra *MainPage.xaml.cs,* e substitua o valor `ClientId` deste valor.

Configure a autenticação para a sua aplicação:

1. De volta ao [portal Azure](https://portal.azure.com), em **Manage**, selecione **Autenticação**.
1. Na secção **de redirecionamento de**  |  **URIs sugeridas para clientes públicos (mobile, desktop)** verifique https://login.microsoftonline.com/common/oauth2/nativeclient .
1. Selecione **Guardar**.

Configure permissões API para a sua aplicação:

1. Em **Gestão**, selecione **permissões API**.
1. **Selecione Adicionar uma permissão**e, em seguida, certifique-se de que selecionou **APIs da Microsoft**.
1. Selecione **Microsoft Graph**.
1. Selecione **permissões delegadas**, procure por *User.Read*, e verifique se **o Utilizador.Read** está selecionado.
1. Se escolho alguma alteração, **selecione Adicionar permissões** para as guardar.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Permitir a autenticação integrada nos domínios federados (opcional)

Para ativar a autenticação integrada do Windows quando é utilizado com um domínio AD Azure federado, o manifesto de aplicação deve permitir capacidades adicionais. Volte para a sua aplicação no Estúdio Visual.

1. *Open Package.appxmanifest*.
1. Selecione **Capacidades**e ative as seguintes definições:

   * **Autenticação da Empresa**
   * **Redes Privadas (Servidor & cliente)**
   * **Certificados de Utilizador Partilhados**

> [!IMPORTANT]
> [A autenticação integrada](https://aka.ms/msal-net-iwa) do Windows não está configurada por padrão para esta amostra. As aplicações que `Enterprise Authentication` solicitam ou `Shared User Certificates` têm capacidades requerem um nível mais elevado de verificação pela Windows Store. Além disso, nem todos os desenvolvedores querem realizar o nível mais elevado de verificação. Ativar esta definição apenas se necessitar de autenticação Integrada do Windows com um domínio AD Azure federado.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>Abordagem alternativa à utilização do WithDefaultRedirectURI()

Na amostra atual, o `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` método é utilizado. Para `WithDefaultRedirectURI()` utilizar, complete estes passos:

1. Em *MainPage.XAML.cs,* `WithRedirectUri` substitua `WithDefaultRedirectUri` por:

   **Código atual**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Código atualizado**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Encontre o URI de retorno de chamada para a sua aplicação adicionando o `redirectURI` campo em *MainPage.xaml.cs* e definindo um ponto de rutura nele:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Executar a aplicação e, em seguida, copiar o valor de quando o ponto de `redirectUri` rutura é atingido. O valor deve ser semelhante ao seguinte valor: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    Em seguida, pode remover a linha de código porque é necessário apenas uma vez, para obter o valor.

3. No portal de registo de aplicações, adicione o valor devolvido em **RedirectUri** no painel **de autenticação.**

## <a name="test-your-code"></a>Teste o seu código

Para testar a sua aplicação, selecione a chave **F5** para executar o seu projeto no Visual Studio. A sua janela principal aparece:

![Interface de utilizador da aplicação](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Quando estiver pronto para testar, selecione **Ligue para a Microsoft Graph API**. Em seguida, utilize uma conta organizacional AD AZure ou uma conta Microsoft, como live.com ou outlook.com, para iniciar scontabilidade. A primeira vez que um utilizador executa este teste, a aplicação apresenta uma janela a pedir ao utilizador para iniciar sposição.

### <a name="consent"></a>Consent (Consentimento)

Na primeira vez que iniciar sôs a sua aplicação, um ecrã de consentimento aparece semelhante à seguinte imagem. Selecione **Sim** para explicitamente consentir o acesso:

![Ecrã de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Resultados esperados

Vê as informações do perfil do utilizador devolvidas pela chamada da Microsoft Graph API no ecrã de Resultados da **Chamada API:**

![Ecrã de resultados de chamadas da API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Você também vê informações básicas sobre o símbolo adquirido via `AcquireTokenInteractive` ou na caixa de Info `AcquireTokenSilent` **Token:**

|Propriedade  |Formato  |Descrição |
|---------|---------|---------|
|`Username` |`user@domain.com` |O nome de utilizador que identifica o utilizador.|
|`Token Expires` |`DateTime` |O tempo em que o símbolo expira. A Microsoft Authentication Library prolonga a data de validade renovando o token conforme necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

A API do Microsoft Graph requer o `user.read` âmbito de leitura do perfil de um utilizador. Este âmbito é adicionado por padrão em todas as aplicações registadas no Portal de Registo de Candidaturas. Outras APIs para o Microsoft Graph e APIs personalizados para o seu servidor de back-end podem necessitar de âmbitos adicionais. Por exemplo, a API do Gráfico microsoft requer o `Calendars.Read` âmbito para listar os calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione a `Calendars.Read` permissão delegada à informação de registo de pedidos. Em seguida, adicione o `Calendars.Read` âmbito à `acquireTokenSilent` chamada.

> [!NOTE]
> Os utilizadores podem ser solicitados a obter consentimentos adicionais à medida que aumentam o número de âmbitos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1

Recebe uma das seguintes mensagens de erro quando faz sedudas na sua aplicação num domínio AD federado:

* "Nenhum certificado de cliente válido encontrado no pedido."
* "Não há certificados válidos encontrados na loja de certificados do utilizador."
* "Tente de novo escolher um método de autenticação diferente."

**Causa:** As capacidades de empresarial e certificado não estão ativadas.

**Solução:** Siga os passos em [Permitir a autenticação integrada nos domínios federados (opcional)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Permite a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tenta utilizar o Windows Hello num computador Windows 10 para iniciar sação num ambiente com autenticação multifactor configurada. A lista de certificados aparece. Se optar por utilizar o pin, a janela PIN nunca aparece.

**Causa:** Este problema é uma limitação conhecida do corretor de autenticação web em aplicações UWP que funcionam nos desktops do Windows 10. Funciona bem no Windows 10 Mobile.

**Solução alternativa:** Selecione **Iniciar sção com outras opções**. Em seguida, **selecione Iniciar sômata com um nome de utilizador e senha**. **Selecione Forneça a sua palavra-passe**. Em seguida, passe pelo processo de autenticação do telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a utilização da Biblioteca de Autenticação do Microsoft (MSAL) para autorização e autenticação em aplicações .NET:

> [!div class="nextstepaction"]
> [Visão geral da Biblioteca de Autenticação da Microsoft (MSAL)](msal-overview.md)

---
title: Plataforma de identidade da Microsoft UWP a começar Azure
description: Como as aplicações da Plataforma Universal Windows (UWP) podem chamar uma API que requer fichas de acesso pelo ponto final da plataforma de identidade da Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0dc70aa67a1414c08ec70e2e034f4ab12b194c0a
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "81535949"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Ligue para a Microsoft Graph API a partir de uma aplicação universal da Plataforma Windows (XAML)

> [!div renderon="docs"]

Este guia explica como uma aplicação nativa da Universal Windows Platform (UWP) pode solicitar um sinal de acesso. A aplicação chama então a Microsoft Graph API. O guia também se aplica a outras APIs que requerem fichas de acesso do ponto final da plataforma de identidade da Microsoft.

No final deste guia, a sua aplicação chama uma API protegida utilizando contas pessoais. Exemplos são outlook.com, live.com e outros. A sua aplicação também chama contas de trabalho e escola de qualquer empresa ou organização que tenha O Diretório Ativo Azure (Azure AD).

>[!NOTE]
> Este guia requer o Visual Studio com o desenvolvimento da Plataforma Universal Windows instalado. Consulte [a configuração](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para obter instruções para descarregar e configurar o Visual Studio para desenvolver aplicações da Plataforma Universal windows.

>[!NOTE]
> Se for novidade na plataforma de identidade da Microsoft, recomendamos que comece com o [Call the Microsoft Graph API a partir de uma aplicação Universal Windows Platform (UWP).](quickstart-v2-uwp.md)

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como funciona a aplicação de amostragerada por este tutorial](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Este guia cria uma aplicação UWP de amostra que consulta a Microsoft Graph API. Para este cenário, é adicionado um símbolo aos pedidos http, utilizando o cabeçalho de Autorização. A Microsoft Authentication Library (MSAL) trata de aquisições e renovações simbólicas.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza o seguinte pacote NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta secção fornece instruções passo a passo para integrar uma aplicação Windows Desktop .NET (XAML) com o Sign-In com a Microsoft. Em seguida, a aplicação pode consultar APIs web que requerem um token, como a Microsoft Graph API.

Este guia cria uma aplicação que exibe um botão que consulta a API do Gráfico e um botão para assinar. Também exibe caixas de texto que contêm os resultados das chamadas.

> [!NOTE]
> Deseja descarregar o projeto do Estúdio Visual desta amostra em vez de criá-lo? [Faça o download](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) de um projeto e salte para a etapa de registo da [aplicação](#register-your-application "passo de registo de candidaturas") para configurar a amostra de código antes de ser executado.

### <a name="create-your-application"></a>Crie a sua aplicação

1. Open Visual Studio e selecione **Criar um novo projeto.**
1. Em **Criar um novo projeto,** escolha a App Em Branco **(Universal Windows)** para C# e selecione **Next**.
1. Em **Configurar o seu novo projeto,** nomeie a app e selecione **Criar**.
1. Se solicitado, no **New Universal Windows Platform Project,** selecione qualquer versão para versões **Target** e **Mínima** e selecione **OK**.

   ![Versões Mínima e Target](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Adicione a Biblioteca de Autenticação da Microsoft ao seu projeto

1. No Estúdio Visual, selecione **Tools** > **NuGet Package Manager** > **Manager Console**.
1. Copiar e colar o seguinte comando na janela da consola do Gestor de **Pacotes:**

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Este comando instala a Biblioteca de Autenticação da [Microsoft.](https://aka.ms/msal-net) A MSAL adquire, caches e atualiza tokens de utilizador que acedem a APIs protegidos pela plataforma de identidade da Microsoft.

### <a name="create-your-applications-ui"></a>Crie o UI da sua aplicação

O Visual Studio cria *o MainPage.xaml* como parte do seu modelo de projeto. Abra este ficheiro e, em seguida, substitua o nó **de Grelha** da sua aplicação pelo seguinte código:

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Use mSAL para obter um símbolo para microsoft Graph API

Esta secção mostra como usar o MSAL para obter um símbolo para a Microsoft Graph API. Faça alterações no ficheiro *MainPage.xaml.cs.*

1. Em *MainPage.xaml.cs,* adicione as seguintes referências:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Substitua `MainPage` a sua classe pelo seguinte código:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        public IPublicClientApplication PublicClientApp { get; }

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

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
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente<a name="more-information"></a>

O `AcquireTokenInteractive` método resulta numa janela que leva os utilizadores a iniciarem o seu sessão. As aplicações geralmente exigem que os utilizadores assinem interativamente a primeira vez para aceder a um recurso protegido. Também podem precisar de assinar quando uma operação silenciosa para adquirir um símbolo falha. Um exemplo é quando a palavra-passe de um utilizador expirou.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `AcquireTokenSilent` método lida com aquisições e renovações simbólicas sem qualquer interação do utilizador. Depois `AcquireTokenInteractive` de correr pela primeira vez e incita `AcquireTokenSilent` o utilizador a obter credenciais, use o método para solicitar fichas para chamadas posteriores. Este método adquire fichas silenciosamente. MSAL lida com cache e renovação simbólicas.

Eventualmente, `AcquireTokenSilent` o método falha. As razões para a falha incluem um utilizador que assinou ou alterou a sua palavra-passe noutro dispositivo. Quando a MSAL deteta que o problema requer `MsalUiRequiredException` uma ação interativa, abre uma exceção. A sua aplicação pode lidar com esta exceção de duas formas:

* A sua `AcquireTokenInteractive` candidatura liga imediatamente. Esta chamada resulta em levar o utilizador a iniciar sessão. Normalmente, utilize esta abordagem para aplicações online onde não exista conteúdo offline disponível para o utilizador. A amostra gerada por esta configuração guiada segue o padrão. Vê-se em ação a primeira vez que executa a amostra.

   Como nenhum utilizador utilizou `accounts.FirstOrDefault()` a aplicação, contém um `MsalUiRequiredException` valor nulo e lança uma exceção.

   O código da amostra trata então `AcquireTokenInteractive`da exceção, ligando . Esta chamada resulta em levar o utilizador a iniciar sessão.

* A sua aplicação apresenta uma indicação visual aos utilizadores de que precisam de iniciar sessão. Depois podem selecionar o momento certo para iniciar sessão. A aplicação `AcquireTokenSilent` pode voltar a tentar mais tarde. Utilize esta abordagem quando os utilizadores podem utilizar outrafuncionalidade de aplicação sem interrupções. Um exemplo é quando o conteúdo offline está disponível na aplicação. Neste caso, os utilizadores podem decidir quando querem iniciar sessão. A aplicação `AcquireTokenSilent` pode voltar a ser tentada depois de a rede estar temporariamente indisponível.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Ligue para a Microsoft Graph API usando o símbolo que acabou de obter

Adicione o seguinte novo método para *MainPage.xaml.cs:*

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
           // Add the token in Authorization header
           request.Headers.Authorization =
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

 Este método `GET` faz um pedido da `Authorization` API do gráfico utilizando um cabeçalho.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre a realização de uma chamada rest contra uma API protegida

Nesta aplicação de `GetHttpContentWithToken` amostra, `GET` o método faz um pedido http contra um recurso protegido que requer um símbolo. Em seguida, o método devolve o conteúdo ao chamador. Este método adiciona o token adquirido no cabeçalho de **autorização http.** Para esta amostra, o recurso é o ponto **final** do Microsoft Graph API me, que exibe as informações de perfil do utilizador.

### <a name="add-a-method-to-sign-out-the-user"></a>Adicione um método para assinar o utilizador

Para assinar o utilizador, adicione o seguinte método para *MainPage.xaml.cs:*

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
> MSAL.NET usa métodos assíncronos para adquirir fichas ou manipular contas. Tens de apoiar as ações da UI no fio da UI. Esta é a `Dispatcher.RunAsync` razão da chamada `ConfigureAwait(false)`e das precauções para chamar.

#### <a name="more-information-about-signing-out"></a>Mais informações sobre a assinatura<a name="more-information-on-sign-out"></a>

O `SignOutButton_Click` método remove o utilizador da cache do utilizador MSAL. Este método diz eficazmente à MSAL para esquecer o utilizador atual. Um futuro pedido para adquirir um símbolo só tem sucesso se for interativo.

A aplicação nesta amostra suporta um único utilizador. A MSAL suporta cenários em que o utilizador pode iniciar sessão em mais de uma conta. Um exemplo é uma aplicação de e-mail onde um utilizador tem várias contas.

### <a name="display-basic-token-information"></a>Mostrar informações básicas de fichas

Adicione o seguinte método a *MainPage.xaml.cs* para mostrar informações básicas sobre o símbolo:

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

As fichas de identificação adquiridas através da utilização do **OpenID Connect** também contêm um pequeno subconjunto de informações pertinentes ao utilizador. `DisplayBasicTokenInfo`exibe informações básicas contidas no símbolo. Estas informações incluem o nome de exibição do utilizador e o ID. Também inclui a data de validade do símbolo e a corda que representa o próprio símbolo de acesso. Se selecionar várias vezes o botão **Call Microsoft Graph API,** verá que o mesmo token foi reutilizado para pedidos posteriores. Também pode ver a data de validade prolongada quando a MSAL decidir que é hora de renovar o símbolo.

### <a name="display-message"></a>Mensagem de exibição

Adicione o seguinte novo método para *MainPage.xaml.cs:*

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

## <a name="register-your-application"></a>Registar a sua aplicação

Agora precisa de registar o seu pedido:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione registos de > **aplicações**de **diretório ativo Azure**.
1. Selecione **Novo registo**. Introduza um nome de aplicação significativo que será apresentado aos utilizadores da app, por exemplo *UWP-App-call-MSGraph*.
1. Sob os tipos de **conta suportados**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox)** e, em seguida, selecione **Register** para continuar.
1. Na página geral, encontre o valor de **ID da Aplicação (cliente)** e copie-o. Volte para o Visual Studio, abra *MainPage.xaml.cs,* e substitua o valor deste `ClientId` valor.

Configure a autenticação para a sua aplicação:

1. De volta ao [portal Azure,](https://portal.azure.com)sob **gestão,** selecione **Autenticação**.
1. Na secção **Redirecionamento** | **de URIs sugerida para clientes públicos (mobile, desktop),** verifique **https://login.microsoftonline.com/common/oauth2/nativeclient**.
1. Selecione **Guardar**.

Configure permissões DaPI para a sua aplicação:

1. Em **Gerir,** selecione **permissões API**.
1. Selecione **Adicionar uma permissão** e, em seguida, certifique-se de que selecionou **APIs**da Microsoft .
1. Selecione **Microsoft Graph**.
1. Selecione **permissões delegadas,** procure *user.Read* e verifique se **o Utilizador.Read** está selecionado.
1. Se tiver feito alterações, selecione **Adicionar permissões** para as salvar.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Permitir a autenticação integrada em domínios federados (opcional)

Para ativar a autenticação integrada no Windows quando é utilizada com um domínio AD Azure federado, o manifesto de aplicação deve permitir capacidades adicionais. Volte para a sua aplicação no Estúdio Visual.

1. Open *Package.appxmanifest*.
1. Selecione Capacidades e ative as **seguintes** definições:

   * **Autenticação empresarial**
   * **Redes Privadas (Servidor de & cliente)**
   * **Certificados de Utilizador Partilhados**

> [!IMPORTANT]
> [A autenticação integrada](https://aka.ms/msal-net-iwa) do Windows não está configurada por padrão para esta amostra. As aplicações `Enterprise Authentication` `Shared User Certificates` que solicitam ou capacidades requerem um nível de verificação mais elevado pela Windows Store. Além disso, nem todos os desenvolvedores querem realizar o nível mais elevado de verificação. Ative esta definição apenas se precisar de Autenticação Integrada do Windows com um domínio AD Azure federado.

## <a name="test-your-code"></a>Teste o seu código

Para testar a sua aplicação, selecione F5 para executar o seu projeto no Estúdio Visual. A sua janela principal aparece:

![Interface de utilizador da aplicação](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Quando estiver pronto para testar, selecione **Call Microsoft Graph API**. Em seguida, utilize uma conta organizacional Azure AD ou uma conta Microsoft, como live.com ou outlook.com, para iniciar sessão. Na primeira vez que um utilizador executa isto, a aplicação exibe uma janela a pedir ao utilizador para iniciar sessão.

### <a name="consent"></a>Consentimento

A primeira vez que faz sessão na sua candidatura, é-lhe apresentado um ecrã de consentimento semelhante ao seguinte. Selecione **Sim** para consentir explicitamente no acesso:

![Ecrã de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Resultados esperados

Vê as informações de perfil do utilizador devolvidas pela chamada da Microsoft Graph API no ecrã **api Call Results:**

![Ecrã de resultados de chamada api](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Você também vê informações básicas `AcquireTokenInteractive` sobre `AcquireTokenSilent` o símbolo adquirido via ou na caixa **Token Info:**

|Propriedade  |Formato  |Descrição |
|---------|---------|---------|
|`Username` |`user@domain.com` |O nome de utilizador que identifica o utilizador.|
|`Token Expires` |`DateTime` |O tempo em que o símbolo expira. A MSAL prolonga a data de validade renovando o símbolo conforme necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

A Microsoft Graph API requer a possibilidade de ler o `user.read` perfil de um utilizador. Este âmbito é adicionado por padrão em todas as aplicações registadas no Portal de Registo de Aplicações. Outras APIs para o Microsoft Graph e APIs personalizadas para o seu servidor back-end podem requerer âmbitos adicionais. Por exemplo, a Microsoft Graph `Calendars.Read` API requer a possibilidade de listar os calendários do utilizador.

Para aceder aos calendários do utilizador no contexto `Calendars.Read` de uma aplicação, adicione a permissão delegada à informação de registo de aplicações. Em seguida, adicione o `Calendars.Read` âmbito à `acquireTokenSilent` chamada.

> [!NOTE]
> Os utilizadores podem ser solicitados para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1

Recebe uma das seguintes mensagens de erro quando insere a sua aplicação num domínio AD Azure federado:

* Nenhum certificado de cliente válido encontrado no pedido.
* Não há certificados válidos encontrados na loja de certificados do utilizador.
* Tente novamente escolher um método de autenticação diferente.

Causa: As capacidades da empresa e do certificado não estão ativadas.

Solução: Siga os passos em [Autenticação Integrada Enable em domínios federados (opcional)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Permite a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tenta utilizar o Windows Hello num computador Windows 10 para iniciar sessão num ambiente com a autenticação de vários fatores configurada. A lista de certificados é apresentada. No entanto, se optar por utilizar o pin, a janela PIN nunca é apresentada.

Causa: Este problema é uma limitação conhecida do corretor de autenticação web em aplicações UWP que funcionam no ambiente de trabalho do Windows 10. Funciona bem no Windows 10 Mobile.

Seleção De **Sessão com outras opções**. Em seguida, selecione **Iniciar sessão com um nome**de utilizador e senha . Selecione **Fornecer a sua palavra-passe**. Em seguida, passe pelo processo de autenticação do telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

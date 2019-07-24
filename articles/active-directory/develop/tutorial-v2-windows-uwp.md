---
title: Introdução ao UWP da plataforma de identidade da Microsoft | Azure
description: Como os aplicativos Plataforma Universal do Windows (UWP) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade da plataforma Microsoft Identity.
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae3f9f2f4a9f6966d3453123773887c244cf4e2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334066"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Chamar a API Microsoft Graph de um aplicativo Plataforma Universal do Windows (XAML)

> [!div renderon="docs"]

Este guia explica como um aplicativo de Plataforma Universal do Windows nativo (UWP) pode solicitar um token de acesso e, em seguida, chamar Microsoft Graph API. O guia também se aplica a outras APIs que exigem tokens de acesso do ponto de extremidade da plataforma Microsoft Identity.

No final deste guia, seu aplicativo chama uma API protegida usando contas pessoais. Os exemplos são outlook.com, live.com e outros. Seu aplicativo também chama contas corporativas e de estudante de qualquer empresa ou organização que tenha Azure Active Directory (Azure AD).

>[!NOTE]
> Este guia requer o Visual Studio 2017 com o desenvolvimento Plataforma Universal do Windows instalado. Consulte [Get Set up](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para obter instruções para baixar e configurar o Visual Studio para desenvolver plataforma universal do Windows aplicativos.

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Este guia cria um aplicativo UWP de exemplo que consulta Microsoft Graph API ou uma API Web que aceita tokens do ponto de extremidade da plataforma Microsoft Identity. Para esse cenário, um token é adicionado às solicitações HTTP por meio do cabeçalho Authorization. A MSAL (biblioteca de autenticação da Microsoft) lida com aquisições e renovações de tokens.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft|

## <a name="set-up-your-project"></a>Configurar seu projeto

Esta seção fornece instruções passo a passo para integrar um aplicativo .NET da área de trabalho do Windows (XAML) com a *entrada com a Microsoft*. Em seguida, ele pode consultar APIs Web que exigem um token, como Microsoft Graph API.

Este guia cria um aplicativo que exibe um botão que consulta API do Graph, um botão de saída e caixas de texto que exibem os resultados das chamadas.

> [!NOTE]
> Deseja baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) e pule para a etapa de(#register-your-application "registro do aplicativo") de [registro de aplicativo]para configurar o exemplo de código antes de ele ser executado.

### <a name="create-your-application"></a>Criar seu aplicativo

1. No Visual Studio, selecione **arquivo** > **novo** > **projeto**.
2. Em **modelos**, selecione **Visual C#** .
3. Selecione **Aplicação em branco (Universal Windows)** .
4. Nomeie o aplicativo e selecione **OK**.
5. Se solicitado, selecione qualquer versão para o **destino** e  as versões mínimas e selecione **OK**.

    >![Versões mínimas e de destino](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Adicionar a biblioteca de autenticação da Microsoft ao seu projeto
1. No Visual Studio, selecione **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**.
2. Copie e cole o seguinte comando na janela do **console do Gerenciador de pacotes** :

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Esse comando instala a [biblioteca de autenticação da Microsoft](https://aka.ms/msal-net). O MSAL adquire, armazena em cache e atualiza os tokens de usuário que acessam APIs protegidas pela plataforma de identidade da Microsoft.

## <a name="create-your-applications-ui"></a>Criar a interface do usuário do seu aplicativo

Um arquivo **MainPage. XAML** é criado automaticamente como parte do seu modelo de projeto. Abra esse arquivo e siga as instruções:

* Substitua o nó da **grade** do aplicativo pelo seguinte código:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Use MSAL para obter um token para Microsoft Graph API

Esta seção mostra como usar o MSAL para obter um token para Microsoft Graph API.

1.  Em **MainPage.XAML.cs**, adicione a referência para MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Substitua o código da sua <code>MainPage</code> classe pelo código a seguir:

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
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
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
    ```

### <a name="more-information"></a>Mais informações

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Uma chamada para o `AcquireTokenInteractive` método resulta em uma janela que solicita que os usuários entrem. Os aplicativos geralmente exigem que os usuários entrem interativamente na primeira vez que precisam acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar. Um exemplo é quando a senha de um usuário expira.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `AcquireTokenSilent` método manipula aquisições de tokens e renovações sem nenhuma interação do usuário. Depois `AcquireTokenInteractive` que o é executado pela primeira vez e as credenciais do usuário são solicitadas, `AcquireTokenSilent` o método deve ser usado para solicitar tokens para chamadas subsequentes porque ele adquire os tokens silenciosamente. MSAL manipulará o cache de token e a renovação.

Eventualmente, o `AcquireTokenSilent` método falha. Os motivos para a falha podem ser que os usuários tenham desconectado ou alterado sua senha em outro dispositivo. Quando o MSAL detecta que o problema pode ser resolvido exigindo uma ação interativa, ele dispara uma `MsalUiRequiredException` exceção. Seu aplicativo pode manipular essa exceção de duas maneiras:

* Ele pode fazer uma chamada `AcquireTokenInteractive` imediatamente. Essa chamada resulta na solicitação do usuário para entrar. Normalmente, esse padrão é usado em aplicativos online em que não há conteúdo offline disponível para o usuário. O exemplo gerado por essa configuração interativa segue o padrão. Você o verá em ação na primeira vez em que executar o exemplo.
  * Como nenhum usuário usou o aplicativo, `accounts.FirstOrDefault()` ele contém um valor nulo e uma `MsalUiRequiredException` exceção é lançada.
  * O código no exemplo manipula a exceção chamando `AcquireTokenInteractive`. Essa chamada resulta na solicitação do usuário para entrar.

* Ou, em vez disso, ele apresenta uma indicação visual para os usuários de que uma conexão interativa é necessária. Em seguida, eles podem selecionar o horário certo para entrar. Ou o aplicativo pode tentar `AcquireTokenSilent` novamente mais tarde. Frequentemente, esse padrão é usado quando os usuários podem usar outras funcionalidades do aplicativo sem interrupções. Um exemplo é quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam entrar para acessar o recurso protegido ou atualizar as informações desatualizadas. Caso contrário, o aplicativo pode decidir tentar `AcquireTokenSilent` novamente quando a rede for restaurada depois que ela estiver temporariamente indisponível.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar Microsoft Graph API usando o token que você acabou de obter

* Adicione o novo método a seguir ao **MainPage.XAML.cs**. Esse método é usado para fazer uma `GET` solicitação em relação a API do Graph usando `Authorization` um cabeçalho:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste aplicativo de exemplo, o `GetHttpContentWithToken` método é usado para fazer uma solicitação `GET` http em um recurso protegido que requer um token. Em seguida, o método retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no cabeçalho de **autorização http** . Para este exemplo, o recurso é o Microsoft Graph ponto de extremidade do API **me** , que exibe as informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

* Para desconectar o usuário, adicione o seguinte método a **MainPage.XAML.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> O MSAL.NET usa métodos assíncronos para adquirir tokens ou manipular contas e, portanto, você precisa cuidar da realização de ações da interface do usuário no thread da `Dispatcher.RunAsync`interface do usuário, portanto, o e as precauções para chamar`ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Mais informações sobre a saída

O `SignOutButton_Click` método remove o usuário do cache de usuário MSAL. Esse método informa efetivamente ao MSAL para esquecer o usuário atual. Em seguida, uma solicitação futura para adquirir um token é realizada com sucesso apenas se for feita para ser interativa.
O aplicativo neste exemplo dá suporte a um único usuário. Mas o MSAL dá suporte a cenários em que mais de uma conta pode ser conectada ao mesmo tempo. Um exemplo é um aplicativo de email em que um usuário tem várias contas.

## <a name="display-basic-token-information"></a>Exibir informações básicas de token

* Adicione o seguinte método a **MainPage.XAML.cs** para exibir informações básicas sobre o token:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
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

### <a name="more-information"></a>Mais informações

Os tokens de ID adquiridos por meio do **OpenID Connect** também contêm um pequeno subconjunto de informações pertinentes ao usuário. `DisplayBasicTokenInfo`exibe informações básicas contidas no token. Os exemplos são o nome de exibição e a ID do usuário, a data de validade do token e a cadeia de caracteres que representa o token de acesso em si. Se você selecionar o botão **chamar Microsoft Graph API** várias vezes, verá que o mesmo token foi reutilizado para solicitações subsequentes. Você também pode ver a data de expiração estendida quando MSAL decide que é hora de renovar o token.

## <a name="register-your-application"></a>Registar a sua aplicação

Agora você precisa registrar seu aplicativo no portal de registro de aplicativos da Microsoft:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se sua conta estiver presente em mais de um locatário do Azure AD, `Directory + Subscription` selecione no canto superior direito no menu na parte superior da página e alterne a sessão do portal para o locatário do Azure ad desejado.
1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `UWP-App-calling-MSGraph`.
   - Na seção **tipos de conta com suporte** , selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .
   - Selecione **Registar** para criar a aplicação.
1. Na página **visão geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para mais tarde. Volte para o Visual Studio, abra **MainPage.XAML.cs**e substitua o valor de CLIENTID pela ID do aplicativo que você acabou de registrar:
1. Na lista de páginas da aplicação, selecione **Autenticação**.
   1. Na seção **URIs** de redirecionamento, na lista URIs de redirecionamento:
   1. Na coluna **tipo** , selecione **cliente público (Mobile & Desktop)** .
   1. Insira `urn:ietf:wg:oauth:2.0:oob` na coluna **URI** de redirecionamento.
1. Selecione **Guardar**.
1. Na lista de páginas do aplicativo, selecione permissões de **API**
   - Clique no botão **Adicionar uma permissão** e, em seguida,
   - Verifique se a guia **da API da Microsoft** está selecionada
   - Na seção *APIs da Microsoft comumente usadas* , clique em **Microsoft Graph**
   - Na seção **permissões delegadas** , verifique se as permissões corretas estão marcadas: **User. Read**. Use a caixa de pesquisa, se necessário.
   - Selecione o botão **adicionar permissões**

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Habilitar a autenticação integrada em domínios federados (opcional)

Para habilitar a autenticação integrada do Windows quando ela é usada com um domínio federado do Azure AD, o manifesto do aplicativo deve habilitar recursos adicionais:

1. Clique duas vezes em **Package. appxmanifest**.
2. Selecione a guia **recursos** e verifique se as seguintes configurações estão habilitadas:

    - Autenticação corporativa
    - Redes privadas (servidor de & cliente)
    - Certificados de usuário compartilhado

> [!IMPORTANT]
> A [autenticação integrada do Windows](https://aka.ms/msal-net-iwa) não está configurada por padrão para este exemplo. Os aplicativos que solicitam recursos de *autenticação corporativa* ou *certificados de usuário compartilhados* exigem um nível mais alto de verificação pela Windows Store. Além disso, nem todos os desenvolvedores querem executar o nível mais alto de verificação. Habilite essa configuração somente se precisar de autenticação integrada do Windows com um domínio federado do Azure AD.

## <a name="test-your-code"></a>Testar seu código

Para testar seu aplicativo, selecione F5 para executar o projeto no Visual Studio. A janela principal é exibida:

![Interface do usuário do aplicativo](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Quando você estiver pronto para testar, selecione **chamar API de Microsoft Graph**. Em seguida, use uma conta institucional do Azure AD ou um conta Microsoft, como live.com ou outlook.com, para entrar. Se for a primeira vez, você verá uma janela solicitando que o usuário entre:

![Página de entrada](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Consentimento

Na primeira vez que você entrar em seu aplicativo, será exibida uma tela de consentimento semelhante à seguinte. Selecione **Sim** para consentir explicitamente o acesso:

![Tela de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Resultados esperados

Você vê as informações de perfil do usuário retornadas pelo Microsoft Graph chamada à API na tela de **resultados da chamada à API** :

![Tela de resultados da chamada à API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Você também verá informações básicas sobre o token adquirido `AcquireTokenInteractive` via `AcquireTokenSilent` ou na caixa de **informações do token** :

|Propriedade  |Formato  |Descrição |
|---------|---------|---------|
|**Nome de Utilizador** |<span>user@domain.com</span> |O nome de usuário que identifica o usuário.|
|**O token expira** |DateTime |A hora em que o token expira. MSAL estende a data de expiração renovando o token conforme necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

Microsoft Graph API requer que o escopo *User. Read* Leia o perfil de um usuário. Esse escopo é adicionado automaticamente por padrão em cada aplicativo registrado no portal de registro de aplicativo. Outras APIs para Microsoft Graph e APIs personalizadas para seu servidor back-end podem exigir escopos adicionais. Microsoft Graph API requer o escopo *calendários. Read* para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *calendários. Read* para as informações de registro do aplicativo. Em seguida, adicione o escopo *calendários. Read* à `acquireTokenSilent` chamada.

> [!NOTE]
> Os usuários podem ser solicitados a fornecer consentirs adicionais à medida que você aumenta o número de escopos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1

Você recebe uma das seguintes mensagens de erro ao entrar em seu aplicativo em um domínio federado do Azure AD:

* Nenhum certificado de cliente válido encontrado na solicitação.
* Nenhum certificado válido encontrado no repositório de certificados do usuário.
* Tente escolher novamente um método de autenticação diferente.

**Faz** Os recursos corporativos e de certificado não estão habilitados.

**Soluções** Siga as etapas em [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Habilite a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tente usar o Windows Hello em um computador com Windows 10 para entrar em um ambiente com a autenticação multifator configurada. A lista de certificados é apresentada. No entanto, se você optar por usar seu PIN, a janela do PIN nunca será apresentada.

**Faz** Esse problema é uma limitação conhecida do agente de autenticação da Web em aplicativos UWP executados no Windows 10 desktop. Ele funciona bem no Windows 10 Mobile.

**Resolução** Selecione **entrar com outras opções**. Em seguida, selecione **entrar com um nome de usuário e senha**. Selecione **fornecer sua senha**. Em seguida, percorra o processo de autenticação por telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

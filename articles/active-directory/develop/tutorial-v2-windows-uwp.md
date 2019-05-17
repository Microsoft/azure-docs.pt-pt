---
title: Plataforma de identidade do Microsoft UWP introdução | Azure
description: Como os aplicativos da plataforma Universal do Windows (UWP) podem chamar uma API que requer que os tokens de acesso pelo ponto de final de plataforma de identidade do Microsoft.
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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b7e59e39adbb485738ca66b7ad8e5ba8293ddc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784985"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Chamar o Microsoft Graph API a partir de uma aplicação plataforma Universal do Windows (XAML)

> [!div renderon="docs"]

Este guia explica como um aplicativo da plataforma Universal do Windows (UWP) nativo pode pedir um token de acesso e, em seguida, chamar o Microsoft Graph API. O guia também se aplica a outros APIs que precisam de tokens de acesso do ponto de extremidade do Microsoft identity platform.

No final deste guia, seu aplicativo chama uma API protegida através da utilização de contas pessoais. Os exemplos são o outlook.com, live.com e outros. Seu aplicativo também chama contas profissionais e escolares de qualquer empresa ou organização que tenha o Azure Active Directory (Azure AD).

>[!NOTE]
> Este guia requer o Visual Studio 2017 com o desenvolvimento de plataforma Universal do Windows instalado. Ver [mãos à obra](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para obter instruções transferir e configurar o Visual Studio para desenvolver aplicações da plataforma Universal do Windows.

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como funciona a aplicação de exemplo gerada por este tutorial](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Este guia cria um exemplo de aplicação de UWP que consulta o Graph API da Microsoft ou uma API Web que aceita tokens do ponto de extremidade de plataforma de identidade Microsoft. Para este cenário, um token é adicionado a pedidos de HTTP com o cabeçalho de autorização. Biblioteca de autenticação da Microsoft (MSAL) processa aquisições de token e as renovações.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft|

## <a name="set-up-your-project"></a>Configurar seu projeto

Esta secção fornece instruções passo a passo para integrar uma aplicação .NET de Desktop do Windows (XAML), com *início de sessão com a Microsoft*. Em seguida, ele pode consultar as APIs da Web que exigem um token, como o Microsoft Graph API.

Este guia cria um aplicativo que exibe um botão que consultas do Graph API, um botão para terminar sessão e caixas de texto que apresentam os resultados das chamadas.

> [!NOTE]
> Pretende transferir o projeto do Visual Studio este exemplo em vez disso? [Transfira um projeto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) e avance para o [registo de aplicação](#register-your-application "passo de registo de aplicação") passo para configurar o exemplo de código antes da execução.

### <a name="create-your-application"></a>Criar a sua aplicação

1. No Visual Studio, selecione **arquivo** > **New** > **projeto**.
2. Sob **modelos**, selecione **Visual c#**.
3. Selecione **Aplicação em branco (Universal Windows)**.
4. Dê um nome da aplicação e selecione **OK**.
5. Se lhe for pedido, selecione qualquer versão para **destino** e **mínimo** versões e selecione **OK**.

    >![Versões mínimo e de destino](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Adicionar a biblioteca de autenticação da Microsoft ao seu projeto
1. No Visual Studio, selecione **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**.
2. Copie e cole o seguinte comando no **Package Manager Console** janela:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Este comando instala [biblioteca de autenticação da Microsoft](https://aka.ms/msal-net). A MSAL adquire, coloca em cache e atualiza os tokens de utilizador que acessem a APIs protegidas por plataforma de identidade da Microsoft.

## <a name="create-your-applications-ui"></a>Criar a IU da sua aplicação

R **mainpage. XAML** ficheiro é criado automaticamente como parte do seu modelo de projeto. Abrir este ficheiro e, em seguida, siga as instruções:

* Substitua o seu aplicativo **Grid** nó com o código a seguir:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Utilizar a MSAL para obter um token para a Microsoft Graph API

Esta secção mostra como utilizar a MSAL para obter um token para a Microsoft Graph API.

1.  Na **MainPage.xaml.cs**, adicionar a referência para a MSAL para a classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Substitua o código de seu <code>MainPage</code> classe com o código a seguir:

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
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
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

Uma chamada para o `AcquireTokenInteractive` método resulta numa janela que solicita aos utilizadores iniciar sessão. Aplicativos exigem, normalmente, os utilizadores iniciem sessão interativamente na primeira vez que precisam acessar um recurso protegido. Também poderá ter de iniciar sessão quando ocorre uma falha de uma operação silenciosa para adquirir um token. Um exemplo é quando palavra-passe de um utilizador tiver expirado.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `AcquireTokenSilent` método processa a aquisições de token e as renovações sem qualquer interação do utilizador. Após `AcquireTokenInteractive` é executado pela primeira vez e é pedido ao utilizador as credenciais, o `AcquireTokenSilent` método deve ser utilizado para pedir tokens para chamadas subseqüentes porque adquire tokens silenciosamente. A MSAL processará a cache de tokens e a renovação.

Eventualmente, o `AcquireTokenSilent` falha do método. Motivos da falha podem ser que os utilizadores tenham terminar a sessão ou alterar a palavra-passe noutro dispositivo. Quando a MSAL Deteta que o problema pode ser resolvido, exigindo que uma ação interativa, ele é disparado um `MsalUiRequiredException` exceção. Seu aplicativo pode manipular essa exceção de duas formas:

* Ele pode fazer uma chamada contra `AcquireTokenInteractive` imediatamente. Esta chamada resulta em pedir ao utilizador para iniciar sessão. Normalmente, esse padrão é usado em aplicativos online onde não existe nenhum conteúdo disponível offline para o utilizador. O exemplo gerado por esta configuração assistida segue o padrão. Vê-lo em tempo de ação a primeira que executa o exemplo.
  * Uma vez que nenhum utilizador tiver utilizado o aplicativo `accounts.FirstOrDefault()` contém um valor nulo e um `MsalUiRequiredException` exceção é lançada.
  * O código no exemplo, em seguida, processa a exceção ao chamar `AcquireTokenInteractive`. Esta chamada resulta em pedir ao utilizador para iniciar sessão.

* Ou, em vez disso, ele apresenta uma indicação visual para os usuários que um início de sessão interativo é necessário. Em seguida, podem selecionar o momento certo para iniciar sessão. Ou o aplicativo pode tentar novamente `AcquireTokenSilent` mais tarde. Com freqüência, esse padrão é usado quando os utilizadores podem utilizar outras funcionalidades da aplicação sem interrupção. Um exemplo é quando o conteúdo offline está disponível no aplicativo. Neste caso, os usuários podem decidir quando pretende iniciar sessão para aceder ao recurso protegido ou atualizar as informações Desatualizadas. Ou outra aplicação pode decidir repetir `AcquireTokenSilent` quando a rede é restaurada depois de estar temporariamente indisponível.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar o Microsoft Graph API utilizando o token que acabou de obteve

* Adicionar o novo método seguinte à **MainPage.xaml.cs**. Este método é utilizado para efetuar uma `GET` pedido em relação a Graph API utilizando um `Authorization` cabeçalho:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Obter mais informações sobre como fazer uma chamada REST em relação a uma API protegida

Nesse aplicativo de exemplo, o `GetHttpContentWithToken` método é utilizado para fazer um HTTP `GET` pedido em relação a um recurso protegido que necessita de um token. Em seguida, o método retorna o conteúdo para o chamador. Este método adiciona o token obtido no **autorização HTTP** cabeçalho. Para este exemplo, o recurso é o Microsoft Graph API **me** ponto final, que apresenta informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicione um método para terminar sessão do utilizador

* Para terminar sessão do utilizador, adicione o seguinte método à **MainPage.xaml.cs**:

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
> MSAL.NET utiliza métodos assíncronos para adquirir tokens ou manipular contas e, portanto, precisa cuidar de fazer as ações da interface do Usuário-ed no thread de interface do Usuário, portanto o `Dispatcher.RunAsync`e as precauções para chamar `ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Obter mais informações sobre o fim de sessão

O `SignOutButton_Click` método Remove o utilizador da cache de utilizador MSAL. Este método indica eficazmente ao MSAL esquecer o utilizador atual. Em seguida, um pedido de futuro para adquirir um token só será bem-sucedida se tornou-se a ser interativo.
A aplicação neste exemplo suporta um único utilizador. Mas MSAL oferece suporte a cenários em que mais de uma conta pode estar conectada ao mesmo tempo. Um exemplo é uma aplicação de e-mail em que um utilizador tiver várias contas.

## <a name="display-basic-token-information"></a>Apresentar informações básicas de token

* Adicione o seguinte método à **MainPage.xaml.cs** para apresentar informações básicas sobre o token:

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

Tokens de ID adquirida por meio **OpenID Connect** também conter um pequeno subconjunto de informações pertinentes para o usuário. `DisplayBasicTokenInfo` Mostra informações básicas, contidas no token. Os exemplos são nome a apresentar do utilizador e o ID, a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Se selecionar a **chamar o Microsoft Graph API** botão várias vezes, verá que o mesmo token foi reutilizado em solicitações subseqüentes. Também pode ver a data de validade alargada quando decide de MSAL está na altura de renovar o token.

## <a name="register-your-application"></a>Registar a sua aplicação

Agora precisa de registar a sua aplicação no Portal de registo de aplicação do Microsoft:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta está presente em mais do que um inquilino do Azure AD, selecione `Directory + Subscription` no canto superior direito no menu na parte superior da página e o comutador do inquilino a sessão do portal para o Azure AD pretendido.
1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
1. Selecione **novo registo**.
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `UWP-App-calling-MSGraph`.
   - Na **tipos de conta suportados** secção, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, o Skype, Xbox, Outlook.com)**.
   - Selecione **Registar** para criar a aplicação.
1. Na aplicação **descrição geral** página, encontre o **ID de aplicação (cliente)** valor e registe-a para utilizar mais tarde. Volte ao Visual Studio, abra **MainPage.xaml.cs**e substitua o valor do ID de cliente com o ID da aplicação que acabou de registar:
1. Na lista de páginas da aplicação, selecione **Autenticação**.
   1. Na **URIs de redirecionamento** secção, na lista de URIs de redirecionamento:
   1. Na **tipo** coluna select **cliente público (móvel e ambiente de trabalho)**.
   1. Introduza `urn:ietf:wg:oauth:2.0:oob` no **URI de REDIRECIONAMENTO** coluna.
1. Selecione **Guardar**.
1. Na lista de páginas da aplicação, selecione **permissões de API**
   - Clique nas **adicionar uma permissão** botão e, em seguida,
   - Certifique-se de que o **da API do Microsoft** separador está selecionado
   - Na *frequentemente utilizadas APIs da Microsoft* secção, clique em **Microsoft Graph**
   - Na **permissões delegadas** secção, certifique-se de que as permissões corretas são verificadas: **User.Read**. Utilize a caixa de pesquisa, se necessário.
   - Selecione o **adicionar permissões** botão

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Ativar a autenticação integrada nos domínios federados (opcionais)

Para ativar a autenticação de Windows-Integrated quando é utilizado com um Azure federado o domínio do AD, o manifesto do aplicativo deve habilitar recursos adicionais:

1. Faça duplo clique em **Package. appxmanifest**.
2. Selecione o **capacidades** separador e certifique-se de que as seguintes definições estão ativadas:

    - Autenticação empresarial
    - Redes privadas (cliente e servidor)
    - Certificados de utilizador partilhados

> [!IMPORTANT]
> [Autenticação Windows integrada](https://aka.ms/msal-net-iwa) não está configurado por predefinição para este exemplo. Aplicativos que pedem *autenticação empresarial* ou *certificados de utilizador partilhado* capacidades exigem um nível mais elevado de verificação, a Windows Store. Além disso, nem todos os desenvolvedores pretendem executar o nível mais elevado de verificação. Ative esta definição só se precisar de autenticação integrada do Windows com um Azure federado domínio do AD.

## <a name="test-your-code"></a>Testar o seu código

Para testar a sua aplicação, selecione F5 para executar o seu projeto no Visual Studio. É apresentada a janela principal:

![Interface do usuário do aplicativo](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Quando estiver pronto para testar, selecione **chamar o Microsoft Graph API**. Em seguida, utilize uma conta organizacional do Azure AD ou uma conta Microsoft, tais como live.com ou outlook.com, para iniciar sessão. Se for a primeira vez, verá uma janela de pedir ao utilizador para iniciar sessão:

![Página de início de sessão](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Consentimento

A primeira vez que iniciar sessão sua aplicação, é apresentado um ecrã de consentimento semelhante ao seguinte. Selecione **Sim** consentir explicitamente para acesso:

![Ecrã de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Resultados esperados

Ver informações de perfil de utilizador devolvidas pela chamada de API do Microsoft Graph no **os resultados da chamada API** ecrã:

![Tela de resultados da chamada de API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Também ver informações básicas sobre o token adquirido através de `AcquireTokenInteractive` ou `AcquireTokenSilent` no **informações sobre o Token** caixa:

|Propriedade  |Formato  |Descrição |
|---------|---------|---------|
|**Nome de Utilizador** |<span>user@domain.com</span> |O nome de utilizador que identificam o utilizador.|
|**Token expira** |DateTime |O tempo que o token expira. A MSAL expande a data de expiração ao renovar o token, se necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Obter mais informações sobre âmbitos e permissões delegadas

Requer o Microsoft Graph API a *user.read* âmbito para ler um perfil de utilizador. Este âmbito é adicionado automaticamente por predefinição em todos os aplicativos que está registado no Portal de registo de aplicação. Outras APIs para o Microsoft Graph e APIs personalizadas para o seu servidor de back-end, pode exigir âmbitos adicionais. Requer o Microsoft Graph API a *Calendars.Read* âmbito para listar calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione a *Calendars.Read* delegado permissão para as informações de registo de aplicação. Em seguida, adicione a *Calendars.Read* definir o âmbito para o `acquireTokenSilent` chamar.

> [!NOTE]
> Poderá ser pedido aos utilizadores para consentimentos adicionais à medida que aumenta o número de âmbitos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1

Receber uma das seguintes mensagens de erro ao iniciar sessão na sua aplicação num Azure federado domínio AD:

* Não foi encontrado no pedido de certificado de cliente válido.
* Não foram encontrados no arquivo de certificados do utilizador de certificados válidos.
* Tente novamente com um método de autenticação diferentes.

**Causa:** Não são ativadas capacidades de Enterprise e o certificado.

**Solução:** Siga os passos em [autenticação integrada nos domínios federados](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Ativar [autenticação integrada nos domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tente utilizar o Windows Hello num computador Windows 10 para iniciar sessão num ambiente com a autenticação multifator configurada. É apresentada a lista de certificados. No entanto, se optar por utilizar o PIN, nunca é apresentada a janela PIN.

**Causa:** Este problema é uma limitação conhecida de Mediador de autenticação web nas aplicações UWP executadas na área de trabalho do Windows 10. Ele funciona bem no Windows 10 Mobile.

**Solução:** Selecione **iniciar sessão com as outras opções**. Em seguida, selecione **inicie sessão com um nome de utilizador e palavra-passe**. Selecione **forneça a palavra-passe**. Em seguida, percorra o processo de autenticação de telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

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
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a243b11c2789afe0b2eb7ffd8de032dc10d8d8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423341"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Chamar a API Microsoft Graph de um aplicativo Plataforma Universal do Windows (XAML)

> [!div renderon="docs"]

Este guia explica como um aplicativo de Plataforma Universal do Windows nativo (UWP) pode solicitar um token de acesso. Em seguida, o aplicativo chama Microsoft Graph API. O guia também se aplica a outras APIs que exigem tokens de acesso do ponto de extremidade da plataforma Microsoft Identity.

No final deste guia, seu aplicativo chama uma API protegida usando contas pessoais. Os exemplos são outlook.com, live.com e outros. Seu aplicativo também chama contas corporativas e de estudante de qualquer empresa ou organização que tenha Azure Active Directory (Azure AD).

>[!NOTE]
> Este guia requer o Visual Studio com o desenvolvimento Plataforma Universal do Windows instalado. Consulte [Get Set up](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para obter instruções para baixar e configurar o Visual Studio para desenvolver plataforma universal do Windows aplicativos.

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Este guia cria um aplicativo UWP de exemplo que consulta Microsoft Graph API. Para esse cenário, um token é adicionado às solicitações HTTP usando o cabeçalho Authorization. A MSAL (biblioteca de autenticação da Microsoft) lida com aquisições e renovações de tokens.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa o seguinte pacote NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft|

## <a name="set-up-your-project"></a>Configurar seu projeto

Esta seção fornece instruções passo a passo para integrar um aplicativo .NET da área de trabalho do Windows (XAML) com a entrada com a Microsoft. Em seguida, o aplicativo pode consultar APIs Web que exigem um token, como Microsoft Graph API.

Este guia cria um aplicativo que exibe um botão que consulta API do Graph e um botão para sair. Ele também exibe caixas de texto que contêm os resultados das chamadas.

> [!NOTE]
> Deseja baixar este projeto do Visual Studio de exemplo em vez de criá-lo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) e pule para a etapa de [registro do aplicativo](#register-your-application "etapa de registro do aplicativo") para configurar o exemplo de código antes de ele ser executado.

### <a name="create-your-application"></a>Criar seu aplicativo

1. Abra o Visual Studio e selecione **criar um novo projeto**.
1. Em **criar um novo projeto**, escolha **aplicativo em branco (universal do Windows)** para C# e selecione **Avançar**.
1. Em **configurar seu novo projeto**, nomeie o aplicativo e selecione **criar**.
1. Se solicitado, em **novo projeto plataforma universal do Windows**, selecione qualquer versão para **destino** e versões **mínimas** e selecione **OK**.

   ![Versões mínimas e de destino](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Adicionar a biblioteca de autenticação da Microsoft ao seu projeto

1. No Visual Studio, selecione **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**.
1. Copie e cole o seguinte comando na janela do **console do Gerenciador de pacotes** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Esse comando instala a [biblioteca de autenticação da Microsoft](https://aka.ms/msal-net). O MSAL adquire, armazena em cache e atualiza os tokens de usuário que acessam APIs protegidas pela plataforma de identidade da Microsoft.

### <a name="create-your-applications-ui"></a>Criar a interface do usuário do seu aplicativo

O Visual Studio cria *MainPage. XAML* como parte de seu modelo de projeto. Abra esse arquivo e, em seguida, substitua o nó da **grade** do aplicativo pelo seguinte código:

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Use MSAL para obter um token para Microsoft Graph API

Esta seção mostra como usar o MSAL para obter um token para Microsoft Graph API. Faça alterações no arquivo *MainPage.XAML.cs* .

1. No *MainPage.XAML.cs*, adicione as seguintes referências:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Substitua sua classe `MainPage` pelo código a seguir:

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

#### Obter um token de usuário interativamente<a name="more-information"></a>

O método `AcquireTokenInteractive` resulta em uma janela que solicita que os usuários entrem. Normalmente, os aplicativos exigem que os usuários entrem interativamente na primeira vez para acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar. Um exemplo é quando a senha de um usuário expira.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O método `AcquireTokenSilent` manipula aquisições e renovações de token sem nenhuma interação do usuário. Depois que `AcquireTokenInteractive` for executado pela primeira vez e solicitar credenciais ao usuário, use o método `AcquireTokenSilent` para solicitar tokens para chamadas posteriores. Esse método adquire os tokens silenciosamente. MSAL manipula o cache de token e a renovação.

Eventualmente, o método `AcquireTokenSilent` falha. Os motivos de falha incluem um usuário que desconectou ou alterou sua senha em outro dispositivo. Quando o MSAL detecta que o problema requer uma ação interativa, ele gera uma exceção de `MsalUiRequiredException`. Seu aplicativo pode manipular essa exceção de duas maneiras:

* Seu aplicativo chama `AcquireTokenInteractive` imediatamente. Essa chamada resulta na solicitação do usuário para entrar. Normalmente, use essa abordagem para aplicativos online em que não há conteúdo offline disponível para o usuário. O exemplo gerado por essa configuração interativa segue o padrão. Você o verá em ação na primeira vez em que executar o exemplo.

   Como nenhum usuário usou o aplicativo, `accounts.FirstOrDefault()` contém um valor nulo e gera uma exceção `MsalUiRequiredException`.

   O código no exemplo manipula a exceção chamando `AcquireTokenInteractive`. Essa chamada resulta na solicitação do usuário para entrar.

* Seu aplicativo apresenta uma indicação visual para os usuários de que precisam entrar. Em seguida, eles podem selecionar o horário certo para entrar. O aplicativo pode tentar novamente `AcquireTokenSilent` mais tarde. Use essa abordagem quando os usuários puderem usar outras funcionalidades de aplicativos sem interrupções. Um exemplo é quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam entrar. O aplicativo pode tentar novamente `AcquireTokenSilent` depois que a rede estava temporariamente indisponível.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar Microsoft Graph API usando o token que você acabou de obter

Adicione o novo método a seguir ao *MainPage.XAML.cs*:

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

 Esse método faz uma solicitação de `GET` de API do Graph usando um cabeçalho `Authorization`.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste aplicativo de exemplo, o método `GetHttpContentWithToken` faz uma solicitação HTTP `GET` em um recurso protegido que requer um token. Em seguida, o método retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no cabeçalho de **autorização http** . Para este exemplo, o recurso é o Microsoft Graph ponto de extremidade do API **me** , que exibe as informações de perfil do usuário.

### <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

Para desconectar o usuário, adicione o seguinte método a *MainPage.XAML.cs*:

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
> O MSAL.NET usa métodos assíncronos para adquirir tokens ou manipular contas. Você precisa dar suporte a ações de interface do usuário no thread da interface do usuário. Esse é o motivo para a chamada de `Dispatcher.RunAsync` e as precauções para chamar `ConfigureAwait(false)`.

#### Mais informações sobre como sair<a name="more-information-on-sign-out"></a>

O método `SignOutButton_Click` remove o usuário do cache de usuário MSAL. Esse método informa efetivamente ao MSAL para esquecer o usuário atual. Uma solicitação futura para adquirir um token é realizada com sucesso apenas se ele for interativo.

O aplicativo neste exemplo dá suporte a um único usuário. O MSAL dá suporte a cenários em que o usuário pode entrar em mais de uma conta. Um exemplo é um aplicativo de email em que um usuário tem várias contas.

### <a name="display-basic-token-information"></a>Exibir informações básicas de token

Adicione o seguinte método a *MainPage.XAML.cs* para exibir informações básicas sobre o token:

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

#### Mais informações<a name="more-information-1"></a>

Tokens de ID adquiridos usando o **OpenID Connect** também contêm um pequeno subconjunto de informações pertinentes ao usuário. `DisplayBasicTokenInfo` exibe informações básicas contidas no token. Essas informações incluem o nome de exibição e a ID do usuário. Ele também inclui a data de validade do token e a cadeia de caracteres que representa o token de acesso em si. Se você selecionar o botão **chamar Microsoft Graph API** várias vezes, verá que o mesmo token foi reutilizado para solicitações posteriores. Você também pode ver a data de expiração estendida quando MSAL decide que é hora de renovar o token.

### <a name="display-message"></a>Exibir mensagem

Adicione o novo método a seguir ao *MainPage.XAML.cs*:

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

Agora você precisa registrar seu aplicativo:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **do Azure Active Directory** > **registos das aplicações**.
1. Selecione **novo registro**. Insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo, por exemplo, *UWP-app-Calling-MSGraph*.
1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox)** e, em seguida, selecione **registrar** para continuar.
1. Na página Visão geral, localize o valor da **ID do aplicativo (cliente)** e copie-o. Volte para o Visual Studio, abra *MainPage.XAML.cs*e substitua o valor de `ClientId` por esse valor.

Configure a autenticação para seu aplicativo:

1. De volta ao [portal do Azure](https://portal.azure.com), em **gerenciar**, selecione **autenticação**.
1. Na seção **redirecionar uris** | **URIs de redirecionamento sugeridos para clientes públicos (móvel, área de trabalho)** , marque **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Selecione **Guardar**.

Configurar permissões de API para seu aplicativo:

1. Em **gerenciar**, selecione **permissões de API**.
1. Selecione **Adicionar uma permissão** e, em seguida, verifique se você selecionou **APIs da Microsoft**.
1. Selecione **Microsoft Graph**.
1. Selecione **permissões delegadas**, procure por *usuário. Leia* e verifique se **User. Read** está selecionado.
1. Se você fez alterações, selecione **adicionar permissões** para salvá-las.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Habilitar a autenticação integrada em domínios federados (opcional)

Para habilitar a autenticação integrada do Windows quando ela é usada com um domínio federado do Azure AD, o manifesto do aplicativo deve habilitar recursos adicionais. Volte para seu aplicativo no Visual Studio.

1. Abra *Package. appxmanifest*.
1. Selecione **recursos** e habilite as seguintes configurações:

   * **Autenticação corporativa**
   * **Redes privadas (servidor de & cliente)**
   * **Certificados de usuário compartilhado**

> [!IMPORTANT]
> A [autenticação integrada do Windows](https://aka.ms/msal-net-iwa) não está configurada por padrão para este exemplo. Os aplicativos que solicitam recursos de `Enterprise Authentication` ou `Shared User Certificates` exigem um nível mais alto de verificação pela Windows Store. Além disso, nem todos os desenvolvedores querem executar o nível mais alto de verificação. Habilite essa configuração somente se precisar de autenticação integrada do Windows com um domínio federado do Azure AD.

## <a name="test-your-code"></a>Testar seu código

Para testar seu aplicativo, selecione F5 para executar o projeto no Visual Studio. A janela principal é exibida:

![Interface do usuário do aplicativo](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Quando você estiver pronto para testar, selecione **chamar API de Microsoft Graph**. Em seguida, use uma conta institucional do Azure AD ou um conta Microsoft, como live.com ou outlook.com, para entrar. Na primeira vez que um usuário executar isso, o aplicativo exibirá uma janela solicitando que o usuário entre.

### <a name="consent"></a>Consentimento

Na primeira vez que você entrar em seu aplicativo, será exibida uma tela de consentimento semelhante à seguinte. Selecione **Sim** para consentir explicitamente o acesso:

![Tela de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Resultados esperados

Você vê as informações de perfil do usuário retornadas pelo Microsoft Graph chamada à API na tela de **resultados da chamada à API** :

![Tela de resultados da chamada à API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Você também verá informações básicas sobre o token adquirido por meio de `AcquireTokenInteractive` ou `AcquireTokenSilent` na caixa **informações do token** :

|Propriedade  |Formato  |Descrição |
|---------|---------|---------|
|`Username` |`user@domain.com` |O nome de usuário que identifica o usuário.|
|`Token Expires` |`DateTime` |A hora em que o token expira. MSAL estende a data de expiração renovando o token conforme necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

Microsoft Graph API requer que o escopo de `user.read` Leia o perfil de um usuário. Esse escopo é adicionado por padrão em cada aplicativo registrado no portal de registro de aplicativo. Outras APIs para Microsoft Graph e APIs personalizadas para seu servidor back-end podem exigir escopos adicionais. Por exemplo, Microsoft Graph API requer o escopo de `Calendars.Read` para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada `Calendars.Read` às informações de registro do aplicativo. Em seguida, adicione o escopo de `Calendars.Read` à chamada `acquireTokenSilent`.

> [!NOTE]
> Os usuários podem ser solicitados a fornecer consentirs adicionais à medida que você aumenta o número de escopos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1

Você recebe uma das seguintes mensagens de erro ao entrar em seu aplicativo em um domínio federado do Azure AD:

* Nenhum certificado de cliente válido encontrado na solicitação.
* Nenhum certificado válido encontrado no repositório de certificados do usuário.
* Tente escolher novamente um método de autenticação diferente.

Causa: os recursos corporativos e de certificado não estão habilitados.

Solução: siga as etapas em [habilitar a autenticação integrada em domínios federados (opcional)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Habilite a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tente usar o Windows Hello em um computador com Windows 10 para entrar em um ambiente com a autenticação multifator configurada. A lista de certificados é apresentada. No entanto, se você optar por usar seu PIN, a janela do PIN nunca será apresentada.

Causa: esse problema é uma limitação conhecida do agente de autenticação da Web em aplicativos UWP executados no Windows 10 desktop. Ele funciona bem no Windows 10 Mobile.

Solução alternativa: selecione **entrar com outras opções**. Em seguida, selecione **entrar com um nome de usuário e senha**. Selecione **fornecer sua senha**. Em seguida, percorra o processo de autenticação por telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas:

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

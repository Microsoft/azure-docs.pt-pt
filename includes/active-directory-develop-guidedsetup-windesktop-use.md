---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0b00597deff5a498d54ffcfd9978a68e5b60c5f8
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203227"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utilizar a MSAL para obter um token para o Microsoft Graph API

Nesta secção, vai utilizar a MSAL para obter um token para o Microsoft Graph API.

1. Na *MainWindow.xaml.cs* de ficheiros, adicionar a referência para a MSAL para a classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Substitua o `MainWindow` classe código com o seguinte:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] _scopes = new string[] { "user.read" };

        public MainWindow()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;

            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();

            try
            {
                authResult = await app.AcquireTokenSilentAsync(_scopes, accounts.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
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
                ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Chamar o `AcquireTokenAsync` método resulta numa janela que solicita aos utilizadores iniciar sessão. Aplicativos exigem, normalmente, os utilizadores iniciem sessão interativamente na primeira vez que precisam acessar um recurso protegido. Também poderá ter de iniciar sessão quando uma operação silenciosa para adquirir um token falha (por exemplo, quando a senha de um usuário tenha expirada).

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `AcquireTokenSilentAsync` método processa a aquisições de token e as renovações sem qualquer interação do utilizador. Após `AcquireTokenAsync` é executado pela primeira vez, `AcquireTokenSilentAsync` é o método habitual a utilizar para obter os tokens que acedem a recursos protegidos por chamadas subsequentes, uma vez que chamadas para pedir ou renovar os tokens são efetuadas automaticamente.

Eventualmente, o `AcquireTokenSilentAsync` método irá falhar. Motivos da falha podem ser que o utilizador tem sessão ou alterado a palavra-passe noutro dispositivo. Quando a MSAL Deteta que o problema pode ser resolvido, exigindo que uma ação interativa, ele é disparado um `MsalUiRequiredException` exceção. Seu aplicativo pode manipular essa exceção de duas formas:

* Ele pode fazer uma chamada contra `AcquireTokenAsync` imediatamente. Esta chamada resulta em pedir ao utilizador para iniciar sessão. Esse padrão é normalmente usado em aplicativos online onde não existe nenhum conteúdo disponível offline para o utilizador. O exemplo gerado por esta configuração assistida segue este padrão, o que pode ver no tempo de ação a primeira que executar o exemplo. 

* Uma vez que nenhum utilizador tiver utilizado o aplicativo `PublicClientApp.Users.FirstOrDefault()` contém um valor nulo e um `MsalUiRequiredException` exceção é lançada. 

* O código no exemplo, em seguida, processa a exceção ao chamar `AcquireTokenAsync`, que resulta em pedir ao utilizador para iniciar sessão.

* Em vez disso, ele pode apresentar uma indicação visual aos utilizadores um interativo início de sessão é necessário, para que podem selecionar o momento certo para iniciar sessão. Ou o aplicativo pode tentar novamente `AcquireTokenSilentAsync` mais tarde. Este padrão é frequentemente utilizado quando os utilizadores podem utilizar outras funcionalidades da aplicação sem interrupção – por exemplo, quando o conteúdo offline está disponível no aplicativo. Neste caso, os usuários podem decidir quando pretende iniciar sessão para aceder ao recurso protegido ou atualizar as informações Desatualizadas. Em alternativa, o aplicativo pode decidir repetir `AcquireTokenSilentAsync` quando a rede é restaurada após ter sido temporariamente indisponível.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar a Graph API do Microsoft ao utilizar o token que acabou de obteve

Adicionar o novo método seguinte à sua `MainWindow.xaml.cs`. O método é usado para tornar um `GET` pedido em relação a Graph API com um cabeçalho de autorizar:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Obter mais informações sobre como fazer uma chamada REST em relação a uma API protegida

Este aplicativo de exemplo, vai utilizar o `GetHttpContentWithToken` método para fazer um HTTP `GET` em relação a um recurso protegido que necessita de um token do pedido e, em seguida, devolver o conteúdo para o chamador. Este método adiciona o token obtido no cabeçalho de autorização de HTTP. Para este exemplo, o recurso é o Microsoft Graph API *me* ponto final, que apresenta informações de perfil do usuário.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Adicione um método para terminar sessão um utilizador

Para terminar sessão um utilizador, adicione o seguinte método à sua `MainWindow.xaml.cs` ficheiro:

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
### <a name="more-information-about-user-sign-out"></a>Obter mais informações sobre a fim de sessão do utilizador

O `SignOutButton_Click` método Remove os utilizadores a partir da cache de utilizador MSAL, que indica eficazmente ao MSAL esquecer o utilizador atual para que um pedido de futuro para adquirir um token apenas terá êxito se que esteja a ser interativo.

Embora o aplicativo neste exemplo oferece suporte a usuários únicos, MSAL oferece suporte a cenários em que várias contas podem estar conectadas ao mesmo tempo. Um exemplo é uma aplicação de e-mail em que um utilizador tiver várias contas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Apresentar informações básicas de token

Para apresentar informações básicas sobre o token, adicione o método seguinte à sua *MainWindow.xaml.cs* ficheiro:

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
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

Além do token de acesso que é utilizado para chamar a API do Microsoft Graph, depois do utilizador inicia sessão no, a MSAL também obtém um token de ID. Este token contém um pequeno subconjunto de informações que são pertinentes para os utilizadores. O `DisplayBasicTokenInfo` método exibe as informações básicas que estão contidas no token. Por exemplo, exibe o nome a apresentar do usuário e ID, bem como a data de expiração do token e a cadeia de caracteres que representa o token de acesso em si. Pode selecionar o *chamar o Microsoft Graph API* botão várias vezes e ver que o mesmo token foi reutilizado em solicitações subseqüentes. Também pode ver a data de expiração que está sendo estendido quando MSAL decide está na altura de renovar o token.
<!--end-collapse-->

---
title: Tutorial - Inscreva-se nos utilizadores e chame uma API protegida a partir de uma aplicação Blazor WebAssembly
titleSuffix: Microsoft identity platform
description: Neste tutorial, inscreva-se nos utilizadores e chame uma API protegida utilizando a plataforma de identidade da Microsoft numa aplicação Blazor WebAssembly (WASM).
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 4d6401c53071235784a5371a4a6315e4e25a438f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680269"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Tutorial: Inscreva-se nos utilizadores e chame uma API protegida a partir de uma aplicação Blazor WebAssembly

Neste tutorial, você constrói uma aplicação Blazor WebAssembly que assina nos utilizadores e obtém dados do Microsoft Graph utilizando a plataforma de identidade da Microsoft e registando a sua aplicação no Azure Ative Directory (Azure AD). 

Neste tutorial:

> [!div class="checklist"]
>
> * Criar uma nova aplicação Blazor WebAssembly configurada para utilizar o Azure Ative Directory (Azure AD) para [autenticação e autorização](authentication-vs-authorization.md) utilizando a plataforma de identidade microsoft
> * Recupere dados de uma API web protegida, neste caso [Microsoft Graph](/graph/overview)

Este tutorial utiliza .NET Core 3.1. Os docs .NET contêm instruções sobre [como proteger uma aplicação Blazor WebAssembly](/aspnet/core/blazor/security/webassembly/graph-api) utilizando ASP.NET Core 5.0. 

Também temos um [tutorial para o Blazor Server.](tutorial-blazor-server.md) 

## <a name="prerequisites"></a>Pré-requisitos

* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Um inquilino da AD Azure onde pode registar uma aplicação. Se não tiver acesso a um inquilino AZure AD, pode obter um registando-se no [Microsoft 365 Developer Program](https://developer.microsoft.com/microsoft-365/dev-program) ou criando uma conta gratuita [Azure.](https://azure.microsoft.com/free)

## <a name="register-the-app-in-the-azure-portal"></a>Registe a app no portal Azure

Todas as aplicações que utilizem o Azure Ative Directory (Azure AD) para autenticação devem ser registadas no Azure AD. Siga as instruções no [Registo de um pedido](quickstart-register-app.md) com estas especificações:

- Para **tipos de conta suportada**, selecione Contas **apenas neste diretório organizacional**.
- Deixe o **URI de redirecionamento** descer definido para **Web** e insira `https://localhost:5001/authentication/login-callback` . A porta padrão para uma aplicação em execução em Kestrel é 5001. Se a aplicação estiver disponível numa porta diferente, especifique o número da porta em vez de `5001` .

Uma vez registado, na  >  **concessão Autenticação Implícita,** selecione as caixas de verificação para **fichas de acesso** e **fichas de identificação**, e, em seguida, selecione o botão **Guardar.**

## <a name="create-the-app-using-the-net-core-cli"></a>Crie a aplicação utilizando o .NET Core CLI

Para criar a aplicação, precisa dos modelos Blazor mais recentes. Pode instalá-los para o CLI .NET Core com o seguinte comando:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

Em seguida, executar o seguinte comando para criar a aplicação. Substitua os espaços reservados no comando com as informações adequadas da página geral da sua aplicação e execute o comando numa concha de comando. A localização de saída especificada com a `-o|--output` opção cria uma pasta de projeto se não existir e se tornar parte do nome da app.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Marcador de posição   | Nome do portal Azure       | Exemplo                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | ID da Aplicação (cliente) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID do Diretório (inquilino)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Testar a aplicação

Agora pode construir e executar a aplicação. Quando executar esta aplicação de modelo, deve especificar a estrutura a executar usando --estrutura. Este tutorial utiliza a Norma .NET 2.1, mas o modelo também suporta outros quadros.

```dotnetcli
dotnet run --framework netstandard2.1
```

No seu navegador, navegue para `https://localhost:5001` , e faça login usando uma conta de utilizador AZURE AD para ver a aplicação em execução e registro utilizadores com a plataforma de identidade Microsoft.

Os componentes deste modelo que permitem logins com AZure AD utilizando a plataforma de identidade da Microsoft são explicados no [ASP.NET doc sobre este tópico](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>Recuperar dados de uma API protegida (Microsoft Graph)

[O Microsoft Graph](/graph/overview) contém APIs que fornecem acesso aos dados do Microsoft 365 para os seus utilizadores, e suporta os tokens emitidos pela plataforma de identidade microsoft, o que faz com que seja uma boa API protegida para usar como exemplo. Nesta secção, adicione código para ligar para o Microsoft Graph e exibir os e-mails do utilizador na página "Fetch data" da aplicação.

Esta secção é escrita usando uma abordagem comum para chamar uma API protegida usando um cliente nomeado. O mesmo método pode ser utilizado para outras APIs protegidas que pretende chamar. No entanto, se pretender ligar para o Microsoft Graph a partir da sua aplicação, pode utilizar o SDK gráfico para reduzir a placa de caldeira. Os docs .NET contêm instruções sobre [como utilizar o Gráfico SDK](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0).

Antes de começar, faça login na sua app uma vez que irá fazer alterações nas permissões necessárias, e o seu token atual não funcionará. Se ainda não o fez, volte a executar a sua aplicação e selecione **Iniciar sessão** antes de atualizar o código abaixo.

Agora irá atualizar o registo e o código da sua aplicação para puxar os e-mails de um utilizador e exibir as mensagens dentro da app.

Em primeiro lugar, adicione a permissão da `Mail.Read` API ao registo da aplicação para que a Azure AD tenha conhecimento de que a aplicação irá solicitar o acesso ao e-mail dos seus utilizadores.

1. No portal Azure, selecione a sua aplicação nas **inscrições da App.**
1. Em **Gestão**, selecione **permissões API**.
1. **Selecione Adicionar uma permissão**  >  **Microsoft Graph**.
1. Selecione **Permissões Delegadas** e, em seguida, procure e selecione a permissão **Mail.Read.**
1. **Selecione Permissões de adicionar**.

Em seguida, adicione o seguinte ao ficheiro *.csproj* do seu projeto no netstandard2.1 **ItemGroup**. Isto permitir-lhe-á criar o HttpClient personalizado no passo seguinte.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Em seguida, modifique o código conforme especificado nos próximos passos. Estas alterações irão adicionar [tokens de acesso](access-tokens.md) aos pedidos de saída enviados para a API do Gráfico da Microsoft. Este padrão é discutido mais detalhadamente em [ASP.NET Core Blazor WebAssembly cenários adicionais de segurança](/aspnet/core/blazor/security/webassembly/additional-scenarios).

Primeiro, crie um novo ficheiro chamado *GraphAuthorizationMessageHandler.cs* com o seguinte código. Este manipulador será o utilizador a adicionar um token de acesso para os `User.Read` `Mail.Read` e âmbitos de utilização aos pedidos de saída para a API do Gráfico microsoft.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Em seguida, substitua o conteúdo do `Main` método em *Program.cs* pelo seguinte código. Este código faz uso do novo `GraphAPIAuthorizationMessageHandler` e adiciona `User.Read` `Mail.Read` e, como âmbito padrão, a aplicação irá solicitar quando o utilizador entrar pela primeira vez.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Por fim, substitua o conteúdo da página *FetchData.razor* pelo seguinte código. Este código recolhe dados de e-mail do utilizador a partir da API do Gráfico microsoft e apresenta-os como uma lista. Em `OnInitializedAsync` , o novo que utiliza o `HttpClient` token de acesso adequado é criado e usado para fazer o pedido para a Microsoft Graph API.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Agora, volte a lançar a aplicação. Vai reparar que é solicitado a dar acesso à aplicação para ler o seu correio. Isto é esperado quando uma aplicação solicita o `Mail.Read` âmbito.

Depois de conceder o consentimento, navegue na página "Recolher dados" para ler algum e-mail.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Screenshot da aplicação final. Tem um título que diz Olá Nicholas e mostra uma lista de e-mails pertencentes ao Nicholas.":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [As melhores práticas e recomendações da plataforma de identidade da Microsoft](./identity-platform-integration-checklist.md)
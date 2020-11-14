---
title: Tutorial - Criar uma aplicação Blazor Server que utiliza a plataforma de identidade da Microsoft para autenticação Rio Azure
titleSuffix: Microsoft identity platform
description: Neste tutorial, configura a autenticação utilizando a plataforma de identidade da Microsoft numa aplicação do Blazor Server.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: aaf716b4ac4c49f1d852e917ba818a10ecb541c4
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628038"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Tutorial: Criar uma aplicação Blazor Server que utiliza a plataforma de identidade da Microsoft para autenticação

O Blazor Server fornece suporte para hospedar componentes Razor no servidor numa aplicação core ASP.NET. Neste tutorial, aprende-se a implementar a autenticação e a obter dados do Microsoft Graph numa aplicação do Blazor Server utilizando a plataforma de identidade da Microsoft e registando a sua aplicação no Azure Ative Directory (Azure AD).

Também temos um tutorial para [o Blazor WASM.](tutorial-blazor-webassembly.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma nova aplicação Blazor Server configurada para utilizar o Azure Ative Directory (Azure AD) para autenticação
> * Lidar com a autenticação e autorização usando Microsoft.Identity.Web
> * Recuperar dados de uma API web protegida, Gráfico do Microsoft

## <a name="prerequisites"></a>Pré-requisitos

- [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Um inquilino da AD Azure onde pode registar uma aplicação. Se não tiver acesso a um inquilino AZure AD, pode obter um registando-se no [Microsoft 365 Developer Program](https://developer.microsoft.com/microsoft-365/dev-program) ou criando uma conta gratuita [Azure.](https://azure.microsoft.com/free)

## <a name="register-the-app-in-the-azure-portal"></a>Registe a app no portal Azure

Todas as aplicações que utilizem o Azure Ative Directory (Azure AD) para autenticação devem ser registadas no Azure AD. Siga as instruções no [Registo de um pedido](quickstart-register-app.md) com estas adições:

- Para **tipos de conta suportada** , selecione Contas **apenas neste diretório organizacional**.
- Deixe o **URI de redirecionamento** descer definido para **Web** e insira `https://localhost:5001/signin-oidc` . A porta padrão para uma aplicação em execução em Kestrel é 5001. Se a aplicação estiver disponível numa porta diferente, especifique o número da porta em vez de `5001` .

Na **Authentication**  >  **concessão Autenticação Implícita** , selecione as caixas de verificação para **tokens de acesso** e **fichas de identificação** e, em seguida, selecione o botão **Guardar.**

Finalmente, como a aplicação chama uma API protegida (neste caso, o Microsoft Graph), precisa de um segredo de cliente para verificar a sua identidade quando solicita um token de acesso para chamar essa API.

1. Dentro do mesmo registo de aplicações, em **Manage,** selecione **Certificados & segredos.**
2. Criar um **novo segredo de cliente** que nunca expira.
3. Tome nota do **valor** do segredo, pois irá usá-lo no próximo passo. Não pode aceder de novo quando se navega para longe deste painel. No entanto, pode recriá-lo conforme necessário.

## <a name="create-the-app-using-the-net-cli"></a>Criar a aplicação utilizando o .NET CLI

Executar o seguinte comando para descarregar os modelos para Microsoft.Identity.Web, que iremos utilizar neste tutorial.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Em seguida, executar o seguinte comando para criar a aplicação. Substitua os espaços reservados no comando com as informações adequadas da página geral da sua aplicação e execute o comando numa concha de comando. A localização de saída especificada com a `-o|--output` opção cria uma pasta de projeto se não existir e se tornar parte do nome da app.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Marcador de posição   | Nome do portal Azure       | Exemplo                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | ID da Aplicação (cliente) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID do Diretório (inquilino)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Agora, navegue para a sua nova app Blazor no seu editor e adicione o segredo do cliente ao *appsettings.jsem* arquivo, substituindo o texto "secret-from-app-registration".

```json
"ClientSecret": "xkAlNiG70000000_UI~d.OS4Dl.-Cy-1m3",
```

## <a name="test-the-app"></a>Testar a aplicação

Agora pode construir e executar a aplicação. Quando executar esta aplicação de modelo, deve especificar a estrutura a executar usando --estrutura. Este tutorial utiliza o .NET Core 3.1 SDK.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

No seu navegador, navegue para `https://localhost:5001` , e faça login usando uma conta de utilizador AZure AD para ver a aplicação em execução.

## <a name="retrieving-data-from-microsoft-graph"></a>Recuperar dados do Microsoft Graph

[O Microsoft Graph](/graph/overview) oferece uma gama de APIs que fornecem acesso aos dados microsoft 365 dos seus utilizadores. Ao utilizar a plataforma de identidade da Microsoft como fornecedor de identidade para a sua aplicação, tem acesso mais fácil a estas informações, uma vez que o Microsoft Graph suporta diretamente os tokens emitidos pela plataforma de identidade da Microsoft. Nesta secção, adicione código para exibir os e-mails assinados no utilizador na página "recolher dados" da aplicação.

Antes de começar, faça login na sua app uma vez que irá fazer alterações nas permissões necessárias, e o seu token atual não funcionará. Se ainda não o fez, volte a executar a sua aplicação e selecione **Iniciar sessão** antes de atualizar o código abaixo.

Agora irá atualizar o registo e o código da sua aplicação para puxar o e-mail de um utilizador e exibir as mensagens dentro da app. Para tal, primeiro alargar as permissões de registo de aplicações em Azure AD para permitir o acesso aos dados de e-mail. Em seguida, adicione código à aplicação Blazor para recuperar e exibir estes dados numa das páginas.

1. No portal Azure, selecione a sua aplicação nas **inscrições da App.**
1. Em **Gestão** , selecione **permissões API**.
1. **Selecione Adicionar uma permissão**  >  **Microsoft Graph**.
1. Selecione **Permissões Delegadas** e, em seguida, procure e selecione a permissão **Mail.Read.**
1. **Selecione Permissões de adicionar**.

No *appsettings.jsno* ficheiro, atualize o seu código para que ele pegue o token apropriado com as permissões certas. Adicione "mail.read" depois do âmbito "user.read" em "DownstreamAPI". Isto especifica quais os âmbitos (ou permissões) a que a aplicação solicitará acesso.

```json
"Scopes": "user.read mail.read"
```

Em seguida, atualize o código no ficheiro *FetchData.razor* para recuperar dados de e-mail em vez dos detalhes meteorológicos predefinidos (aleatórios). Substitua o código no ficheiro pelo seguinte:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Iniciar a aplicação. Vais reparar que foste solicitado para as novas permissões, indicando que está tudo a funcionar como esperado. Agora, para além dos dados básicos do perfil do utilizador, a aplicação está a solicitar acesso aos dados de email.

Depois de conceder o consentimento, navegue na página "Recolher dados" para ler algum e-mail.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Screenshot da aplicação final. Tem um título que diz Olá Nicholas e mostra uma lista de e-mails pertencentes ao Nicholas.":::

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar para a construção de aplicativos web que assinam nos utilizadores na nossa série de cenários multi-partes:

> [!div class="nextstepaction"]
> [Cenário: aplicação web que assina nos utilizadores](scenario-web-app-sign-user-overview.md)

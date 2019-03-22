---
title: Guia para a autenticação de clientes do serviço Azure SignalR
description: Neste guia, irá aprender a autenticar os clientes do serviço Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 7660e1405598676599cab30467d22ac979438deb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003694"
---
# <a name="azure-signalr-service-authentication"></a>Autenticação de serviço SignalR do Azure

Este tutorial baseia-se na aplicação de sala de chat introduzida no início rápido. Se não tiver concluído [Create a chat room with SignalR Service](signalr-quickstart-dotnet-core.md) (Criar uma sala de chat com o SignalR Service), conclua primeiro esse exercício.

Neste tutorial, vai aprender a implementar a sua própria autenticação e a integrá-la no Microsoft Azure SignalR Service.

A autenticação utilizada inicialmente na aplicação de sala de chat do início rápido é demasiado simples para cenários do mundo real. A aplicação permite que cada cliente afirme a sua identidade e o servidor aceita-a simplesmente. Esta abordagem não é muito útil para aplicações do mundo real em que um utilizador não autorizado representaria outros utilizadores para aceder a dados confidenciais.

O [GitHub](https://github.com/) disponibiliza APIs de autenticação baseadas num popular protocolo padrão da indústria, chamado [OAuth](https://oauth.net/). Essas APIs permitem que aplicações de terceiros autentiquem contas do GitHub. Neste tutorial, vai utilizar essas APIs para implementar a autenticação através de uma conta do GitHub antes de permitir inícios de sessão de clientes na aplicação de sala de chat. Após a autenticação de uma conta do GitHub, as informações da conta serão adicionadas como um cookie e utilizadas pelo cliente Web para a autenticação.

Para obter mais informações sobre as APIs de autenticação OAuth disponibilizadas através do GitHub, veja [Basics of Authentication](https://developer.github.com/v3/guides/basics-of-authentication/) (Noções Básicas da Autenticação).

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção, disponível nas plataformas Windows, macOS e Linux.

O código deste tutorial está disponível para transferência no [repositório do GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![OAuth concluída alojada no Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Registar uma aplicação de OAuth nova na sua conta do GitHub
> * Adicionar um controlador de autenticação para suportar a autenticação do GitHub
> * Implementar a aplicação Web ASP.NET Core no Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter os seguintes pré-requisitos:

* Uma conta criada no [GitHub](https://github.com/)
* [Git](https://git-scm.com/)
* [SDK .NET Core](https://www.microsoft.com/net/download/windows)
* [O Azure Cloud Shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Transfira ou clone a [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) repositório do GitHub.

## <a name="create-an-oauth-app"></a>Criar uma aplicação de OAuth

1. Abra um browser, navegue para `https://github.com` e inicie sessão na sua conta.

2. Na sua conta, navegue para **Settings** (Definições)  > **Developer settings** (Definições do programador) e clique em **Register a new application** (Registar uma aplicação nova) ou em **New OAuth App** (Aplicação de OAuth nova), em *OAuth Apps* (Aplicações de OAuth).

3. Utilize as seguintes definições para a aplicação de OAuth nova e clique em **Register application** (Registar aplicação):

    | Nome da Definição | Valor sugerido | Descrição |
    | ------------ | --------------- | ----------- |
    | Nome da aplicação | *Chat to Azure SignalR* | O utilizador do GitHub deve ser capaz de reconhecer e confiar no aplicativo que se estão a autenticar.   |
    | URL da home page | `http://localhost:5000/home` | |
    | Descrição da aplicação | *Um exemplo de sala de bate-papo com o serviço Azure SignalR com a autenticação do GitHub* | Uma descrição útil da aplicação que vai ajudar os utilizadores da aplicação a compreender o contexto da autenticação que está a ser utilizada. |
    | URL da chamada de retorno da autorização | `http://localhost:5000/signin-github` | Esta é a definição mais importante para a sua aplicação de OAuth. É o URL de chamada de retorno que o GitHub devolve ao utilizador após a autenticação com êxito. Neste tutorial, tem de utilizar o URL de chamada de retorno predefinido para o pacote *AspNet.Security.OAuth.GitHub*, */signin-github*.  |

4. Assim que o registo da aplicação de OAuth nova estiver concluído, adicione o *Client ID* (ID de Cliente) e o *Client Secret* (Segredo de Cliente) ao Secret Manager com os seguintes comandos. Substitua *Your_GitHub_Client_Id* (Seu_Id_de_cliente_do_GitHub) *Your_GitHub_Client_Secret* (Seu_Segredo_de_cliente_do_GitHub) pelos valores da sua aplicação de OAuth.

        dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
        dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret

## <a name="implement-the-oauth-flow"></a>Implementar o fluxo de OAuth

### <a name="update-the-startup-class-to-support-github-authentication"></a>Atualizar a classe Startup para suportar a autenticação do GitHub

1. Adicione uma referência aos pacotes *Microsoft.AspNetCore.Authentication.Cookies* e *AspNet.Security.OAuth.GitHub* mais recentes e restaure todos os pacotes.

        dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
        dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
        dotnet restore

1. Abra *Startup.cs* e adicione instruções `using` para os seguintes espaços de nomes:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. Na parte superior da classe `Startup`, adicione constantes às chaves do Secret Manager que armazenam os segredos da aplicação de OAuth do GitHub.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Adicione o seguinte código ao método `ConfigureServices`, para suportar a autenticação na aplicação de OAuth do GitHub:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Adicione o método de programa auxiliar `GetUserCompanyInfoAsync` à classe `Startup`.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Atualize o método `Configure` da classe Startup com a seguinte linha de código e guarde o ficheiro.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Adicionar um controlador de autenticação

Nesta secção, vai implementar uma API `Login` que utiliza a aplicação de OAuth do GitHub para autenticar os clientes. Depois de autenticada, a API adicionará um cookie à resposta do cliente Web antes de redirecioná-lo novamente para a aplicação de chat. Este cookie será depois utilizado para identificar o cliente.

1. Adicione um novo ficheiro de código de controlador ao diretório *chattest\Controllers*. Dê o nome *AuthController.cs* ao ficheiro.

2. Adicione o seguinte código ao controlador de autenticação. Se o diretório do seu projeto não era *chattest*, confirme que atualiza o espaço de nomes:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Guarde as alterações.

### <a name="update-the-hub-class"></a>Atualizar a classe Hub

Por predefinição, quando um cliente Web se tenta ligar ao SignalR Service, a ligação é concedida com base num token de acesso que é fornecido internamente. Este token de acesso não é associado a uma identidade autenticada. Este acesso é, na verdade, um acesso anónimo.

Nesta secção, vai ativar a autenticação real ao adicionar o atributo `Authorize` à classe Hub e atualizar os métodos de Hub para lerem o nome de utilizador a partir da afirmação do utilizador autenticado.

1. Abra *Hub\Chat.cs* e adicione referências a estes espaços de nomes:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Atualize o código do Hub, conforme mostrado abaixo. Este código adiciona o atributo `Authorize` à classe `Chat` e utiliza a identidade do utilizador autenticado nos métodos do Hub. Além disso, é adicionado o método `OnConnectedAsync`, que registará uma mensagem do sistema na sala de chat sempre que um cliente novo se ligar.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Guarde as alterações.

### <a name="update-the-web-client-code"></a>Atualizar o código do cliente Web

1. Abra *wwwroot\index.html* e substitua o código que pede o nome de utilizador pelo código para utilizar o cookie que o controlador de autenticação devolve.

    Remova o seguinte código de *index.html*:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Adicione o seguinte código em vez do código acima para utilizar o cookie:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Imediatamente abaixo da linha de código que adicionou para utilizar o cookie, adicione a seguinte definição à função `appendMessage`:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Atualize as funções `bindConnectionMessage` e `onConnected` com o código seguinte para utilizar `appendMessage`.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. Na parte inferior de *index.html*, atualize o processador de erros de `connection.start()`, conforme mostrado abaixo para pedir ao utilizador que inicie sessão.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Guarde as alterações.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar a aplicação localmente

1. Guarde as alterações a todos os ficheiros.

2. Crie a aplicação com a CLI de .NET Core e execute o seguinte comando na shell de comandos:

        dotnet build

3. Depois de concluir com êxito a compilação, execute o seguinte comando para executar a aplicação Web localmente:

        dotnet run

    Por predefinição, a aplicação será alojada localmente na porta 5000:

        E:\Testing\chattest>dotnet run
        Hosting environment: Production
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.

4. Inicie uma janela do browser e navegue para `http://localhost:5000`. Clique na ligação **here** (aqui), na parte superior, para iniciar sessão com o GitHub.

    ![OAuth concluída alojada no Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    É-lhe pedido que autorize o acesso da aplicação de chat à sua conta do GitHub. Clique no botão **Authorize** (Autorizar).

    ![Autorizar a aplicação de OAuth](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    Será redirecionado novamente para a aplicação de chat e será iniciada sessão com o nome da sua conta do GitHub. Para determinar o nome da sua conta, a aplicação Web autenticou-o com a autenticação nova que adicionou.

    ![Conta identificada](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    Agora que a aplicação de chat faz a autenticação com o GitHub e armazena as informações de autenticação como cookies, deve implementá-la no Azure, para que outros utilizadores se possam autenticar com as respetivas contas e comunicar a partir de outras estações de trabalho.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-app-to-azure"></a>Implementar a aplicação no Azure

Nesta secção, irá utilizar a interface de linha de comandos do Azure (CLI) do Azure Cloud Shell para criar uma nova aplicação web no [App Service do Azure](https://docs.microsoft.com/azure/app-service/) para alojar a sua aplicação ASP.NET no Azure. A aplicação Web será configurada para utilizar a implementação de Git local. A aplicação Web também vai ser configurada com a sua cadeia de ligação do SignalR, os segredos da aplicação de OAuth do GitHub e um utilizador de implementação.

Os passos nesta secção utilizam a extensão *signalr* da CLI do Azure. Execute o seguinte comando para instalar a extensão *signalr* da CLI do Azure:

```azurecli-interactive
az extension add -n signalr
```

Quando criar os seguintes recursos, confirme que utiliza o mesmo grupo de recursos no qual o recurso do SignalR Service reside. Esta abordagem faz com que seja muito mais fácil limpar se, no futuro, quiser remover todos os recursos. Os exemplos mostrados partem do princípio de que utilizou o nome do grupo recomendado nos tutoriais anteriores, *SignalRTestResources*.

### <a name="create-the-web-app-and-plan"></a>Criar a aplicação Web e o plano

Copie o texto dos comandos abaixo e atualize os parâmetros. Cole o script atualizado no Azure Cloud Shell e prima **Enter** para criar um plano do Serviço de Aplicações novo e uma aplicação Web nova.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parâmetro | Descrição |
| -------------------- | --------------- |
| ResourceGroupName | Este nome de grupo de recursos foi sugerido nos tutoriais anteriores. É aconselhável manter todos os recursos dos tutoriais agrupados. Utilize o mesmo grupo de recursos que utilizou nos tutoriais anteriores. |
| WebAppPlan | Introduza um nome exclusivo novo para o Plano do Serviço de Aplicações. |
| WebAppName | Este será o nome da nova aplicação Web e fará parte do URL. Utilize um nome exclusivo. Por exemplo, signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Adicionar as definições da aplicação à aplicação Web

Nesta secção, vai adicionar as definições da aplicação para os seguintes componentes:

* Cadeia de ligação do recurso do SignalR Service
* ID do cliente da aplicação de OAuth do GitHub
* Segredo do cliente da aplicação de OAuth do GitHub

Copie o texto dos comandos abaixo e atualize os parâmetros. Cole o script atualizado no Azure Cloud Shell e prima **Enter** para adicionar as definições da aplicação:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parâmetro | Descrição |
| -------------------- | --------------- |
| GitHubClientId | Atribua a esta variável o Id de Cliente secreto da aplicação de OAuth do GitHub. |
| GitHubClientSecret | Atribua a esta variável a palavra-passe secreta da aplicação de OAuth do GitHub. |
| ResourceGroupName | Atualize esta variável para ser o mesmo nome de grupo de recursos que utilizou na secção anterior. |
| SignalRServiceResource | Atualize esta variável com o nome do recurso do SignalR Service que criou no início rápido. Por exemplo, signalrtestsvc48778624. |
| WebAppName | Atualize esta variável com o nome da nova aplicação Web que criou na secção anterior. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Configurar a aplicação Web para implementação no Git local

No Azure Cloud Shell, cole o seguinte script. Este script cria um nome de utilizador de implementação e uma palavra-passe novos que vão ser utilizados quando implementar o seu código na aplicação Web com o Git. O script também configura a aplicação Web para implementação com um repositório de Git local e devolve o URL de implementação do Git.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parâmetro | Descrição |
| -------------------- | --------------- |
| DeploymentUserName | Escolha um nome de utilizador de implementação novo. |
| DeploymentUserPassword | Escolha uma palavra-passe para o utilizador de implementação novo. |
| ResourceGroupName | Utilize o mesmo nome do grupo de recursos que utilizou na secção anterior. |
| WebAppName | Este será o nome da aplicação Web nova que criou anteriormente. |

Anote o URL de implementação do Git que este comando devolve. Vai utilizar esse URL mais tarde.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Implementar o código na aplicação Web do Azure

Para implementar o código, execute os seguintes comandos numa shell do Git.

1. Navegue para a raiz do diretório do projeto. Se não tiver o projeto inicializado com um repositório do Git, execute os seguintes comandos:

    ```bash
    git init
    ```

2. Adicione um remoto para o URL de implementação do Git que apontou anteriormente:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Coloque todos os ficheiros no repositório inicializado e adicione uma consolidação.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Implemente o código na aplicação Web no Azure.

    ```bash
    git push Azure master
    ```

    É-lhe pedido que se autentique para poder implementar o código no Azure. Introduza o nome de utilizador e a palavra-passe do utilizador de implementação que criou acima.

### <a name="update-the-github-oauth-app"></a>Atualizar a aplicação de OAuth do GitHub

A última coisa que tem de fazer é atualizar o **URL da home page** e o **URL da chamada de retorno da autorização** da aplicação de OAuth do GitHub para apontar para a nova aplicação alojada.

1. Abra [https://github.com](https://github.com) num browser e navegue para **Settings** (Definições)  > **Developer settings** (Definições do programador)  > **Oauth Apps** (Aplicações de OAuth), na sua conta.

2. Clique na aplicação de autenticação e atualize o **URL da home page** e o **URL da chamada de retorno da autorização**, conforme mostrado abaixo:

    | Definição | Exemplo |
    | ------- | ------- |
    | URL da home page | https://signalrtestwebapp22665120.azurewebsites.net/home |
    | URL da chamada de retorno da autorização | https://signalrtestwebapp22665120.azurewebsites.net/signin-github |

3. Navegue para o URL da aplicação Web e teste a aplicação.

    ![OAuth concluída alojada no Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, pode eliminar os recursos do Azure criados neste início rápido, para evitar encargos.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele contidos serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *SignalRTestResources*. No grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

![Eliminar](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e clique em **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou a autenticação com OAuth para proporcionar uma melhor abordagem à autenticação com o Azure SignalR Service. Para saber mais sobre como utilizar o Azure SignalR Service, avance para os exemplos da CLI do Azure para SignalR Service.

> [!div class="nextstepaction"]
> [Exemplos da CLI do Azure SignalR](./signalr-reference-cli.md)
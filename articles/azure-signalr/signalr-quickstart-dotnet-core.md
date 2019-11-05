---
title: Guia de início rápido para aprender a usar o serviço de Signaler do Azure
description: Um início rápido para utilizar o Azure SignalR Service para criar uma sala de chat com aplicações ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 022780f2b37c8bed49c81774d443b69bae41e5e7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476746"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Início rápido: criar uma sala de chat usando o serviço Signalr


O Serviço Azure SignalR é um serviço do Azure que ajuda os programadores a facilmente criar aplicações web com funcionalidades em tempo real. Esse serviço é baseado no [signalr para ASP.NET Core 2,1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), mas também oferece suporte [a signalr para ASP.NET Core 3,0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

Este artigo mostra-lhe como começar a utilizar o Azure SignalR Service. Neste guia de início rápido, você criará um aplicativo de chat usando um aplicativo Web ASP.NET Core MVC. Esta aplicação irá fazer uma ligação ao seu recurso do Azure SignalR Service para ativar as atualizações de conteúdos em tempo real. Você hospedará o aplicativo Web localmente e se conectará a vários clientes de navegador. Cada cliente poderá enviar atualizações de conteúdos para todos os outros clientes. 

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. Uma opção é [Visual Studio Code](https://code.visualstudio.com/), que está disponível nas plataformas Windows, MacOS e Linux.

O código deste tutorial está disponível para transferência no [repositório do GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Além disso, você pode criar os recursos do Azure usados neste guia de início rápido seguindo [criar um script de serviço de signalr](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Instale o [SDK do .NET Core](https://www.microsoft.com/net/download/windows).
* Baixe ou clone o repositório GitHub [AzureSignalR-Sample](https://github.com/aspnet/AzureSignalR-samples) . 

## <a name="create-an-azure-signalr-resource"></a>Criar um recurso do Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Nesta seção, você usa a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um projeto de aplicativo Web ASP.NET Core MVC. A vantagem de usar o CLI do .NET Core sobre o Visual Studio é que ele está disponível nas plataformas Windows, macOS e Linux. 

1. Crie uma pasta para seu projeto. Este guia de início rápido usa a pasta *E:\Testing\chattest*

2. Na nova pasta, execute o seguinte comando para criar o projeto:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Adicionar o Secret Manager ao projeto

Nesta seção, você adicionará a [ferramenta Gerenciador de segredo](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. A ferramenta Gerenciador de segredo armazena dados confidenciais para trabalho de desenvolvimento fora de sua árvore de projeto. Essa abordagem ajuda a evitar o compartilhamento acidental de segredos do aplicativo no código-fonte.

1. Abra o ficheiro *.csproj*. Adicione um elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Além disso, adicione um elemento `UserSecretsId`, conforme mostrado no código a seguir para o *. csproj*mais informativo, e salve o arquivo.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Adicionar o Azure SignalR à aplicação Web

1. Adicione uma referência ao pacote NuGet `Microsoft.Azure.SignalR` executando o seguinte comando:

        dotnet add package Microsoft.Azure.SignalR

2. Execute o seguinte comando para restaurar os pacotes para seu projeto:

        dotnet restore

3. Adicione um segredo denominado *Azure:SignalR:ConnectionString* ao Secret Manager. 

    Este segredo irá conter a cadeia de ligação para aceder ao seu recurso do SignalR Service. *Azure: signalr: ConnectionString* é a chave de configuração padrão que o signalr procura para estabelecer uma conexão. Substitua o valor no comando a seguir pela cadeia de conexão para o recurso de serviço de sinalização.

    Você deve executar esse comando no mesmo diretório que o arquivo *. csproj* .

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    O Gerenciador de segredo será usado somente para testar o aplicativo Web enquanto ele estiver hospedado localmente. Em um tutorial posterior, você implantará o aplicativo Web de chat no Azure. Depois que o aplicativo Web for implantado no Azure, você usará uma configuração de aplicativo em vez de armazenar a cadeia de conexão com o Gerenciador de segredo.

    Esse segredo é acessado com a API de configuração. Dois-pontos (:) funciona no nome da configuração com a API de configuração em todas as plataformas com suporte. Consulte [configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Abra *Startup.cs* e atualize o método de `ConfigureServices` para usar o serviço de signaler do Azure chamando o método de `services.AddSignalR().AddAzureSignalR()` somente para ASP.NET Core 2:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```
    Para ASP.NET Core 3 +, não há nenhuma alteração necessária para o método `ConfigureServices`.

    Ao não passar um parâmetro para `AddAzureSignalR()`, esse código usa a chave de configuração padrão para a cadeia de conexão de recurso do serviço Signalr. A chave de configuração padrão é *Azure: signalr: ConnectionString*.

5. Também em *Startup.cs*, atualize o método `Configure` substituindo a chamada para `app.UseStaticFiles()` pelo código a seguir e salvar o arquivo, somente para ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Para ASP.NET Core 3 +, substitua o código acima por:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Adicionar a classe Hub

No Signalr, um hub é um componente principal que expõe um conjunto de métodos que podem ser chamados do cliente. Nesta secção, vai definir uma classe hub com dois métodos: 

* `Broadcast`: este método transmite uma mensagem para todos os clientes.
* `Echo`: este método envia uma mensagem novamente para o autor da chamada.

Ambos os métodos usam a interface `Clients` que o ASP.NET Core o SDK do Signalr fornece. Essa interface fornece acesso a todos os clientes conectados, para que você possa enviar conteúdo por push para seus clientes.

1. No diretório do projeto, adicione uma pasta nova designada *Hub*. Adicione um ficheiro de código de hub novo, chamado *Chat.cs*, à pasta nova.

2. Adicione o seguinte código a *chat.cs* para definir a classe de Hub e salvar o arquivo. 

    Se tiver utilizado um nome de projeto diferente de *chattest*, atualize o espaço de nomes desta classe.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Adicionar a interface do cliente para o aplicativo Web

A interface do usuário do cliente para este aplicativo sala de chat consistirá em HTML e JavaScript em um arquivo chamado *index. html* no diretório *wwwroot* .

Copie o arquivo *index. html* , a pasta *CSS* e a pasta *scripts* da pasta *wwwroot* do [repositório de exemplos](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Cole-os na pasta *wwwroot* do projeto.

Este é o código principal de *index. html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

O código em *index. html* chama `HubConnectionBuilder.build()` para fazer uma conexão HTTP com o recurso de Signalr do Azure.

Se a ligação for bem-sucedida, é transmitida para `bindConnectionMessage`, que adiciona processadores de eventos para envios de conteúdos de entrada para o cliente. 

`HubConnection.start()` inicia a comunicação com o hub. Em seguida, `onConnected()` adiciona os manipuladores de eventos de botão. Estes processadores utilizam a ligação para permitir que este cliente envie atualizações de conteúdos para todos os clientes ligados.

## <a name="add-a-development-runtime-profile"></a>Adicionar um perfil de tempo de execução de desenvolvimento

Nesta seção, você adicionará um ambiente de tempo de execução de desenvolvimento para ASP.NET Core. Para obter mais informações, consulte [trabalhar com vários ambientes em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Crie uma pasta denominada *Propriedades* em seu projeto.

2. Adicione um novo arquivo chamado *launchSettings. JSON* à pasta, com o conteúdo a seguir e salve o arquivo.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

        dotnet build

2. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

    O aplicativo será hospedado localmente na porta 5000, conforme configurado em nosso perfil de tempo de execução de desenvolvimento:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Abra duas janelas do navegador. Em cada navegador, vá para `http://localhost:5000`. Você será solicitado a inserir seu nome. Insira um nome de cliente para ambos os clientes e teste o conteúdo de mensagem por push entre ambos os clientes usando o botão **Enviar** .

    ![Exemplo de um chat de grupo do Azure Signalr](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Limpar recursos

Se você continuar no próximo tutorial, poderá manter os recursos criados neste guia de início rápido e reutilizá-los.

Se você tiver concluído o aplicativo de exemplo de início rápido, poderá excluir os recursos do Azure criados neste guia de início rápido para evitar cobranças. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível e inclui todos os recursos nesse grupo. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se você criou os recursos para hospedar esse exemplo em um grupo de recursos existente que contém os recursos que deseja manter, poderá excluir cada recurso individualmente de sua folha em vez de excluir o grupo de recursos.
> 
> 

Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

Na caixa de texto **Filtrar por nome** , digite o nome do seu grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. Em seu grupo de recursos na lista de resultados, selecione as reticências ( **...** ) > **excluir grupo de recursos**.

   
![Seleções para excluir um grupo de recursos](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


É-lhe pedido que confirme a eliminação do grupo de recursos. Insira o nome do grupo de recursos a ser confirmado e selecione **excluir**.
   
Após alguns instantes, o grupo de recursos e todos os respetivos recursos são eliminados.



## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo recurso de serviço de Signaler do Azure. Em seguida, você o usou com um aplicativo Web ASP.NET Core para enviar atualizações de conteúdo por push em tempo real para vários clientes conectados. Para saber mais sobre como usar o serviço de Signaler do Azure, prossiga para o tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Azure SignalR Service authentication](./signalr-concept-authenticate-oauth.md) (Autenticação do Azure SignalR Service)



---
title: Quickstart para aprender a usar o Serviço De Sinalização Azure
description: Um início rápido para utilizar o Azure SignalR Service para criar uma sala de chat com aplicações ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: f87625fe4f56b369f2bf4aade3ef5424084b6fe8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81254891"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Quickstart: Criar uma sala de chat utilizando o Serviço SignalR


O Serviço Azure SignalR é um serviço do Azure que ajuda os programadores a facilmente criar aplicações web com funcionalidades em tempo real. Este serviço baseia-se no [SignalR para ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), mas também suporta [o SignalR para ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

Este artigo mostra-lhe como começar a utilizar o Azure SignalR Service. Neste arranque rápido, você vai criar uma aplicação de chat usando uma ASP.NET aplicação web Core MVC. Esta aplicação irá fazer uma ligação ao seu recurso do Azure SignalR Service para ativar as atualizações de conteúdos em tempo real. Você irá hospedar a aplicação web localmente e conectar-se com vários clientes de navegador. Cada cliente poderá enviar atualizações de conteúdos para todos os outros clientes. 

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. Uma opção é [o Visual Studio Code](https://code.visualstudio.com/), que está disponível nas plataformas Windows, macOS e Linux.

O código deste tutorial está disponível para transferência no [repositório do GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Além disso, pode criar os recursos Azure utilizados neste quickstart seguindo o [script Create a SignalR Service](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Instale o [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Descarregue ou clone o repositório GitHub [da amostra AzureSignalR.](https://github.com/aspnet/AzureSignalR-samples) 

## <a name="create-an-azure-signalr-resource"></a>Criar um recurso do Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Nesta secção, utiliza a interface de [linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um ASP.NET projeto da aplicação web Core MVC. A vantagem de utilizar o .NET Core CLI sobre o Visual Studio é que está disponível nas plataformas Windows, macOS e Linux. 

1. Crie uma pasta para o seu projeto. Este quickstart utiliza a pasta *E:\Testing\chattest.*

2. Na nova pasta, execute o seguinte comando para criar o projeto:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Adicionar o Secret Manager ao projeto

Nesta secção, você adicionará a [ferramenta Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. A ferramenta Secret Manager armazena dados confidenciais para trabalhos de desenvolvimento fora da árvore do seu projeto. Esta abordagem ajuda a prevenir a partilha acidental de segredos de aplicações no código fonte.

1. Abra o ficheiro *.csproj*. Adicione um elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Adicione também `UserSecretsId` um elemento como mostrado no seguinte código para *chattest.csproj*, e guarde o ficheiro.

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

1. Adicione uma referência `Microsoft.Azure.SignalR` ao pacote NuGet executando o seguinte comando:

        dotnet add package Microsoft.Azure.SignalR

2. Execute o seguinte comando para restaurar as embalagens para o seu projeto:

        dotnet restore

3. Adicione um segredo denominado *Azure:SignalR:ConnectionString* ao Secret Manager. 

    Este segredo irá conter a cadeia de ligação para aceder ao seu recurso do SignalR Service. *Azure:SignalR:ConnectionString* é a chave de configuração predefinida que o SignalR procura para estabelecer uma ligação. Substitua o valor no seguinte comando com a cadeia de ligação para o seu recurso SignalR Service.

    Deve executar este comando no mesmo diretório que o ficheiro *.csproj.*

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    O Secret Manager será utilizado apenas para testar a aplicação web enquanto estiver hospedada localmente. Num tutorial posterior, irá si colocar a aplicação web de chat no Azure. Depois de a aplicação web ser implantada para o Azure, utilizará uma definição de aplicação em vez de armazenar a cadeia de ligação com o Secret Manager.

    Este segredo é acedido com a API de Configuração. Um cólon (:) trabalha no nome de configuração com a API de configuração em todas as plataformas suportadas. Ver [Configuração por ambiente.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0) 


4. Abra *Startup.cs* e atualize o método `ConfigureServices` para utilizar o Azure SignalR ao chamar o método `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Ao não passar um `AddAzureSignalR()`parâmetro para , este código utiliza a chave de configuração predefinida para a cadeia de ligação de recursos do Serviço SignalR. A tecla de configuração predefinida é *Azure:SignalR:ConnectionString*.

5. Também em *Startup.cs* `Configure` , atualize o `app.UseStaticFiles()` método substituindo a chamada pelo seguinte código e guarde o ficheiro, apenas para ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Para ASP.NET Core 3+, substitua o código acima com:

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

No SignalR, um hub é um componente central que expõe um conjunto de métodos que podem ser chamados do cliente. Nesta secção, vai definir uma classe hub com dois métodos: 

* `Broadcast`: este método transmite uma mensagem para todos os clientes.
* `Echo`: este método envia uma mensagem novamente para o autor da chamada.

Ambos os `Clients` métodos utilizam a interface que o ASP.NET Core SignalR SDK fornece. Esta interface dá-lhe acesso a todos os clientes conectados, para que possa empurrar conteúdo para os seus clientes.

1. No diretório do projeto, adicione uma pasta nova designada *Hub*. Adicione um ficheiro de código de hub novo, chamado *Chat.cs*, à pasta nova.

2. Adicione o seguinte código a *Chat.cs* para definir a sua classe hub e guardar o ficheiro. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>Adicione a interface do cliente para a aplicação web

A interface de utilizador do cliente para esta aplicação de chat room será composta por HTML e JavaScript num ficheiro chamado *index.html* no diretório *wwwroot.*

Copie o ficheiro *index.html,* a pasta *css* e a pasta de *scripts* da pasta *wwwroot* do [repositório](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot)de amostras . Cola-os na pasta *wwwroot* do seu projeto.

Aqui está o código principal do *índice.html:* 

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

O código em *index.html* chama `HubConnectionBuilder.build()` para fazer uma ligação HTTP ao recurso Azure SignalR.

Se a ligação for bem-sucedida, é transmitida para `bindConnectionMessage`, que adiciona processadores de eventos para envios de conteúdos de entrada para o cliente. 

`HubConnection.start()` inicia a comunicação com o hub. Em `onConnected()` seguida, adiciona os manipuladores de eventos de botão. Estes processadores utilizam a ligação para permitir que este cliente envie atualizações de conteúdos para todos os clientes ligados.

## <a name="add-a-development-runtime-profile"></a>Adicionar um perfil de tempo de execução de desenvolvimento

Nesta secção, você adicionará um ambiente de desenvolvimento para ASP.NET Core. Para mais informações, consulte [Trabalhar com vários ambientes em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Crie uma pasta chamada *Properties* no seu projeto.

2. Adicione um novo ficheiro chamado *launchSettings.json* à pasta, com o seguinte conteúdo, e guarde o ficheiro.

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


## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Para construir a aplicação utilizando o CLI .NET Core, execute o seguinte comando na concha de comando:

        dotnet build

2. Depois da construção terminar com sucesso, executar o seguinte comando para executar a aplicação web localmente:

        dotnet run

    A aplicação será hospedada localmente no porto 5000, conforme configurado no nosso perfil de tempo de execução de desenvolvimento:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Abra duas janelas do navegador. Em cada navegador, `http://localhost:5000`vá para . Foi-lhe pedido que insira o seu nome. Introduza um nome de cliente para ambos os clientes e teste empurrando o conteúdo da mensagem entre ambos os clientes utilizando o botão **Enviar.**

    ![Exemplo de um chat de grupo Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Limpar recursos

Se continuar para o próximo tutorial, pode manter os recursos criados neste arranque rápido e reutilizá-los.

Se terminar com a aplicação de amostra sintetizadora, pode eliminar os recursos Azure criados neste arranque rápido para evitar encargos. 

> [!IMPORTANT]
> A deslocação de um grupo de recursos é irreversível e inclui todos os recursos desse grupo. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se criou os recursos para hospedar esta amostra num grupo de recursos existente que contém recursos que pretende manter, pode eliminar cada recurso individualmente da sua lâmina em vez de eliminar o grupo de recursos.
> 
> 

Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

No **Filtro por caixa** de texto, digite o nome do seu grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. No seu grupo de recursos na lista de resultados, selecione a elipse (**...**) > **Eliminar o grupo de recursos**.

   
![Seleções para a desalocação de um grupo de recursos](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


É-lhe pedido que confirme a eliminação do grupo de recursos. Insira o nome do seu grupo de recursos para confirmar e selecione **Eliminar**.
   
Após alguns instantes, o grupo de recursos e todos os respetivos recursos são eliminados.



## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um novo recurso do Serviço De Sinalização Azure. Em seguida, usou-a com uma ASP.NET aplicação web Core para empurrar atualizações de conteúdo em tempo real para vários clientes conectados. Para saber mais sobre a utilização do Serviço De Sinalização Azure, continue até ao tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Azure SignalR Service authentication](./signalr-concept-authenticate-oauth.md) (Autenticação do Azure SignalR Service)



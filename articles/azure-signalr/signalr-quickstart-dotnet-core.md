---
title: Arranque rápido para aprender a usar o Serviço Azure SignalR
description: Um início rápido para utilizar o Azure SignalR Service para criar uma sala de chat com aplicações ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 4665666fe56c208b2437a7051bbf9201383365f8
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962140"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Quickstart: Criar uma sala de chat utilizando o Serviço SignalR


O Serviço Azure SignalR é um serviço do Azure que ajuda os programadores a facilmente criar aplicações web com funcionalidades em tempo real. Este serviço baseia-se no [SignalR para ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), mas também suporta [o SignalR para ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

Este artigo mostra-lhe como começar a utilizar o Azure SignalR Service. Neste arranque rápido, irá criar uma aplicação de chat utilizando uma aplicação web core MVC ASP.NET. Esta aplicação irá fazer uma ligação ao seu recurso do Azure SignalR Service para ativar as atualizações de conteúdos em tempo real. Você irá hospedar a aplicação web localmente e conectar-se com vários clientes de navegador. Cada cliente poderá enviar atualizações de conteúdos para todos os outros clientes. 

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. Uma opção é [o Visual Studio Code](https://code.visualstudio.com/), que está disponível nas plataformas Windows, macOS e Linux.

O código deste tutorial está disponível para transferência no [repositório do GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Além disso, pode criar os recursos Azure utilizados neste arranque rápido seguindo o [script Criar um script de Serviço SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Instale o [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Faça o download ou clone do repositório GitHub [da amostra AzureSignalR.](https://github.com/aspnet/AzureSignalR-samples) 

## <a name="create-an-azure-signalr-resource"></a>Criar um recurso do Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Nesta secção, utiliza-se a [interface de linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um projeto de aplicação web Core MVC ASP.NET. A vantagem de utilizar o .NET Core CLI sobre o Visual Studio é que está disponível nas plataformas Windows, macOS e Linux. 

1. Crie uma pasta para o seu projeto. Este arranque rápido utiliza a pasta *E:\Testing\chattest.*

2. Na nova pasta, executar o seguinte comando para criar o projeto:

    ```dotnetcli
    dotnet new mvc
    ```


## <a name="add-secret-manager-to-the-project"></a>Adicionar o Secret Manager ao projeto

Nesta secção, irá adicionar a [ferramenta Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. A ferramenta Secret Manager armazena dados sensíveis para trabalhos de desenvolvimento fora da sua árvore do projeto. Esta abordagem ajuda a prevenir a partilha acidental de segredos de aplicações em código fonte.

1. Abra o ficheiro *.csproj*. Adicione um elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Adicione também um `UserSecretsId` elemento como mostrado no seguinte código para *chattest.csproj*, e guarde o ficheiro.

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

1. Adicione uma referência ao `Microsoft.Azure.SignalR` pacote NuGet executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Executar o seguinte comando para restaurar pacotes para o seu projeto:

    ```dotnetcli
    dotnet restore
    ```

3. Adicione um segredo denominado *Azure:SignalR:ConnectionString* ao Secret Manager. 

    Este segredo irá conter a cadeia de ligação para aceder ao seu recurso do SignalR Service. *Azure:SignalR:ConnectionString* é a chave de configuração padrão que o SignalR procura para estabelecer uma ligação. Substitua o valor no seguinte comando com a cadeia de ligação para o seu recurso SignalR Service.

    Tem de executar este comando no mesmo diretório que o ficheiro *.csproj.*

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    O Secret Manager será usado apenas para testar a aplicação web enquanto estiver hospedado localmente. Num tutorial posterior, irá implementar a aplicação de chat web para o Azure. Depois de a aplicação web ser implantada no Azure, utilizará uma definição de aplicação em vez de armazenar a cadeia de ligação com o Secret Manager.

    Este segredo é acedido com a API de Configuração. Um cólon (:) funciona no nome de configuração com a API de configuração em todas as plataformas suportadas. Ver [Configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Abra *Startup.cs* e atualize o método `ConfigureServices` para utilizar o Azure SignalR ao chamar o método `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Ao não passar um parâmetro para `AddAzureSignalR()` , este código utiliza a chave de configuração predefinida para a cadeia de ligação de recursos SignalR Service. A chave de configuração predefinida é *Azure:SignalR:ConnectionString*.

5. Também em *Startup.cs*, atualize o `Configure` método substituindo a chamada pelo seguinte código `app.UseStaticFiles()` e guarde o ficheiro, apenas para ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Para ASP.NET Core 3+, substitua o código acima por:

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

Ambos os métodos utilizam a `Clients` interface que o ASP.NET Core SignalR SDK fornece. Esta interface dá-lhe acesso a todos os clientes conectados, para que possa empurrar o conteúdo para os seus clientes.

1. No diretório do projeto, adicione uma pasta nova designada *Hub*. Adicione um ficheiro de código de hub novo, chamado *Chat.cs*, à pasta nova.

2. Adicione o seguinte código à *Chat.cs* para definir a classe do hub e guardar o ficheiro. 

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

A interface de utilizador do cliente para esta aplicação de sala de chat será composta por HTML e JavaScript num ficheiro nomeado *index.html* no *diretório wwwroot.*

Copie o ficheiro *index.html,* a pasta *css* e a pasta *scripts* da pasta *wwwroot* do [repositório](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot)de amostras . Cole-as na pasta *wwwroot* do seu projeto.

Aqui está o código principal de *index.html:* 

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

O código em *index.html* chama para fazer `HubConnectionBuilder.build()` uma ligação HTTP ao recurso Azure SignalR.

Se a ligação for bem-sucedida, é transmitida para `bindConnectionMessage`, que adiciona processadores de eventos para envios de conteúdos de entrada para o cliente. 

`HubConnection.start()` inicia a comunicação com o hub. Em seguida, `onConnected()` adiciona os manipuladores de eventos de botão. Estes processadores utilizam a ligação para permitir que este cliente envie atualizações de conteúdos para todos os clientes ligados.

## <a name="add-a-development-runtime-profile"></a>Adicionar um perfil de tempo de execução de desenvolvimento

Nesta secção, você adicionará um ambiente de desenvolvimento para ASP.NET Core. Para obter mais informações, consulte [Trabalhar com vários ambientes em ASP.NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/environments)

1. Crie uma pasta chamada *Propriedades* no seu projeto.

2. Adicione um novo ficheiro nomeado *launchSettings.jsna* pasta, com o seguinte conteúdo, e guarde o ficheiro.

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


## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Para construir a aplicação utilizando o .NET Core CLI, executar o seguinte comando na concha de comando:

    ```dotnetcli
    dotnet build
    ```

2. Após a construção terminar com sucesso, executar o seguinte comando para executar a aplicação web localmente:

    ```dotnetcli
    dotnet run
    ```

    A aplicação será hospedada localmente na porta 5000, conforme configurado no nosso perfil de execução de desenvolvimento:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Development
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.    
    ```

3. Abra duas janelas do navegador. Em cada navegador, vá a `http://localhost:5000` . Foi solicitado que insira o seu nome. Insira o nome do cliente para clientes e teste o conteúdo de mensagem de pressão entre ambos os clientes utilizando o botão **Enviar.**

    ![Exemplo de uma conversa do grupo Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Limpar recursos

Se continuar para o próximo tutorial, pode manter os recursos criados neste quickstart e reutilizá-los.

Se terminar com a aplicação de amostra de arranque rápido, pode eliminar os recursos Azure criados neste arranque rápido para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e inclui todos os recursos desse grupo. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se criou os recursos para hospedar esta amostra num grupo de recursos existente que contém recursos que pretende guardar, pode eliminar cada recurso individualmente da sua lâmina em vez de eliminar o grupo de recursos.
> 
> 

Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

Na caixa de texto **Filter por nome,** digite o nome do seu grupo de recursos. As instruções neste início rápido utilizaram um grupo de recursos denominado *SignalRTestResources*. No seu grupo de recursos na lista de resultados, selecione a elipse **(...**) > **Eliminar o grupo de recursos**.

   
![Seleções para eliminar um grupo de recursos](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


É-lhe pedido que confirme a eliminação do grupo de recursos. Introduza o nome do seu grupo de recursos para confirmar e selecione **Delete**.
   
Após alguns instantes, o grupo de recursos e todos os respetivos recursos são eliminados.



## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um novo recurso do Serviço Azure SignalR. Em seguida, usou-a com uma aplicação web Core ASP.NET para empurrar atualizações de conteúdo em tempo real para vários clientes conectados. Para saber mais sobre a utilização do Serviço Azure SignalR, continue ao tutorial que demonstre autenticação.

> [!div class="nextstepaction"]
> [Azure SignalR Service authentication](./signalr-concept-authenticate-oauth.md) (Autenticação do Azure SignalR Service)


